# Operazioni tra basi — appunti mirati per la teoria

**Materiale:** dispense **4.4 Operazioni e conversione tra basi diverse** e **4.3 Rappresentazione di informazioni**. Questa scheda raccoglie gli argomenti e gli esercizi affrontati durante lo studio: sistemi posizionali, conversioni tra basi 2/8/10/16, addizione binaria, sottrazione tramite complemento a 2 e lettura diretta di numeri *signed*.

**Convenzioni:** `(1010)₂` indica un numero in base 2, `(2A)₁₆` in base 16. Per gli interi con segno specifichiamo sempre **complemento a 2 (CA2)** e **numero di bit**: gli stessi bit hanno significati diversi se letti come *unsigned* oppure come CA2.

> **Provenienza degli esempi:** `19₁₀ → 10011₂`, `8F₁₆ → 217₈` e `-6` in CA2 su 4 bit compaiono nelle dispense. Gli altri esempi numerici sono **esercizi svolti durante la conversazione** (non trascrizioni di domande d'esame). Il metodo di lettura diretta dei pesi con il bit più significativo negativo è una **riformulazione didattica della codifica CA2**.

---

## 1. Sistemi posizionali: leggere un numero in base `b`

| Base | Nome | Cifre ammesse |
|---|---|---|
| 2 | Binario | `0`, `1` |
| 8 | Ottale | `0`–`7` |
| 10 | Decimale | `0`–`9` |
| 16 | Esadecimale | `0`–`9`, `A`–`F` |

In esadecimale le lettere rappresentano valori: `A=10`, `B=11`, `C=12`, `D=13`, `E=14`, `F=15`.

**Ogni cifra contribuisce con una potenza della base.** La cifra più a **destra** ha esponente `0`, quella accanto esponente `1`, poi `2`, ecc.:

```text
Cifre:           1    0    1    1    0
Potenze (base 2) 2^4  2^3  2^2  2^1  2^0
Contributi:      16    0     4    2    0
```

Quindi `(10110)₂ = 16 + 4 + 2 = (22)₁₀`.

**Da ricordare:** è **l'esponente** che parte da zero a destra, non il valore della cifra.

Altri esempi:

```text
(1101)₂ = 1·2^3 + 1·2^2 + 0·2^1 + 1·2^0 = 13₁₀
(2A)₁₆ = 2·16^1 + 10·16^0 = 42₁₀
(3A)₁₆ = 3·16^1 + 10·16^0 = 58₁₀
```

La stessa regola vale per qualunque base. La cifra scritta deve essere **minore della base**: `102₂` e `19₈` non sono numeri validi in quelle basi.

## 2. Da decimale a un'altra base: divisioni successive

Per convertire un **intero non negativo** dalla base 10 alla base `b`:

1. Dividi per `b` usando il quoziente intero.
2. Annota il **resto**.
3. Continua sul quoziente finché arrivi a zero.
4. **Leggi i resti dal basso verso l'alto** (dall'ultimo al primo).

### Esempio dalla dispensa 4.4: `19₁₀` in base 2

| Numero da dividere | Quoziente (`÷ 2`) | Resto |
|---:|---:|---:|
| 19 | 9 | 1 |
| 9 | 4 | 1 |
| 4 | 2 | 0 |
| 2 | 1 | 0 |
| 1 | 0 | 1 |

**Resti in ordine inverso: `10011`.**

`(19)₁₀ = (10011)₂`, verificabile come `16 + 2 + 1 = 19`.

### Esempio: `42₁₀` in base 16

| Numero | Quoziente (`÷ 16`) | Resto |
|---:|---:|---|
| 42 | 2 | 10 = `A` |
| 2 | 0 | 2 |

Si legge `2A`: **`(42)₁₀ = (2A)₁₆`**.

**Trappola collegata ai cicli:** se salvi i resti in un array iniziando dall'indice `0`, l'indice dopo l'ultima memorizzazione punta alla prima posizione **non** usata; per stamparli all'indietro devi partire dall'ultima posizione effettivamente riempita. La dispensa 4.4 affronta anche gli algoritmi di conversione.

## 3. Conversioni dirette tra binario, ottale ed esadecimale

**Il trucco funziona perché `8 = 2³` e `16 = 2⁴`:**
- **Una cifra ottale = un gruppo di 3 bit.**
- **Una cifra esadecimale = un gruppo di 4 bit.**

Non è necessario convertire prima l'intero numero in decimale.

### Binario → esadecimale: gruppi di 4 bit

```text
10110110₂
  1011 0110        (raggruppa da destra)
     B    6
= B6₁₆
```

Ogni gruppo è un numero fra 0 e 15 e corrisponde a **una** cifra esadecimale.

Un altro esempio risolto:

```text
110101₂
0011 0101₂         (aggiungi zeri solo a sinistra)
   3    5
= 35₁₆
```

**Attenzione:** `35₁₆` **non** è `35₁₀`; vale `3·16 + 5 = 53` in decimale.

### Binario → ottale: gruppi di 3 bit

Sul medesimo numero `10110110₂`:

```text
 10110110
010 110 110        (uno zero a sinistra per completare i gruppi)
  2   6   6
= 266₈
```

Perciò **`10110110₂ = 266₈ = B6₁₆`**.

### Esadecimale → binario: ogni cifra diventa 4 bit

```text
A7₁₆
 A       7
1010    0111
= 10100111₂
```

È essenziale mantenere **tutti e quattro i bit** di ogni gruppo interno: `7 = 0111`, non soltanto `111`.

Altri esempi:

```text
3A₁₆ = 0011 1010₂ = 111010₂
F₁₆  = 1111₂
```

Gli **zeri iniziali** della rappresentazione complessiva si possono eliminare, perché non cambiano il valore.

### Ottale → binario: ogni cifra diventa 3 bit

```text
753₈
 7   5   3
111 101 011
= 111101011₂
```

### Ottale → esadecimale passando dal BINARIO

L'esempio completo discusso:

```text
753₈
 ↓ ogni cifra in 3 bit
111 101 011₂
 ↓ riunisci e raggruppa da destra in gruppi di 4
0001 1110 1011₂
  1    E    B
= 1EB₁₆
```

Un esempio presente nelle slide 4.4 è:

```text
8F₁₆ = 1000 1111₂ = 010 001 111₂ = 217₈
```

**Schema rapido:**

| Conversione | Metodo |
|---|---|
| Base qualunque → decimale | Somma delle cifre × potenze, partendo da `b^0` a destra |
| Decimale → altra base | Divisioni per la nuova base, resti letti al contrario |
| Binario → ottale | Gruppi di **3** bit da destra |
| Binario → esadecimale | Gruppi di **4** bit da destra |
| Ottale → binario | Una cifra → **3** bit |
| Esadecimale → binario | Una cifra → **4** bit |
| Ottale ↔ esadecimale | Passaggio tramite il **binario** e nuovo raggruppamento |

**Regola dello zero:** per completare i gruppi aggiungi zeri **a sinistra** per gli interi non negativi. Non aggiungerli a destra: cambierebbero il valore.

## 4. Addizione binaria: somma e riporto

La dispensa 4.4 tratta l'aritmetica binaria, comprese le addizioni.

| Bit A | Bit B | Riporto in ingresso | Bit risultato | Riporto in uscita |
|---:|---:|---:|---:|---:|
| 0 | 0 | 0 | 0 | 0 |
| 0 | 1 | 0 | 1 | 0 |
| 1 | 0 | 0 | 1 | 0 |
| 1 | 1 | 0 | 0 | 1 |
| 1 | 1 | 1 | 1 | 1 |

**In particolare:** `1 + 1 = 10₂`, quindi **scrivi 0 e riporti 1**; `1 + 1 + 1 = 11₂`, quindi **scrivi 1 e riporti 1**.

Si somma da destra verso sinistra. Esempio illustrativo:

```text
   00110110    = 54₁₀
 + 01110100    = 116₁₀
   --------
   10101010    = 170₁₀
```

Qui i bit sono interpretati come **unsigned** (numeri non negativi). Interpretare il risultato come numero con segno richiede sapere qual è la codifica e quanti bit si usano.

## 5. Complemento a 2: come rappresentare un numero negativo

Nella dispensa **4.3 Rappresentazione di informazioni** è introdotto il **complemento a 2 (CA2)** per gli interi relativi.

Per ottenere **`-x` su `n` bit**, partendo dalla rappresentazione di `+x` su **esattamente `n` bit**:

1. Scrivi il numero positivo in binario su `n` bit.
2. **Inverti** ogni bit (`0↔1`).
3. **Aggiungi 1**.

### Esempio della dispensa 4.3: `-6` su 4 bit

```text
+6:                 0110
Bit invertiti:      1001
Aggiungi 1:         1010
```

Quindi **`1010` rappresenta `-6` in CA2 su 4 bit**.

**Attenzione:** `1010₂` come numero **unsigned** vale `10`, ma la **stessa sequenza di 4 bit in CA2** vale `-6`. La differenza dipende dall'interpretazione dei bit, non dalla loro scrittura.

### Esempio: `-5` su 8 bit

```text
+5:                 00000101
Bit invertiti:      11111010
Aggiungi 1:         11111011
```

Quindi **`11111011` è `-5` in CA2 su 8 bit**.

## 6. Leggere DIRETTAMENTE un numero signed (CA2), senza passare dall'unsigned

**Metodo dei pesi:** in CA2, il bit più a **sinistra** ha **peso negativo**. Gli altri bit hanno i pesi positivi usuali.

Per una parola di `n` bit, da destra verso sinistra i pesi sono:

```text
+2^0, +2^1, +2^2, ..., +2^(n-2), -2^(n-1)
```

**Per un numero CA2 su 4 bit:**

```text
Bit:         1    0    1    0
Peso:       -8   +4   +2   +1
Contributo: -8    0   +2    0
Totale = -8 + 2 = -6
```

**Per un numero CA2 su 8 bit:**

```text
Bit:        1     1    1    1    1    0    1    1
Peso:      -128  +64  +32  +16   +8  +4   +2   +1
Contributo:-128  +64  +32  +16   +8   0   +2   +1
Totale = -5
```

**Se il bit più significativo è `0`,** il numero CA2 non è negativo e il calcolo coincide con il binario non negativo. **Se è `1`,** includi il suo peso negativo.

(Questo **metodo rapido dei pesi** è una riformulazione didattica delle regole CA2; nelle slide 4.3 il tema compare come **codifica/decodifica del complemento a 2**.)

### Intervallo dei numeri con segno

Con `n` bit, nel CA2:

`min = -2^(n-1)` e `max = 2^(n-1) - 1`.

| Bit | Valore minimo | Valore massimo |
|---:|---:|---:|
| 4 | -8 | +7 |
| 8 | -128 | +127 |
| 16 | -32768 | +32767 |

Esempio: con 4 bit **`+8` non è rappresentabile in CA2**, mentre `-8` sì.

## 7. Sottrazione come addizione in complemento a 2

La sottrazione di due interi a larghezza fissata si può eseguire come:

`A - B = A + (-B)`.

**Esempio: `6 - 3` su 4 bit.**

```text
+6 = 0110
+3 = 0011
-3 = 1101       (inverti 0011 -> 1100, poi +1)

    0110
  + 1101
  ------
  1 0011
```

Si conservano solo i **4 bit** previsti, scartando il riporto esterno:

`0011₂ = +3`, quindi **`6 - 3 = 3`**.

**Nota:** il riporto esterno e l'**overflow signed** sono concetti diversi; scartare il riporto non permette da solo di decidere se il risultato CA2 è rappresentabile. (**Distinzione menzionata per completezza, ma la rilevazione dell'overflow non è stata ancora affrontata negli esercizi di questa scheda.**)

## 8. Errori da evitare nella prova teorica

- **Conversione verso base 10:** iniziare dai pesi `b^0` a **destra**, non a sinistra.
- **Conversione dalla base 10:** leggere i **resti in ordine inverso**.
- **Raggruppamento:** in binario → ottale/esadecimale, partire **da destra**.
- **Padding:** aggiungere gli zeri **a sinistra**, non a destra, quando si completano gruppi di bit per numeri non negativi.
- **Esadecimale:** ricordare che `A=10`, `B=11`, `C=12`, `D=13`, `E=14`, `F=15`.
- **Gruppi fissi:** da esadecimale ogni cifra richiede **4 bit**, da ottale **3 bit**; preservare eventuali zeri iniziali nel gruppo.
- **CA2:** indicare sempre il **numero di bit** e l'interpretazione *signed*; i bit da soli non bastano.
- **Lettura CA2 diretta:** solo il **bit più a sinistra** ha peso negativo.
- **Operazioni su `n` bit:** il riporto esterno viene scartato, ma l'eventuale overflow va controllato separatamente.

## 9. Esercizi svolti e controllo del risultato

| Esercizio | Soluzione |
|---|---|
| `(45)₁₀` → base 2 | `(101101)₂` (`32+8+4+1`) |
| `(110010)₂` → base 10 | `50` (`32+16+2`) |
| `(110101)₂` → base 16 | `35₁₆` |
| `(A7)₁₆` → base 2 | `10100111₂` |
| `(753)₈` → base 16 | `1EB₁₆` |
| `(-6)₁₀` → CA2 su 4 bit | `1010` |
| `(-5)₁₀` → CA2 su 8 bit | `11111011` |
| `(1010)` CA2 su 4 bit → decimale | `-6` |
| `(11111011)` CA2 su 8 bit → decimale | `-5` |
| `6 - 3` via CA2 su 4 bit | `0110 + 1101 = 0011` (riporto esterno scartato) |

## 10. Riferimenti e prossimi argomenti

- **4.4 Operazioni e conversione tra basi diverse:** sistemi posizionali, conversioni 2/8/10/16, raggruppamento, addizione binaria e algoritmi di conversione.
- **4.3 Rappresentazione di informazioni:** rappresentazioni degli interi relativi, complemento a 2, codifica e decodifica dei numeri con segno.
- **Ancora da approfondire:** riconoscimento dell'**overflow** nelle operazioni signed; ulteriori rappresentazioni presenti nella dispensa 4.3 (per esempio la virgola mobile); altri esercizi di aritmetica binaria. La scheda non pretende di avere già trattato questi argomenti.

**Criterio di preparazione:** priorità alle tecniche della dispensa e agli esercizi effettivamente svolti insieme; i dettagli extra sono indicati esplicitamente come approfondimenti.
