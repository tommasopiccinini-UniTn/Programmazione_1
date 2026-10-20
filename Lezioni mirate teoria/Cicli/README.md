# Cicli in C — appunti mirati per la teoria

Riepilogo delle regole e degli esempi discussi, con particolare attenzione alle domande **«Qual è l'output esatto?»**.

**Riferimento d'esame:** [Prima prova teorica, 22 ottobre 2024](../../Prove_Teoriche/22102024%201_ProvaTeorica%20PROG1.pdf) (domande 2 e 3).  
**Dispense di riferimento:** `3.3 StruttureControllo` e `2.2 Introduzione al C`.

## 1. Esecuzione di un `for`

```c
for (inizializzazione; condizione; aggiornamento) {
    // corpo del ciclo
}
```

1. **Inizializzazione**: una sola volta.
2. **Condizione**: prima di ogni iterazione; se falsa, il ciclo termina.
3. **Corpo**: eseguito quando la condizione è vera.
4. **Aggiornamento**: dopo il corpo, anche se nel corpo viene modificata la variabile di controllo.
5. Si torna al controllo della condizione.

### Diagramma di flusso

Il diagramma Mermaid è renderizzato direttamente da GitHub.

```mermaid
flowchart TD
    A["Inizializzazione"] --> B{"Condizione vera?"}
    B -- "Sì" --> C["Corpo del ciclo"]
    C --> D["Aggiornamento"]
    D --> B
    B -- "No" --> E(["Fine"])
```

### Esempio base

```c
int i;
for (i = 0; i < 5; i++) {
    printf("%d ", i);
}
```

**Output:** `0 1 2 3 4`

Il valore `5` non viene stampato: il confronto `5 < 5` è falso **prima** di eseguire il corpo.

## 2. `i++` contro `++i`

- `i++` (**post-incremento**): produce il valore corrente di `i`, poi la incrementa.
- `++i` (**pre-incremento**): incrementa `i`, poi produce il nuovo valore.

Partendo separatamente da `i = 3`:

| Espressione | Valore stampato | Valore finale di `i` |
|---|---:|---:|
| `printf("%d", i++);` | 3 | 4 |
| `printf("%d", ++i);` | 4 | 4 |

### Dettaglio importante: incremento nell'aggiornamento del `for`

Questi due cicli stampano entrambi `0 1 2 3 4`:

```c
for (int i = 0; i < 5; i++) {
    printf("%d ", i);
}

for (int i = 0; i < 5; ++i) {
    printf("%d ", i);
}
```

**Perché?** Il terzo campo del `for` viene sempre valutato **dopo** il corpo del ciclo. Quando l'aggiornamento consiste soltanto in `i++` o `++i`, il valore prodotto dall'espressione è scartato: interessa solo il suo effetto, cioè aumentare `i` di 1.

Questo **non** vuol dire che `++i` nell'intestazione venga eseguito prima del corpo: la posizione nel `for` determina *quando* si esegue l'aggiornamento, mentre pre/post-incremento determina il valore prodotto dall'espressione.

## 3. Tracciare i cicli con due aggiornamenti

### Esempio A — `i++` nel corpo e nell'aggiornamento

```c
int i, n = 10;
for (i = 0; i < n; i++) {
    printf("%d ", i++);
}
```

**Output:** `0 2 4 6 8`

| Iterazione | `i` all'inizio | Valore stampato | Dopo `i++` nel `printf` | Dopo l'aggiornamento del `for` |
|---|---:|---:|---:|---:|
| 1 | 0 | 0 | 1 | 2 |
| 2 | 2 | 2 | 3 | 4 |
| 3 | 4 | 4 | 5 | 6 |
| 4 | 6 | 6 | 7 | 8 |
| 5 | 8 | 8 | 9 | 10 |
| Fine | 10 | — | — | — |

### Esempio B — `++i` nel corpo e nell'aggiornamento

```c
int i;
for (i = 0; i < 5; ++i) {
    printf("%d ", ++i);
}
```

**Output:** `1 3 5`

| Iterazione | `i` al controllo | `i < 5` | Valore stampato dal `++i` | `i` dopo aggiornamento `for` |
|---|---:|---|---:|---:|
| 1 | 0 | Vero | 1 | 2 |
| 2 | 2 | Vero | 3 | 4 |
| 3 | 4 | Vero | 5 | 6 |
| Fine | 6 | Falso | — | — |

**Perché compare 5?** Con `i = 4` la condizione `4 < 5` è vera. Una volta entrati nel corpo, `++i` porta la variabile a 5 e la stampa. Solo successivamente l'aggiornamento del `for` la porta a 6.

### Esempio C — `i--` nel `printf`, `i += 2` nel `for`

```c
int i;
for (i = 0; i < 6; i += 2) {
    printf("%d ", i--);
}
```

**Output:** `0 1 2 3 4 5`

| Iterazione | `i` prima del `printf` | Stampa | `i` dopo `i--` | `i` dopo `i += 2` |
|---|---:|---:|---:|---:|
| 1 | 0 | 0 | -1 | 1 |
| 2 | 1 | 1 | 0 | 2 |
| 3 | 2 | 2 | 1 | 3 |
| 4 | 3 | 3 | 2 | 4 |
| 5 | 4 | 4 | 3 | 5 |
| 6 | 5 | 5 | 4 | 6 |
| Fine | 6 | — | — | — |

In questo caso l'effetto complessivo di ogni iterazione è `-1 + 2 = +1`.

## 4. `while` e `do-while`

| Costrutto | Momento del controllo | Numero minimo di esecuzioni del corpo |
|---|---|---:|
| `for` | Prima del corpo | 0 |
| `while` | Prima del corpo | 0 |
| `do-while` | Dopo il corpo | 1 |

```c
int i = 5;
while (i < 3) {
    printf("%d", i);
}
// Non stampa nulla.

do {
    printf("%d", i);
} while (i < 3);
// Stampa 5 una volta.
```

## 5. Checklist per gli esercizi «calcola l'output»

- Seguire l'ordine **condizione → corpo → aggiornamento → condizione**.
- Distinguere il **valore stampato** dal **valore successivo della variabile**.
- Controllare se il contatore cambia **anche nel corpo**.
- Distinguere `i++` e `++i` quando il loro valore è usato in un'espressione.
- Annotare anche **l'ultima condizione falsa**: spiega perché il ciclo termina.
- Nei cicli annidati, ricominciare a tracciare il ciclo interno per ogni iterazione di quello esterno.

> Nota C: esempi come `printf("%d %d", i++, ++i)` modificano senza sequenziamento la stessa variabile nella medesima espressione e hanno comportamento indefinito. Non attribuire loro un output «sicuro».

## 6. Riferimento all'esame

Nella [prima prova teorica del 22/10/2024](../../Prove_Teoriche/22102024%201_ProvaTeorica%20PROG1.pdf), la **domanda 2** richiede l'output di frammenti di codice con `for` e incrementi/decrementi, mentre la **domanda 3** richiede la stampa di una figura usando i cicli. Il procedimento consigliato per la domanda 2 è tracciare per ogni iterazione **valore in ingresso, stampa, modifica nel corpo e aggiornamento**.
