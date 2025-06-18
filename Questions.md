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
Le limitazioni nell’uso di un TRNG per implementare un cifrario simmetrico si evidenziano nel caso di cifrari a flusso.
Nei cifrari a flusso non è possibile utilizzare TRNG per la generazione del flusso di chiave perché c’è un limite intrinseco legato alla frequenza di generazione. La maggior parte degli scenari applicativi richiede una frequenza molto alta di generazione di chiave che il TRNG non può garantire, perché quest’ultimo estrae bit casuali da fenomeni fisici che non si verificano in base alle nostre esigenze. Un secondo motivo è legato alla non riproducibilità del flusso di uscita che caratterizza i TRNG e che è fondamentale ai fini dell’implementazione di un cifrario a flusso.

</details>

<details>
<summary>Descrivere l'attacco con estensione alle funzioni hash crittograficamente sicure fornendo anche schemi di spiegazione</summary>
L’attacco con estensione della lunghezza del messaggio, conosciuto come length extension attack, è un attacco crittografico che sfrutta una debolezza strutturale di molte funzioni hash classiche, come MD5, SHA-1 e SHA-2, tutte costruite secondo lo schema di Merkle-Damgård. Questo schema prevede che il messaggio venga diviso in blocchi e che ogni blocco venga elaborato iterativamente, aggiornando uno stato interno (detto chaining value) che alla fine produce l’impronta (hash) del messaggio. Inoltre, queste funzioni aggiungono automaticamente un padding al messaggio prima di calcolare l’hash, in base alla lunghezza del messaggio originale. In contesti in cui si desidera autenticare un messaggio m usando un segreto condiviso s con una costruzione come H(s || m), questa struttura può essere sfruttata da un attaccante che voglia estendere il messaggio originale con contenuto arbitrario senza conoscere il segreto s. Supponiamo che un mittente (Alice) invii a un destinatario (Bob) un messaggio m accompagnato da H(s || m), per dimostrare che il messaggio proviene da una fonte legittima. Un attaccante che intercetta questa comunicazione ottiene sia m che H(s || m) ma non conosce s. Tuttavia, conoscendo la lunghezza del messaggio m e stimando quella del segreto s (ad esempio, 16 byte), l’attaccante può calcolare quale padding verrebbe automaticamente aggiunto a s || m dalla funzione hash. Poiché lo stato interno della funzione hash dopo aver processato s || m è noto (è esattamente H(s || m)), l’attaccante può riutilizzare questo stato interno come punto di partenza per continuare la computazione dell’hash, aggiungendo un nuovo blocco di dati m′ scelto arbitrariamente. In questo modo, ottiene un nuovo valore hash H(s || m || padding || m′) senza conoscere il segreto, ma solo riutilizzando ciò che la funzione hash avrebbe fatto internamente. Il risultato è che l’attaccante può costruire un nuovo messaggio m* = m || padding || m′ e un hash falso valido, che sarà accettato dal destinatario come se provenisse da Alice, poiché il valore dell’hash combacia con quello che il destinatario calcolerebbe normalmente. Questo attacco si basa quindi sulla possibilità di estendere un messaggio autenticato senza invalidare il codice di autenticazione, semplicemente continuando la computazione dell’hash già avviata, sfruttando la struttura iterativa e il padding automatico. È importante notare che questo attacco è possibile solo se il segreto viene anteposto al messaggio (s || m), non se viene posto alla fine (m || s), perché in quest’ultimo caso l’attaccante non può calcolare l’hash corretto senza conoscere il segreto. Le contromisure principali consistono nell’uso di HMAC (Hash-based Message Authentication Code), una costruzione sicura che include il segreto in maniera resistente a questo tipo di estensione, e nell’adozione di funzioni hash più moderne come SHA-3, che non seguono lo schema di Merkle-Damgård e sono quindi immuni a questa classe di attacchi.
</details>

