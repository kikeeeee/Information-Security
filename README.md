# Commento riga per riga di `server.js`

> Versione **estremamente dettagliata**: ogni istruzione è spiegata, con note su sicurezza, alternative in produzione e suggerimenti di estensione.

---

## Convenzioni usate

- Ogni “§” indica una **sezione logica** del codice (la trovi anche come commento nel file sorgente).  
- Le righe di codice sono mostrate in blocchi **monoriga** o in brevi snippet, seguite da una spiegazione approfondita.  
- Le *Note ➜* evidenziano accorgimenti o possibili refactor.

---

## § 0 · Caricamento variabili d’ambiente

```js
require('dotenv').config();
```

**Cosa fa:** importa e invoca la funzione `config()` di `dotenv`, che legge il file `.env` nella directory corrente e popola `process.env` con le coppie chiave/valore trovate.

**Perché è _PRIMA di tutto_:** se un modulo caricato dopo avesse bisogno di `process.env`, vedrebbe valori `undefined` se questo require fosse posto più in basso.

*Note ➜*  
- In ambienti **serverless** (Vercel, Netlify) le variabili sono esposte dal provider; `dotenv` viene ignorato se il file `.env` non esiste.  
- Puoi specificare un percorso diverso con `dotenv.config({ path: '/custom/.env' })`.

---

## § 1 · Import di librerie e moduli core

```js
const express       = require('express');
const session       = require('express-session');
const passport      = require('passport');
const GoogleStrategy= require('passport-google-oauth20').Strategy;
const GitHubStrategy= require('passport-github2').Strategy;
const jwt           = require('jsonwebtoken');
const crypto        = require('crypto');
```

| Modulo | Ruolo |
|--------|-------|
| **express** | Framework HTTP minimale; gestisce routing, middleware e server. |
| **express-session** | Middleware che genera un cookie `connect.sid` e gestisce la serializzazione della sessione lato server. |
| **passport** | Layer di autenticazione “agnostico”; supporta strategie pluggable. |
| **passport-google-oauth20** | Implementa il flusso OAuth 2 “Authorization Code” con Google. |
| **passport-github2** | Idem ma per GitHub. |
| **jsonwebtoken** | Firma/verifica JSON Web Token (RFC 7519). |
| **crypto** | Modulo core Node: usiamo `crypto.randomUUID()` per generare ID univoci e sicuri. |

*Note ➜*  
- Le strategie `google-oauth20` e `github2` **richiedono** che le callback HTTPS siano inserite nella console dei provider.  
- `jwt` v9+ supporta **promises** (`jwt.signAsync`, `jwt.verifyAsync`): potresti modernizzare il codice in futuro.

---

## § 2 · Log diagnostico delle variabili

```js
console.log('🔍 Verifica configurazione:');
console.log('GOOGLE_CLIENT_ID:',     process.env.GOOGLE_CLIENT_ID ? '✅ PRESENTE' : '❌ MANCANTE');
…
```

**Scopo:** fornire feedback immediato in console quando l’app parte, mostrando se le 4 credenziali cruciali (`ID` + `SECRET` per entrambi i provider) sono effettivamente caricate.  

*Note ➜*  
- Evita di stampare **valori** segreti completi; qui mostriamo solo un flag “presente/mancante”.  
- In produzione potresti spedire questi log a un sistema di APM (Datadog, Grafana Loki, ecc.).

---

## § 3 · Creazione dell’app e porta di ascolto

```js
const app = express();
const PORT = process.env.PORT || 3000;
```

- `express()` restituisce un’applicazione che, dietro le quinte, è una funzione con signature `(req,res,next)`.  
- `PORT` legge la variabile d’ambiente (utile per piattaforme PaaS che assegnano porte dinamiche) e cade su `3000` in locale.

---

## § 4 · Oggetto `config` centralizzato

Snippet abbreviato:

```js
const config = {
  google : { clientID, clientSecret, callbackURL:'/auth/google/callback' },
  github : { clientID, clientSecret, callbackURL:'/auth/github/callback' },
  jwt    : { secret, expiresIn:'24h' },
  session: { secret }
};
```

**Vantaggi:**

1. **Unica fonte di verità:** il resto del codice non deve ricordarsi nomi di env vars.  
2. **Fallback di sviluppo:** se manca la variabile, inserisce una stringa placeholder (_utile per test unitari senza .env_).  
3. **Autocompletamento IDE:** accedere a `config.google.clientID` offre typing in TypeScript o intellisense.

*Note ➜*  
- In produzione dovresti **saltare** l’avvio se i placeholder rimangono; ad esempio lanciare `process.exit(1)`.

---

## § 5 · Middleware Express (parsing + sessione)

### Parsing del body

```js
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
```
- `json()` decodifica `Content‑Type: application/json`.  
- `urlencoded()` decodifica form POST (HTML `<form>`), con `extended:true` che usa la libreria qs per oggetti nidificati.

### Sessione

```js
app.use(session({
  secret: config.session.secret,
  resave: false,
  saveUninitialized: false,
  cookie: { secure: process.env.NODE_ENV === 'production' }
}));
```

| Opzione | Significato |
|---------|-------------|
| **secret** | Chiave usata per firmare l’ID di sessione nel cookie. |
| **resave:false** | Evita di salvare la sessione se non è stata modificata (riduce I/O). |
| **saveUninitialized:false** | Non crea sessioni “vuote” per visitatori anonimi (GDPR‑friendly). |
| **cookie.secure** | True ➔ il cookie si invia **solo** su HTTPS (must‑have in prod). |

*Note ➜*  
- Lo store predefinito è in‑memory (`MemoryStore`): **mai** usarlo in cluster/production; sostituiscilo con Redis, DynamoDB, ecc.

### Inizializzazione Passport

```js
app.use(passport.initialize()); // attacca req.login, req.isAuthenticated, …
app.use(passport.session());    // collega Passport alla sessione Express
```

---

## § 6 · “Database” fittizio

```js
const users = new Map();
```

- **Key** = `id` (UUID).  
- **Value** = object con info profilo.  

*Limitazione:* svanisce al riavvio; perfetto per demo/docker ephemeral, non per produzione.

---

## § 7 · Serializzazione / deserializzazione Passport

```js
passport.serializeUser((user, done) => done(null, user.id));
passport.deserializeUser((id, done)   => done(null, users.get(id)));
```

| Fase | Che succede |
|------|-------------|
| `serializeUser` | Dopo il login, Passport scrive `user.id` dentro il cookie di sessione. |
| `deserializeUser` | Ad ogni request legge l’id, cerca l’utente e lo assegna a `req.user`. |

*Note ➜*  
- Se migrerai a un DB, implementa `deserializeUser` con `await db.users.findById(id)`.

---

## § 8 · Strategia **Google** OAuth 2

Configurazione:

```js
new GoogleStrategy({
  clientID    : config.google.clientID,
  clientSecret: config.google.clientSecret,
  callbackURL : config.google.callbackURL,
  scope       : ['profile','email']
}, async (accessToken, refreshToken, profile, done) => { … })
```

**Callback params:**

| Parametro | Contenuto |
|-----------|-----------|
| `accessToken`  | Token OAuth2 Google (scade in ~1h) |
| `refreshToken` | **Solo alla prima autorizzazione** (se scope offline) |
| `profile`      | Dati OpenID Connect (id, displayName, emails[], photos[]) |

Flusso logico interno:

1. Cerca nella `Map` un utente con `googleId === profile.id`.  
2. Se lo trova ➔ aggiorna `lastLogin`, `accessToken`, `done(null, user)`.  
3. Se non esiste ➔ costruisce `newUser = { id: crypto.randomUUID(), googleId, … }` e `users.set(id, newUser)`.  
4. Qualsiasi eccezione ➔ `done(err,null)` (Passport salta al failureRedirect).

*Note ➜*  
- È **qui** che potresti salvare l’utente in MongoDB/SQL o fare provisioning di risorse.  
- La libreria Google restituisce **thumbnail** 96×96; puoi chiedere foto HD aggiungendo query `sz=200` all’URL.

---

## § 9 · Strategia **GitHub** OAuth 2

Quasi identica, ma:

- scope `['user:email']` (GitHub necessita di un’ulteriore chiamata API se l’email è privata).  
- Usa `profile.displayName || profile.username` in quanto molti user GitHub non impostano il nome reale.

---

## § 10 · Middleware **authenticateJWT**

```js
const authenticateJWT = (req,res,next) => {
  const authHeader = req.headers.authorization;      // "Bearer <token>"
  if (!authHeader) return res.status(401).json({ error:'Token richiesto' });

  const token = authHeader.split(' ')[1];
  jwt.verify(token, config.jwt.secret, (err, payload) => {
    if (err) return res.status(403).json({ error:'Token non valido' });
    req.user = payload;                              // payload = { id,email,name,provider,iat,exp }
    next();
  });
}
```

- Protegge rotte “stateless” (nessuna dipendenza da sessione).  
- `401` se manca header; `403` se firma errata o token scaduto.

*Note ➜*  
- Puoi estrarre `(err, payload)` con promises:  
  ```js
  try { req.user = await jwt.verify(token, secret); next(); }
  catch(e){ res.status(403).json({error:'…'}); }
  ```

---

## § 11 · Middleware **ensureAuthenticated**

```js
const ensureAuthenticated = (req,res,next) =>
  req.isAuthenticated() ? next() : res.status(401).json({ error:'Accesso non autorizzato' });
```

- Usa l’helper di Passport per verificare che la sessione contenga un utente serializzato.

---

## § 12 · Rotta **/** (Homepage demo)

Costruisce HTML inline:

- Se `req.user` **esiste** ➔ benvenuto, avatar, dettagli e link `/logout`.  
- Altrimenti ➔ due pulsanti “Login con Google”/“Login con GitHub”.

*Note ➜*  
- In produzione converrà servire una vera **SPA** o pagine statiche separate da Express.

---

## § 13 · Rotte OAuth 2

### 1 · Avvio login

```js
app.get('/auth/google', passport.authenticate('google',{ scope:['profile','email'] }));
app.get('/auth/github', passport.authenticate('github',{ scope:['user:email'] }));
```

Rimanda subito il browser al provider, indicando Scope e `redirect_uri` (quella definita in console).

### 2 · Callback Google

```js
app.get('/auth/google/callback',
  passport.authenticate('google',{ failureRedirect:'/login?error=google' }),
  (req,res)=>{ … }
);
```

Dopo il successo:

1. Emette `jwt.sign(payload, secret,{ expiresIn:'24h' })`.  
2. Salva il token in `req.session.jwtToken` (così `/api/token` lo può restituire).  
3. `redirect('/')`.

### 3 · Callback GitHub

Identico al precedente, con failure `?error=github`.

---

## § 14 · Pagina **/login** di errore

Legge `req.query.error` e genera un messaggio diverso per Google vs GitHub; profila due link di retry e uno “Torna alla Home”.

---

## § 15 · API REST

### `/api/profile` (via sessione)

```js
app.get('/api/profile', ensureAuthenticated, (req,res)=>{ … });
```

Restituisce un JSON con i campi principali dell’utente salvato nella Map.

### `/api/token`

Restituisce `{ token:req.session.jwtToken, expiresIn:'24h' }`.

### `/api/protected`

```js
app.get('/api/protected', authenticateJWT, (req,res)=>{ … });
```

Esempio di endpoint che **richiede** il token Bearer: se tutto ok, risponde con il payload del token stesso.

---

## § 16 · Logout

```js
app.get('/logout',(req,res)=>{
  req.logout(err=>{ … });
  req.session.destroy();   // rimuove cookie lato client
  res.redirect('/');
});
```

- `req.logout` è un helper di Passport che rimuove `req.user`.  
- **Importante:** chiamare `req.session.destroy()` chiude anche il cookie.

---

## § 17 · Error Handler generale

```js
app.use((err,req,res,next)=>{
  console.error(err.stack);
  res.status(500).json({ error:'Errore interno del server' });
});
```

Qualsiasi middleware che passa `next(err)` fa atterrare l’errore qui.

---

## § 18 · Avvio server

```js
app.listen(PORT, ()=>{ 
  console.log(`Server in esecuzione su http://localhost:${PORT}`);
  console.log(`- Google: ${config.google.clientID ? 'Configurato' : 'Non configurato'}`);
  console.log(`- GitHub: ${config.github.clientID ? 'Configurato' : 'Non configurato'}`);
});
```

Anche qui evitiamo di loggare secrets; mostra solo se le API key sono settate.

---

## § 19 · `module.exports = app`

Rende l’istanza Express esportabile (utile per testate con Jest/Supertest).

---

### TODO / Miglioramenti futuri

- **Store di sessione** persistente (Redis) per cluster e scalabilità.  
- **CSRF** protection per rotte mutative.  
- **Refresh token**: salvare `refreshToken` di Google per rinnovi silenziosi.  
- **Helmet**, **RateLimiter** e **CORS** configurati a seconda del front-end.  
- Database reale al posto della `Map`.

Con questa spiegazione puoi orientarti in ogni singola riga del file e sai esattamente dove intervenire per evolvere il progetto.  
Buon coding! 🚀
