<details>
  <summary>Descrivere tutte le proprietà delle funzioni hash crittograficamente sicure e almeno un esempio di attacco alle funzioni hash crittograficamente sicure.
</summary>

  **Una funzione hash crittograficamente sicura deve soddisfare le seguenti proprietà:**  

1. **Efficienza**  
   Deve essere computazionalmente facile calcolare l’hash \( H(x) \) per ogni input \( x \) indipendentemente dalla sua lunghezza.

2. **Unidirezionalità**  
   Deve essere computazionalmente difficile risalire all’input \( x \) data solo l'impronta\( H(x) \).

3. **Resistenza debole alle collisioni**  
   Dato un input \( x \), deve essere difficile trovare un altro input \( y \ne x \) tale che \( H(y) = H(x) \).

4. **Resistenza forte alle collisioni**  
   Deve essere difficile trovare qualsiasi coppia di input distinti \( (x, y) \) tale che \( H(x) = H(y) \).

## Esempio di attacco: Birthday Attack

Il **Birthday Attack** è un attacco crittografico che sfrutta il paradosso del compleanno per trovare collisioni in una funzione hash.

- **Obiettivo**: trovare due input differenti \( x \) e \( y \) tali che \( H(x) = H(y) \).
- **Utilizzo**: può essere usato, ad esempio, per ottenere la firma digitale su un documento apparentemente innocuo e riutilizzarla su un altro documento malevolo con lo stesso hash.
- **Motivazione**: la probabilità di collisione aumenta con il numero di tentativi effettuati, rendendo questo attacco efficace per funzioni hash con output troppo corto.

</details>

<details>
  <summary>Descrivere le caratteristiche dei PRNG crittograficamente sicuri e fare eventuali schemi di dimensionamento. Descrivere, inoltre per quali motivi nei cifrari a flusso si utilizzano principalmente PRNG crittograficamente sicuri al posto dei TRNG: fornire infine esempi di uso dei TRNG in ambito di crittografia moderna.
</summary>

**I PRNG** crittograficamente sicuri devono avere le seguenti caratteristiche:

  **Casualità dei bit di uscita:** che si verifica sottoponendo l’uscita ad una serie di test statistici previsti dallo standard FIPS 140-2
**Imprevedibilità dei bit di uscita:** che si verifica sottoponendo l’uscita del componente al test next-bit che considera L bit e a partire da essi verifica se esiste un protocollo polinomiale in grado di predire con una probabilità maggiore di 0.5 il bit (L+1)-esimo.
**Indeducibilità del seme:** Deve essere computazionalmente infattibile per un intrusore riuscire a risalire ai bit precedenti fino al seme iniziale. Questa caratteristica è garantita dall’utilizzo di una funzione unidirezionale al posto o della funzione G di calcolo dello stato futuro, o della funzione F di uscita, dell’automa a stati finiti con cui è implementato il PRNG crittografico.
4
Per garantire sicurezza, la lunghezza del seed deve essere almeno pari al livello di sicurezza desiderato. Esempio:

Sicurezza a 128 bit ⇒ seed di almeno 128 bit.

Sicurezza a 256 bit ⇒ seed di almeno 256 bit.

Nei **cifrari a flusso** si utilizzano principalmente PRNG crittograficamente sicuri al posto dei TRNG perché questi ultimi hanno un grosso limite legato alla frequenza di generazione. La maggior parte degli scenari applicativi richiede una frequenza molto alta di generazione di chiave che il TRNG non può garantire, perché quest’ultimo estrae bit casuali da fenomeni fisici che non si verificano in base alle nostre esigenze. Un secondo motivo è legato alla non riproducibilità del flusso di uscita che caratterizza i TRNG e che è fondamentale ai fini dell’implementazione di un cifrario a flusso.
**Esempi di utilizzo** di TRNG in ambito della crittografia moderna sono tutti i componenti crittografici che richiedono un seed iniziale casuale, imprevedibile e indeducibile come appunto i PRNG crittograficamente sicuri, oppure una chiave segreta che necessariamente deve essere generata in modo da essere casuale, imprevedibile e indeducibile, come le funzioni di Encryption.

</details>
<details>
<summary>Indicare le limitazioni di un TRNG se usato in cifrari simmetrici</summary>

  **Le limitazioni nell’uso di un TRNG** per implementare un cifrario simmetrico si evidenziano nel caso di cifrari a flusso.
Nei cifrari a flusso non è possibile utilizzare TRNG per la generazione del flusso di chiave perché c’è un limite intrinseco legato alla frequenza di generazione. La maggior parte degli scenari applicativi richiede una frequenza molto alta di generazione di chiave che il TRNG non può garantire, perché quest’ultimo estrae bit casuali da fenomeni fisici che non si verificano in base alle nostre esigenze. Un secondo motivo è legato alla non riproducibilità del flusso di uscita che caratterizza i TRNG e che è fondamentale ai fini dell’implementazione di un cifrario a flusso.

</details>

<details>
<summary>Descrivere l'attacco con estensione alle funzioni hash crittograficamente sicure fornendo anche schemi di spiegazione</summary>

  **L’attacco con estensione della lunghezza del messaggio (length extension attack)** colpisce tutte quelle situazioni in cui una funzione hash crittograficamente sicura viene implementata secondo lo schema di compressione iterata, caratteristico di algoritmi come MD5, SHA-1 e SHA-2. Questo schema, pur garantendo efficienza e modularità, introduce una vulnerabilità strutturale sfruttabile in particolari condizioni d’uso, in particolare quando la funzione hash viene impiegata per generare un codice di autenticazione (MAC) del tipo H(s || m), dove s è un segreto condiviso tra il mittente e il destinatario, e m è il messaggio da autenticare.

In questo contesto, un attaccante che riesca a ottenere l’impronta H(s || m) pur senza conoscere il valore del segreto s, può sfruttare le caratteristiche iterative della funzione hash per costruire un nuovo messaggio m* = m || padding || m′ e calcolarne un hash valido H(s || m || padding || m′), dove m′ è un’estensione arbitraria scelta dall’attaccante. Il principio chiave dell’attacco è che l’hash H(s || m) rappresenta lo stato intermedio della funzione di compressione subito dopo l’elaborazione di s || m. Utilizzando questo stato come punto di partenza, l’attaccante può continuare la computazione dell’hash in modo coerente, come se fosse stato il mittente legittimo.

L’unica difficoltà per l’attaccante sta nell’indovinare la lunghezza di s, necessaria per calcolare correttamente il padding. Tuttavia, se s è di lunghezza nota o prevedibile (ad esempio una chiave fissa di 16 o 32 byte), questa operazione è del tutto fattibile. Una volta stimata correttamente la lunghezza, l’attaccante è in grado di riprodurre il padding che la funzione hash avrebbe aggiunto automaticamente a s || m, e proseguire con i blocchi di m′ utilizzando lo stesso schema iterativo.

Anche nei casi in cui lo schema di compressione iterata includa padding alla fine del messaggio, l’attacco resta pericoloso. Questo è particolarmente vero quando il messaggio m ha forma numerica o binaria e il sistema ricevente non è in grado di distinguere tra messaggi originari e quelli estesi artificialmente, in quanto la struttura risultante m || padding || m′ può apparire semanticamente valida o indistinguibile dall’originale. In questi scenari, non è solo la funzione hash a essere vulnerabile, ma l’intero protocollo di autenticazione.

Per proteggersi da questo tipo di attacco, la contromisura più semplice ed efficace è evitare la costruzione H(s || m) e preferire invece H(m || s), invertendo l’ordine tra il messaggio e il segreto. Così facendo, l’attaccante non può più simulare la continuazione del processo hash, poiché non conosce s, che in questo caso si troverebbe alla fine e quindi sarebbe incluso in blocchi che non può costruire o modificare.

<pre>
**schema normale**
Mittente: Alice

Segreto: s
Messaggio: m

Calcolo:
  H(s || m) = HASH OUTPUT

Schema interno:

  [ IV ] ──▶ f ──▶ f ──▶ ... ──▶ f ──▶ Final ──▶ H(s || m)
              ▲       ▲              ▲
             s_1     m_1          padding

             
**lenght extension attack**
Attaccante:

Conosce: m, H(s || m)
Non conosce: s

Obiettivo: costruire m* = m || padding || m′ e ottenere H(s || m || padding || m′)

Strategia:
  1. Usa H(s || m) come stato iniziale fittizio.
  2. Aggiunge blocchi m′ personalizzati.
  3. Continua il calcolo come se fosse legittimo.

Schema:

  [ H(s || m) ] ──▶ f ──▶ f ──▶ Final ──▶ H(s || m || padding || m′)
                      ▲       ▲
                   m′_1     m′_2 (scelti dall’attaccante)

Messaggio forgiato inviato:
  m* = m || padding || m′
  hash_falsificato = H(s || m || padding || m′)
</pre>
</details>

<details>
<summary>Osservare lo schema KDC e riportare se lo schema è vulnerabile e se lo è in che modo</summary>
 
  **Lo schema di implementazione di questo centro di distribuzione delle chiavi**, così com’è, non è vulnerabile nel senso della riservatezza a patto che:
Il database in cui T custodisce le master key Ka e Kb sia assolutamente protetto, e inoltre Ka e Kb devono essere impossibili da dedurre, intercettare o indovinare.
I numeri random Ra ed Rb devono essere assolutamente causali, imprevedibili e indeducibili (altrimenti il protocollo sarebbe vulnerabile nella fase in cui le parti si identificano tra di loro)
La chiave di sessione k deve essere casuale, imprevedibile e indeducibile.
Fatte queste ipotesi il protocollo è robusto dal punto di vista della riservatezza ma non dell’integrità. Un intrusore è sempre in grado di modificare i messaggi a caso e questo può causare un Denial of Service (DoS) perché le parti non riusciranno a cifrare e decifrare correttamente, dunque la sessione sarà invalidata.

Con la presenza dei passaggi 4 e 5 un attacco con replica al passaggio 3 sarebbe evitato solo nel modello di minaccia in cui l’intrusore fosse in grado di avviare un attacco attivo sul canale che interconnette A e B, ma non conosca completamente la chiave di sessione k o ne abbia una conoscenza solo parziale.

Se consideriamo il modello di minaccia più sfortunato in cui l’attaccante sia riuscito a decifrare la chiave di sessione K, l’attacco con replica al passaggio 3 avrebbe successo. L’intrusore potrebbe avviare una comunicazione con B facendo partire il protocollo direttamente al passaggio 3 grazie al messaggio precedentemente intercettato. Grazie alla conoscenza di K potrebbe quindi impersonificare A rispondendo correttamente alla sfida al passaggio 5 e da quel momento catturare ogni informazione inviata successivamente.
Sarebbe stato possibile evitare questo attacco solo con una precauzione molto costosa, ovvero quella di far tenere traccia a B di tutte le chiavi di sessione precedentemente utilizzate, in modo da rivelare l’attacco, ma nello schema proposto non è previsto.
È prevista tuttavia una soluzione parziale a questa vulnerabilità visto che KDC attribuisce un tempo di vita limitato alle chiavi di sessione.

</details>
