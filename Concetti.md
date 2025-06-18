<details>
  <summary>I cifrari a flusso</summary> sono algoritmi crittografici simmetrici che operano trasformando il testo in chiaro un bit o un byte alla volta, invece che in blocchi fissi come nei cifrari a blocchi. Funzionano generando un flusso di bit pseudocasuali, detto keystream, a partire da una chiave segreta e da un eventuale vettore di inizializzazione. Questo flusso viene poi combinato con il testo in chiaro mediante un'operazione XOR, ottenendo così il testo cifrato. La stessa operazione, applicata al testo cifrato e al medesimo keystream, consente di recuperare il testo originale. Il punto di forza dei cifrari a flusso è l’efficienza: sono estremamente veloci e leggeri, rendendoli particolarmente adatti alla cifratura in tempo reale di flussi di dati continui come audio o video. Tuttavia, la loro sicurezza dipende in modo critico dal fatto che il keystream non venga mai riutilizzato con la stessa chiave, altrimenti diventano vulnerabili ad attacchi crittanalitici.

Per generare il keystream, spesso si utilizzano generatori di numeri pseudocasuali (PRNG). Un PRNG è un algoritmo deterministico che, partendo da un valore iniziale detto seed, produce una sequenza di bit che appare casuale ma è in realtà completamente deterministica. Questo implica che, se un attaccante conosce il seed o riesce a dedurlo, può riprodurre l’intera sequenza di bit generati e decifrare il messaggio. I PRNG sono molto usati in applicazioni dove l'efficienza è importante e la vera casualità non è essenziale, ma non sono idonei per generare chiavi segrete, proprio per la loro prevedibilità intrinseca. Per questo motivo, è fondamentale che il seed venga scelto con sufficiente entropia, spesso usando un’altra classe di generatori.

A differenza dei PRNG, i True Random Number Generator (TRNG) si basano su fenomeni fisici intrinsecamente casuali, come il rumore termico, il decadimento radioattivo o altre fonti analogiche di entropia. Questo permette di generare bit realmente imprevedibili e non riproducibili, garantendo una maggiore sicurezza crittografica. I TRNG sono usati per la generazione di chiavi segrete, di seed sicuri per PRNG e in tutte le situazioni in cui è necessario assicurare l’imprevedibilità assoluta dei dati. Tuttavia, i TRNG sono generalmente più lenti e meno affidabili in termini di frequenza di generazione, poiché dipendono da eventi fisici non controllabili con precisione. Per questo motivo, spesso si utilizza una combinazione di TRNG e PRNG, in cui il TRNG fornisce il seed iniziale per un PRNG crittograficamente sicuro (PRNGCS), che poi viene utilizzato per generare sequenze più lunghe di bit pseudocasuali con buona efficienza e sicurezza.

In sintesi, i cifrari a flusso sono strumenti fondamentali per la cifratura leggera e continua dei dati, ma la loro sicurezza dipende fortemente dalla qualità e dall’unicità del keystream, che deve essere generato tramite PRNG sicuri e inizializzati con entropia vera, idealmente fornita da un TRNG.
</details>



<details>
<summary>Schema di compressione iterata</summary>
  Questo schema prende in input un messaggio di lunghezza arbitraria e lo trasforma in un'impronta (hash) di lunghezza fissa attraverso una serie di passaggi sequenziali e deterministici. Tutto inizia con un valore iniziale fisso, detto Initialization Vector (IV), definito dallo standard della funzione hash. Questo IV rappresenta lo stato iniziale della computazione. Il messaggio da hashare viene prima suddiviso in blocchi di dimensione fissa, per esempio 512 bit, indicati nello schema come “Message block 1”, “Message block 2”, fino a “Message block N”. Ogni blocco viene processato in sequenza attraverso una funzione di compressione indicata con “f”. Questa funzione prende due input: lo stato corrente della computazione (inizialmente l’IV) e il blocco di messaggio corrente. Il suo output è uno stato aggiornato che verrà usato come input per la prossima iterazione della funzione di compressione con il successivo blocco del messaggio. Questo processo iterativo continua fino a quando tutti i blocchi del messaggio sono stati processati.

Al termine dei blocchi reali del messaggio, la funzione hash aggiunge in automatico una sequenza di bit chiamata “length padding”, che serve a completare la lunghezza del messaggio per rispettare un multiplo del blocco richiesto. Questo padding include un bit '1', seguito da un numero di zeri e infine dai bit che rappresentano la lunghezza originale del messaggio, come richiesto dallo standard della funzione. Anche questo blocco viene elaborato dalla funzione di compressione “f”, esattamente come gli altri. Dopo il padding, lo stato aggiornato viene passato a una fase detta “Finalisation”, che può consistere in ulteriori operazioni specifiche per la funzione hash in uso. Infine, lo stato risultante viene emesso come valore hash, cioè l’impronta digitale del messaggio originale.

Questo schema è efficiente e riutilizzabile, ma presenta una debolezza strutturale importante: la prevedibilità e modularità dello stato interno a ogni passo. Infatti, l’output della funzione f dopo ogni blocco diventa il nuovo stato da cui si può continuare il calcolo. Proprio questa proprietà viene sfruttata nel length extension attack: se un attaccante conosce il valore hash H(s || m) di un messaggio autenticato con un segreto s anteposto, e riesce a indovinare la lunghezza di s, può simulare l’intero schema a partire da H(s || m), aggiungendo nuovi blocchi m′ e proseguendo la computazione come se fosse l’autore originale del messaggio. Per questo motivo, lo schema di compressione iterata è stato abbandonato nelle funzioni hash moderne come SHA-3, che adottano strutture più resistenti a questo tipo di attacchi.
</details>
<details>
  <summary>
    Schema KDC
  </summary>
  
  **Come funziona lo schema KDC** presente nel file: 
  * A invia al KDC:
  R_A: un nonce (numero casuale) per prevenire replay.
  A e B: identificativi delle entità che vogliono comunicare.
  * Il KDC genera e manda a B:
  Una chiave di sessione k.
  Un pacchetto per A, cifrato con la sua chiave segreta K_A, contenente il nonce di A (R_A), l’identificativo di B e la session key k.
  Un ticket per B: E_KB(A || k), che A non può leggere, ma può inoltrare a B.


</details>
