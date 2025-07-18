<head>
  
  **Cap 2**</head>
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

<head>
  
  **Cap 3-4**</head>

<details>
<summary>Osservare lo schema KDC e riportare se lo schema è vulnerabile e se lo è in che modo</summary>
 
  **Lo schema di implementazione di questo centro di distribuzione delle chiavi**, così com’è, non è vulnerabile nel senso della **riservatezza** a patto che:
Il database in cui T custodisce le master key **Ka** e **Kb** sia assolutamente protetto, e inoltre **Ka** e **Kb** devono essere impossibili da dedurre, intercettare o indovinare.
I numeri random **Ra** ed **Rb** devono essere assolutamente causali, imprevedibili e indeducibili (altrimenti il protocollo sarebbe vulnerabile nella fase in cui le parti si identificano tra di loro)
La chiave di sessione k deve essere casuale, imprevedibile e indeducibile.
Fatte queste ipotesi il protocollo è robusto dal punto di vista della riservatezza ma non dell’integrità. Un intrusore è sempre in grado di modificare i messaggi a caso e questo può causare un Denial of Service (DoS) perché le parti non riusciranno a cifrare e decifrare correttamente, dunque la sessione sarà invalidata.

Con la presenza dei passaggi 4 e 5 un attacco con replica al passaggio 3 sarebbe evitato solo nel modello di minaccia in cui l’intrusore fosse in grado di avviare un attacco attivo sul canale che interconnette A e B, ma non conosca completamente la chiave di sessione k o ne abbia una conoscenza solo parziale.

Se consideriamo il modello di minaccia più sfortunato in cui l’attaccante sia riuscito a decifrare la chiave di sessione K, l’attacco con replica al passaggio 3 avrebbe successo. L’intrusore potrebbe avviare una comunicazione con B facendo partire il protocollo direttamente al passaggio 3 grazie al messaggio precedentemente intercettato. Grazie alla conoscenza di K potrebbe quindi impersonificare A rispondendo correttamente alla sfida al passaggio 5 e da quel momento catturare ogni informazione inviata successivamente.
Sarebbe stato possibile evitare questo attacco solo con una precauzione molto costosa, ovvero quella di far tenere traccia a B di tutte le chiavi di sessione precedentemente utilizzate, in modo da rivelare l’attacco, ma nello schema proposto non è previsto.
È prevista tuttavia una soluzione parziale a questa vulnerabilità visto che KDC attribuisce un tempo di vita limitato alle chiavi di sessione.

</details>
<details>
  <summary>Descrivere i cifrari a flusso. Evidenziare le differenze in termine di perdita di sincronismo tra cifrari a flusso sincrono e autosincronizzante. Nei cifrari a flusso autosincronizzante quanto è "lungo" un transitorio dovuto alla perdita di sincronismo?</summary>
  
 **sono algoritmi crittografici simmetrici** che operano trasformando il testo in chiaro un bit o un byte alla volta, invece che in blocchi fissi come nei cifrari a blocchi. Funzionano generando un flusso di bit pseudocasuali, detto keystream, a partire da una chiave segreta e da un eventuale vettore di inizializzazione. Questo flusso viene poi combinato con il testo in chiaro mediante un'operazione XOR, ottenendo così il testo cifrato. La stessa operazione, applicata al testo cifrato e al medesimo keystream, consente di recuperare il testo originale. Il punto di forza dei cifrari a flusso è l’efficienza: sono estremamente veloci e leggeri, rendendoli particolarmente adatti alla cifratura in tempo reale di flussi di dati continui come audio o video. Tuttavia, la loro sicurezza dipende in modo critico dal fatto che il keystream non venga mai riutilizzato con la stessa chiave, altrimenti diventano vulnerabili ad attacchi crittanalitici.

Un’interferenza attiva sul canale, come la modifica, la cancellazione o l’inserimento di bit, può provocare perdita di sincronismo. Nei cifrari a flusso sincroni, in cui il keystream dipende unicamente dalla chiave segreta e non dai dati trasmessi, la perdita o l’inserimento di un singolo bit compromette definitivamente la sincronizzazione tra le parti. Da quel momento in poi, la decifrazione produrrà risultati errati per tutto il messaggio successivo. Tuttavia, un errore isolato, come la modifica di un singolo bit durante la trasmissione, non compromette il sincronismo: l’errore resta confinato a quel bit e non si propaga oltre.

Nei cifrari a flusso autosincronizzanti, invece, il keystream è calcolato in funzione di una finestra dei bit precedenti del testo cifrato, solitamente gestita tramite uno shift register. In questo caso, ogni bit del keystream dipende da un numero fisso di bit cifrati precedenti (es. gli ultimi n bit). Di conseguenza, qualunque tipo di attacco attivo sul canale — che sia modifica, cancellazione o inserimento di bit — provoca una perdita temporanea di sincronismo, ma questa si auto-corregge dopo l’elaborazione di n bit corretti. Il periodo in cui il ricevente produce output errato a causa della perdita di sincronizzazione è detto transitorio, e ha una durata esattamente pari alla lunghezza dello shift register usato, cioè n bit. Superato questo intervallo, la corretta sincronizzazione viene ripristinata automaticamente, rendendo i cifrari autosincronizzanti più resilienti alla corruzione del canale rispetto ai cifrari sincroni.
</details>

<details>
  <summary>Descrivere in termine di propagazione dell'errore le differenze tra le modalità di cifratura: ECB, CBC, OFB, CFB e CTR
</summary>
  
  **La modalità ECB (Electronic Codebook)** è la più semplice e intuitiva: ogni blocco del testo in chiaro viene cifrato indipendentemente dagli altri usando la stessa chiave. Questo approccio ha il vantaggio della semplicità e della possibilità di cifrare e decifrare i blocchi in parallelo, ma è considerato insicuro perché blocchi identici producono blocchi cifrati identici, rivelando pattern nel messaggio. Dal punto di vista della propagazione dell’errore, se un blocco del testo cifrato viene corrotto, solo il blocco corrispondente del testo in chiaro risulterà errato in fase di decifrazione; non ci sono effetti sugli altri blocchi. Come descritto nel testo, l'errore non si propaga oltre il blocco alterato.
  **La modalità CBC (Cipher Block Chaining)** introduce una dipendenza tra i blocchi: ogni blocco in chiaro viene XORato con il blocco cifrato precedente prima della cifratura. Il primo blocco viene XORato con un vettore di inizializzazione (IV). Questo schema offre una sicurezza maggiore rispetto a ECB in quanto maschera la ripetizione dei dati, ma comporta una propagazione dell’errore più ampia: un errore in un blocco cifrato compromette completamente il blocco in chiaro corrispondente, e produce un errore parziale nel blocco successivo, poiché viene utilizzato un valore decifrato (alterato) come input del prossimo XOR. Questa struttura a pipeline provoca propagazione dell'errore.
  **La modalità CFB (Cipher Feedback)** converte un cifratore a blocchi in un cifratore a flusso autosincronizzante. In essa, il blocco cifrato precedente (o l’IV all’inizio) viene cifrato, e il risultato è XORato con il blocco di testo in chiaro per ottenere il cifrato. In questa configurazione, un errore in un bit del testo cifrato produce un errore nello stesso bit del blocco di testo in chiaro corrispondente, e l’intero blocco successivo risulterà errato perché si utilizza il blocco cifrato corrotto come input per il passo successivo. Anche in CFB, quindi, l’errore si propaga su due blocchi, ma in modo diverso da CBC: l'errore dura "solamente" un transitorio (di lunghezza definita dalla lunghezza degli shift register); decisamente non l'ideale per un canale rumoroso.
  L**a modalità OFB** ci ricorda un cifrario a flusso sincrono. Essendoci una retroazione del flusso di chiave e non del cifrato la modifica di un bit del cifrato impatta solo sulla decodifica di quel bit, e non su tutto il flusso di dati. Quindi rispetto alla modifica dei bit di cifrato non si ha propagazione dell’errore e questo rende la modalità OFB più indicata della modalità CFB nei casi di canali rumorosi. La cancellazione o l’iniezione di bit nel cifrato invece provocano una perdita di sincronismo definitiva.
Infine, **la modalità CTR (Counter Mode)** impiega un contatore che viene cifrato a ogni passo per generare un keystream indipendente dal testo in chiaro o cifrato. Ogni blocco del messaggio viene quindi XORato con il blocco corrispondente del keystream; situazione analoga a **OFB**.
</details>


<details>
  <summary>
    Descrivere il problema della malleabilità dei cifrari simmetrici a flusso
  </summary>
  
  **I cifrari simmetrici a flusso** eseguono l’operazione di cifratura e decifrazione per mezzo di una somma modulo 2 tra il messaggio in chiaro e un flusso di chiave. Il problema della malleabilità è una vulnerabilità insita in questo schema e nelle proprietà dell’operazione di XOR e consiste nella possibilità, sotto opportune ipotesi non comuni ma comunque possibili, dell’intrusore nel modificare il cifrato in maniera tale da avere a valle dell’operazione di decifrazione un effetto da lui voluto.
Le ipotesi sono che l’intrusore sia in grado di effettuare degli attacchi attivi sul canale di comunicazione e che abbia delle conoscenze pregresse sul contenuto del messaggio che sorgente e destinazione legittime si scambiano. In questi casi, se ci troviamo in uno scenario in cui il messaggio cifrato è fortemente strutturato, e l’intrusore è a conoscenza di questa struttura, il pericolo a fronte di un attacco che sfrutta la malleabilità è particolarmente concreto.
Supponiamo che, ad esempio, il messaggio strutturato sia un messaggio in cui il primo campo è sempre il mittente, che chiamiamo Mit1; dunque, i primi bit del cifrato si riferiscano al mittente legittimo rappresentato in forma esadecimale. Se l’intrusore sa a chi è destinato un messaggio, quindi è a conoscenza di Mit1, senza conoscere nulla sulla chiave potrebbe addirittura modificare il campo mittente con un mittente arbitrario di sua scelta Mit2. Gli basterà modificare quei bit di cifrato sommandoli modulo 2 con Mit1 XOR Mit2.

Se indichiamo con K i bit di chiave che cifrano i bit relativi alla rappresentazione esadecimale del campo mittente, avremo in fase di decifrazione un messaggio così composto: Mit1 XOR K XOR Mit1 XOR Mit2 XOR K.
Dal momento che per le proprietà dello XOR i termini uguali si elidono, il risultato sarà che il destinatario decifrerà un messaggio in cui il campo mittente ha la rappresentazione esadecimale di Mit2, il mittente scelto dall’attaccante.

</details>

<details>
  <summary>Evidenziare qual è la differenza tra una cifratura a flussi e una cifratura a blocchi e in quali scenari applicativi è preferibile utilizzare un cifrario rispetto ad un altro.
</summary>
  
  **I cifrari a flusso** si ispirano al cifrario perfetto della crittografia classica denominato One Time Pad e prevedono che uno o pochi bit alla volta del messaggio in chiaro siano sommati modulo due con un flusso di chiave opportunamente generato. Lo scenario applicativo in cui è preferibile utilizzare i cifrari a flusso è quello di trasmissione di dati orientata al flusso, come applicazioni web, telefonia, ecc., perché per come è implementato non introduce rallentamenti; quindi, tipicamente risulta più veloce di un cifrario a blocchi.
I cifrari a blocchi in linea generale si ispirano ai cifrari poligrafici composti della Teoria di Shannon secondo cui, per rendere sicuro ai fini della riservatezza un cifrario che non è perfetto, bisogna eseguire iterativamente operazioni di trasposizione e sostituzione su blocchi di messaggio in chiaro, al fine di ottenere confusione e diffusione. Quindi i cifrari a blocchi prevedono che il messaggio in chiaro sia suddiviso in blocchi di grandezza variabile a seconda della modalità di cifratura. Gli scenari in cui è preferibile utilizzare questi cifrari sono applicazioni asincrone, store and forward, come di protezione di file, pacchetti, strutture dati, posta elettronica, perché è più facile impiegare correttamente un cifrario a blocchi rispetto a un cifrario a flusso, e comunque, un cifrario a blocchi usato scorrettamente risulta comunque più sicuro di un cifrario a flusso usato scorrettamente.

</details>


<details>
  <summary>
    Quando perde sincronismo un cifrario a flusso sincrono? Quali sono le condizioni necessarie che rendono sicuro l'utilizzo di un cifrario a flusso sincrono? Si può utilizzare un TRNG per generare il flusso di chiave in un cifrario a flusso sincrono? Motivare le risposte.
  </summary>
  
  **Un cifrario a flusso sincrono** perde sincronismo quando sul canale di trasmissione viene effettuato un attacco attivo che provoca la cancellazione di un bit o l’iniezione di un bit fake sul flusso del cifrato. Da quel momento in poi si verifica una perdita di sincronismo definitiva tra sorgente e destinazione e la decifrazione non avviene più correttamente. Questo perché, nei due casi sopra citati, la conseguenza dell’attacco sarà una non corrispondenza tra l’i-esimo bit del messaggio in chiaro e l’i-esimo bit del flusso di chiave, condizione necessaria per una corretta decifrazione.
La generazione del flusso di chiave usato nei cifrari a flusso deve essere assolutamente casuale, imprevedibile, indeducibile e usata una volta sola.
Un TRNG non può essere utilizzato per generare un flusso di chiavi per due motivi:

* Per sua natura non permette riproducibilità del flusso di chiave, e questo è fondamentale nei cifrari a flusso in cui è necessario avere lo stesso flusso di chiave lato sorgente e lato destinazione.
* Per sua natura il TRNG non può garantire un’elevata frequenza di generazione, in quanto la sequenza di bit casuali viene estratta da un processo di campionamento, filtraggio e post-produzione di fenomeni naturali che non possono verificarsi in base alle nostre esigenze.

</details>

<details>
  <summary>
    Descrivere le vulnerabilità dell'utilizzo della modalità Electronic Code Book e descrivere in maniera esaustiva la modalità Cipher Block Chaining evidenziandone vantaggi e svantaggi.
  </summary>
  
  **La modalità di cifratura ECB** prevede che ogni blocco di testo in chiaro sia cifrato in maniera indipendente e totalmente scollegata dagli altri blocchi. Questo provoca un forte determinismo perché l’uscita non sarà veramente aleatoria; basti pensare che a blocchi di testo in chiaro identici corrisponderanno blocchi di cifrato identici. In questo modo viene violato un principio cardine della riservatezza, ovvero quello secondo cui un intrusore dall’osservazione del canale non deve poter imparare nulla di più di ciò che conosceva prima. Se si utilizza la modalità ECB per cifrare due volte lo stesso messaggio, a un intrusore basta osservare il canale per capire subito che è stato inviato per due volte lo stesso messaggio, quindi la riservatezza non è garantita. Dunque, la modalità ECB non è in grado di nascondere certe caratteristiche del testo in chiaro e, come avviene in questi casi, un crittoanalista esperto dalla sola analisi del cifrato ci potrà dedurre molte informazion

**La modalità CBC** prevede che ogni blocco di testo cifrato dipenda da tutti i blocchi precedenti di testo in chiaro. La struttura è una pipeline in cui ogni blocco di testo in chiaro, prima di essere cifrato con una trasformazione di encryption che ha la chiave segreta di cifratura k uguale per tutti i blocchi, viene sommato modulo 2 con il blocco cifrato ottenuto al passo precedente. In questo modo si elimina quel determinismo che si ha nella modalità ECB. Per evitare all’intrusore di capire se sono stati cifrati due blocchi uguali o con la stessa intestazione, il blocco di testo in chiaro, prima della cifratura, viene sommato modulo 2 con un vettore di inizializzazione non segreto, ma che deve essere invece casuale, imprevedibile e usato una e una sola volta. L’ultimo blocco di cifrato inoltre viene riempito secondo opportune tecniche di padding.

**I vantaggi** della modalità CBC sono:
* Aleatorietà dell’uscita, a patto che il vettore di inizializzazione sia casuale, imprevedibile e usato una e una sola volta.
  
**Gli svantaggi** sono:
* La struttura a pipeline impedisce il processamento in parallelo dei singoli blocchi, quindi efficienza minore rispetto alla modalità ECB
* Il padding introduce un overhead (anche EBC ha il padding, è un malus ma non è un peggioramento rispetto a ECB)
* Se viene meno l’integrità di un blocco di cifrato l’errore si propaga anche sui blocchi successivi
* In fase di decifrazione viene usata la funzione inversa D, quindi in caso di implementazione hardware non può essere utilizzatolo lo stesso componente per la stazione trasmittente e quella ricevente.

</details>

<details>
  <summary>
    Descrivere le tipologie di modelli di distribuzione/accordo di chiavi simmetriche evidenziando anche gli scenari in cui è conveniente adottare un modello rispetto ad un altro.
  </summary>
  
  **Esistono due famiglie di modelli** per la distribuzione/accordo di chiavi simmetriche tra due entità:
  
* Una famiglia che prevede che in precedenza ci sia stato un Key Agreement tra le entità, ovvero sia stato distribuito almeno una volta un segreto tra le entità, attraverso un canale dedicato fuori banda. Appartengono a questa famiglia due modelli, il **modello Master Key** e il **modello KDC (Key Distribution Center)**
* Una famiglia che non prevede Key Agreement. In questo caso viene utilizzato o un protocollo di scambio detto di **Diffie-Hellman** oppure un **cifrario asimmetrico**.

**Il Modello Master Key** prevede che le entità siano già in possesso di un segreto condiviso con l’altra entità, chiamato Master Key, che non viene utilizzato per cifrare messaggi. Siccome la distribuzione di un segreto su canale dedicato è molto costosa, per preservare questo segreto si faranno circolare il minor numero di testi cifrati possibili prodotti con l’uso della Master Key. I messaggi verranno cifrati con delle chiavi, dette di sessione, che verranno distribuite in forma cifrata all’altra entità sul canale insicuro, reso sicuro dall’uso della Master Key, che ne preserva la riservatezza. La Master Key, quindi, avrà vita più lunga possibile e verrà usata solo per cifrare chiavi di sessione. La chiave di sessione invece verrà cambiata spesso per evitare che un intrusore possa intercettare un alto numero di testi cifrati con la stessa chiave e possa dedurre qualche informazione. Questo modello ha un grosso problema di scalabilità visto che il numero di Master Key che devono essere scambiate su canale dedicato è proporzionale a n^2, dove n è il numero di entità.

**Il modello KDC** non risolve completamente il problema della scalabilità ma lo migliora, in quanto prevede un numero di chiavi che devono essere scambiate su un canale dedicato pari a n, ovvero una chiave per ogni entità. Per realizzare questo modello però dobbiamo introdurre una terza parte chiamata centro di distribuzione delle chiavi (KDC – Key Distribution Center). L’ipotesi che assumiamo è che le entità A e B abbiano condiviso con T, entità fidata del KDC, rispettivamente le chiavi Ka e Kb, in maniera totalmente sicura e tale da non poter essere indovinate, dedotte o intercettate. Quando l’entità A vorrà comunicare con B in forma cifrata, dovrà inviare a T la richiesta per ricevere una chiave di sessione. T quindi genererà la chiave e la distribuirà in maniera sicura ad A. A quindi metterà anche B in condizione di conoscere quella chiave che entrambi useranno per cifrare e decifrare i messaggi che si scambiano.

Gli scenari in cui è conveniente usare questi modelli sono quelli chiusi, con un numero di utenti limitato, ad esempio scenari in cui è necessario garantire riservatezza ai messaggi scambiati tra entità all’interno di una rete aziendale.

Per quanto riguarda la famiglia di modelli per la distribuzione/accordo di chiavi simmetriche tra due entità, **senza un precedente Key Agreement**, è possibile utilizzare il protocollo di **Diffie- Hellman**, che è un meccanismo crittografico che si basa sul problema difficile della teoria dei numeri (il problema del logaritmo discreto), e consente a due entità di concordare un segreto scambiandosi delle informazioni sul canale insicuro.

Un altro modo per distribuire una chiave simmetrica tra entità senza precedenti Key Agreement è l’utilizzo di cifrari asimmetrici. Le due parti possono scambiarsi chiavi di sessione in forma cifrata sfruttando il requisito di autenticità della chiave pubblica, garantito dall’infrastruttura a chiave pubblica (PKI).

Queste ultime due soluzioni sono convenienti da usare in quegli scenari aperti, in cui abbiamo un numero di utenti che è in continuo aumento e quindi è necessario che venga garantito il requisito della scalabilità.

</details>

<details>
  <summary>
    Supponiamo che Alice utilizzi la modalità CBC per inviare un messaggio cifrato (ciphertext) a Bob e si sia dimenticata il valore del vettore di inizializzazione IV. Può ricostruire il testo in chiaro per intero o solo parzialmente? Se interamente spiegare perché e se parzialmente spiegare quali parti può ricostruire.
  </summary>
  
  **Con la modalità CBC**, in fase di decifrazione se non si ha la disponibilità del vettore di inizializzazione IV il messaggio può essere ricostruito solo parzialmente. In particolare, possono essere decifrati tutti i blocchi di cifrato ad esclusione del primo. Questo perché nello schema di decifrazione strutturato in pipeline, il vettore di inizializzazione va ad influenzare solo la ricostruzione del primo blocco di cifrato, sommandosi in XOR all’uscita della trasformazione D applicata al primo blocco, e non ha alcuna influenza ai passi successivi della decifrazione, in cui in XOR all’uscita della trasformazione D si vanno a sommare i blocchi di cifrato al passo precedente. (Però prof… l’IV passa in chiaro all’inizio della conversazione, come cazzo fa Bob a perderlo se glielo abbiamo appena mandato => bob è un rimasto)

</details>

<details>
  <summary>
    Bob, per chiedere a Charlie di effettuare una certa azione, deve dimostrare che Alice è d’accordo. La richiesta è contenuta in un messaggio M che in linguaggio naturale creato da Bob. Il protocollo usato è il seguente, dove Ka è un segreto condiviso tra Alice e Charlie. Se l’hash inviato in messaggio è corretto Charlie accetta la richiesta. Supponi che Alice voglia far credere a Charlie che Bob abbia richiesto di autorizzare il messaggio M’. Pensi sia possibile?
Motivare la risposta
  </summary>
Se Alice è in grado di trovare un messaggio M’ con impronta uguale a M, ovvero tale che H(M)=H(M’) è possibile. Questo perché indipendentemente del segreto Ka, in questo caso si avrà sempre che H(M||Ka) = H(M’||Ka), quindi H(M||Ka) sarà un autenticatore anche per M’ e non solo per M. Per tutte le funzioni hash le collisioni possono sempre esserci; infatti, nella realtà tutti gli algoritmi di cui disponiamo per la realizzazione di funzioni hash non hanno realmente un comportamento da oracolo casuale. È per questo che per aumentare l’aleatorietà nel comportamento di una funzione hash e ridurre attacchi di questo tipo, detti attacchi alla collisione, nella pratica non ci si limita ad effettuare una compressione sola ma una doppia compressione.
</details>
<head>
  
  **Cap 5**</head>
