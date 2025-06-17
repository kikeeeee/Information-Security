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


