# Operazioni tra basi — appunti mirati per la teoria

**Materiale:** dispense **4.4 Operazioni e conversione tra basi diverse** e **4.3 Rappresentazione di informazioni**. Questa scheda raccoglie gli argomenti e gli esercizi affrontati durante lo studio: sistemi posizionali, conversioni 2/8/10/16, somme e moltiplicazioni binarie, signed/unsigned, complemento a 2, overflow, numeri frazionari, virgola fissa e virgola mobile (modello semplificato e IEEE 754).

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

**Nota:** il riporto esterno e l'**overflow signed** sono concetti diversi; scartare il riporto non permette da solo di decidere se il risultato CA2 è rappresentabile. (**La rilevazione dell'overflow è spiegata nella sezione 9.**)


## 8. Somme tra numeri scritti in basi diverse

**Procedura:** porta i due operandi nella *stessa base*, sommali e converti il risultato nella base richiesta.

**Esempio: `(1011)₂ + (17)₈`, risultato esadecimale.**

~~~text
(1011)₂ = 8+2+1 = 11₁₀
(17)₈   = 1·8+7 = 15₁₀
11+15 = 26₁₀

26 ÷ 16 = 1, resto 10 = A
 1 ÷ 16 = 0, resto  1

Dal basso verso l'alto: 1A₁₆.
~~~

La seconda divisione **non va omessa** se stai applicando la regola generale dei resti. La scorciatoia (scrivere il quoziente finale non nullo seguito dall'ultimo resto) funziona quando quel quoziente è inferiore alla base.

**Esercizio svolto: `(110101)₂ + (37)₈`.**

~~~text
(110101)₂ = 32+16+4+1 = 53₁₀
(37)₈     = 3·8+7     = 31₁₀
53+31 = 84₁₀

84 ÷ 16 = 5, resto 4
 5 ÷ 16 = 0, resto 5

Risultato: 54₁₆ (verifica: 5·16+4=84)
~~~

Il risultato **non è `44₁₆`**: nella seconda divisione il resto è 5.

## 9. Signed, unsigned, numero minimo di bit e overflow

Su `n` bit:

| Codifica | Minimo | Massimo |
|---|---|---|
| **Unsigned** | 0 | `2^n−1` |
| **Signed in CA2** | `−2^(n−1)` | `2^(n−1)−1` |

**Esempio su 4 bit:** unsigned da **0 a 15**, signed CA2 da **−8 a +7**.

| Bit | Unsigned | Signed CA2 |
|---|---:|---:|
| `0111` | 7 | +7 |
| `1000` | 8 | −8 |
| `1101` | 13 | −3 |
| `1111` | 15 | −1 |

**Numero minimo di bit:** +20 non entra in CA2 su 5 bit (massimo +15), ma entra su 6 bit (massimo +31). Anche −20 richiede 6 bit. **−8 entra su 4 bit, +8 richiede 5 bit.**

### Overflow signed

C'è **overflow** quando il risultato matematico è fuori dall'intervallo rappresentabile. Per una **somma in CA2**, se gli operandi hanno lo stesso segno ma il risultato su n bit ha segno diverso, si verifica overflow. Se i due operandi hanno segni diversi, una semplice addizione non provoca overflow signed.

**Esempio con overflow, CA2 su 4 bit:**

~~~text
  0111 = +7
+ 0011 = +3
------
  1010 = −6 se interpretato come CA2 su 4 bit
~~~

Il vero risultato è **+10**, fuori dall'intervallo **−8…+7**.

**Esempio senza overflow, ma con riporto esterno:**

~~~text
  1101 = −3
+ 1011 = −5
------
1 1000  ← somma binaria completa: 11000
  1000  ← scarto il riporto esterno: −8 su 4 bit
~~~

Risultato **−8**, che è rappresentabile; quindi **nessun overflow**. **Riporto esterno e overflow non sono la stessa cosa.**

### Operazioni unsigned su larghezza fissa

L'aritmetica unsigned su `n` bit dà il risultato **modulo `2^n`**:

~~~text
  1111  = 15 unsigned su 4 bit
+ 0001  = 1
------
1 0000
  0000  = 0 su 4 bit (16 modulo 16)
~~~

**Precisazione sul linguaggio C:** il calcolo unsigned è modulo `2^n`; invece l'**overflow in un'espressione signed è comportamento indefinito**. Gli esempi CA2 su 4 bit illustrano l'aritmetica a larghezza fissa, non garantiscono che il C produca sempre quel risultato in caso di overflow.

## 10. Moltiplicazioni binarie e shift sinistro

Per ogni bit `1` del moltiplicatore copia il moltiplicando, spostandolo a sinistra secondo la posizione del bit; per un bit `0` aggiungi una riga nulla. Poi **somma i prodotti parziali allineati a destra**.

**Esempio `1011₂ × 101₂`:**

~~~text
        1011 = 11₁₀
      ×  101 = 5₁₀
      ------
        1011        (×1)
       0000         (×0, spostato di 1)
    + 101100        (×1, spostato di 2)
      ------
      110111 = 55₁₀
~~~

**Esempio `1101₂ × 11₂` (l'esercizio in cui si era sbagliata la somma):**

~~~text
        1101 = 13
      ×   11 = 3
      ------
      001101       (13)
    + 011010       (13 spostato di 1 = 26)
      ------
      100111       (39₁₀)
~~~

**Risultato `100111₂`, non `10111₂`.** Verifica: `13×3=39=32+4+2+1`.

### Operatore C `<<`

Lo **shift sinistro** di `k` posizioni, per valori non negativi e risultati rappresentabili, equivale a moltiplicare per `2^k`:

~~~c
unsigned int x = 13;
// 13 = 001101₂
x << 1;  // 26 = 011010₂
x << 2;  // 52 = 110100₂
~~~

Per gli unsigned di n bit il risultato dello shift sinistro è modulo `2^n`, se il numero di posizioni è valido. Per gli signed in C le regole sono più restrittive: non trattare indiscriminatamente lo shift di numeri negativi come una moltiplicazione ordinaria.

## 11. Numeri frazionari in binario: lettura e conversione

Le potenze **a destra della virgola** hanno esponente negativo:

| Posizione dopo la virgola | Peso |
|---|---|
| Prima | `2^−1 = 0,5` |
| Seconda | `2^−2 = 0,25` |
| Terza | `2^−3 = 0,125` |
| Quarta | `2^−4 = 0,0625` |

**Esempio `(101.101)₂`:** parte intera `101₂=5`; parte frazionaria `.101₂=0,5+0,125=0,625`. Totale **`5,625₁₀`**.

**Esempio `(1101.011)₂`:** parte intera `1101₂=13`; parte frazionaria `.011₂=0·0,5+1·0,25+1·0,125=0,375`. Totale **`13,375₁₀`**, non 13,75.

**Per convertire da decimale a binario** usa:
- **Parte intera:** divisioni successive per 2, resti letti **dal basso verso l'alto**.
- **Parte frazionaria:** moltiplicazioni successive per 2, ogni volta annoti la *parte intera*, in ordine **dall'alto verso il basso**; prosegui con la parte frazionaria residua.

**Esempio `0,625₁₀`:**

| Moltiplicazione | Cifra estratta | Frazione residua |
|---|---:|---:|
| `0,625×2=1,25` | 1 | 0,25 |
| `0,25×2=0,5` | 0 | 0,5 |
| `0,5×2=1,0` | 1 | 0 |

Risultato **`0.101₂`**.

**Esempi utili:**

| Decimale | Binario | Motivazione |
|---|---|---|
| 0,5 | `0.1₂` | `2^−1` |
| 0,25 | `0.01₂` | `2^−2` |
| 0,125 | `0.001₂` | `2^−3` |
| 0,75 | `0.11₂` | `0,5+0,25` |
| 0,875 | `0.111₂` | `0,5+0,25+0,125` |
| **2,5** | **`10.1₂`** | `2→10₂` e `0,5→.1₂` |
| **3,75** | **`11.11₂`** | `3→11₂` e `0,75→.11₂` |
| **0,375** | **`0.011₂`** | `0,25+0,125` |
| **6,25** | **`110.01₂`** | `6→110₂` e `0,25→.01₂` |

(Precisazione generale: alcune frazioni decimali non hanno un'espansione binaria finita; in tali casi servono approssimazione e arrotondamento, non esercitati qui.)

## 12. Virgola fissa

La posizione della virgola **è stabilita dalla codifica**, non viene conservato un carattere «virgola» nella memoria.

Esempio di formato a **8 bit**: 4 bit per la parte intera, 4 per la parte frazionaria (numero non negativo):

~~~text
1001.1010
1001₂ = 9
.1010₂ = 0,5+0,125 = 0,625
Valore = 9,625₁₀
~~~

## 13. Virgola mobile: modello semplificato della dispensa 4.3

Una rappresentazione *floating point* memorizza separatamente **segno, esponente e mantissa**, usando una potenza di 2 per spostare la virgola.

**Nel modello semplificato degli esempi iniziali della dispensa**:
- **Segno:** 1 bit (`0` = positivo; `1` = negativo).
- **Esponente:** 8 bit **in complemento a 2**.
- **Mantissa:** 23 bit, che rappresentano una frazione della forma **`0.xxx₂`**.

Formula: **`X = (−1)^s × m × 2^e`**.

**Esempio `+5`:**

~~~text
5₁₀ = 101₂ = 0.101₂ × 2^3
Segno:     0
Esponente: 00000011 (+3)
Mantissa:  10100000000000000000000
Verifica:  0,625 × 8 = 5
~~~

**Esempio `−2,5`:**

~~~text
2,5₁₀ = 10.1₂ = 0.101₂ × 2^2
                   ↑ la virgola è stata spostata
                     DUE volte verso sinistra
Segno:     1
Esponente: 00000010 (+2)
Mantissa:  10100000000000000000000
32 bit:    1 | 00000010 | 10100000000000000000000
Verifica: −0,625 × 4 = −2,5
~~~

**Esempio `+3,75`:**

~~~text
3,75₁₀ = 11.11₂ = 0.1111₂ × 2^2
Segno:     0
Esponente: 00000010 (+2)
Mantissa:  11110000000000000000000
Verifica:  0,9375 × 4 = 3,75
~~~

In questo **modello semplificato**, un esponente reale di **−2** è scritto in CA2 su 8 bit come **`11111110`**. Non si mette un simbolo `−` davanti al campo.

## 14. IEEE 754 reale, binary32: segno, bias e 1 implicito

**Attenzione: il modello semplificato e IEEE 754 non usano le stesse codifiche**, anche se occupano entrambi 32 bit.

Per i numeri **normali** IEEE 754 a 32 bit (*binary32*):

| Campo | Bit | Significato |
|---|---:|---|
| Segno `s` | 1 | `0` positivo, `1` negativo |
| Esponente `E` | 8 | Esponente reale **`e=E−127`**, cioè bias 127 |
| Frazione `f` | 23 | Bit dopo la virgola in **`1.f`**: l'`1` iniziale è **implicito** |

Formula: **`X=(−1)^s × (1.f)₂ × 2^(E−127)`**.

### 14.1 Normalizzazione

Qui bisogna ottenere la forma **`1.xxx₂ × 2^e`**, e NON `0.xxx₂`.

~~~text
11.11₂   = 1.111₂ × 2^1      (virgola a sinistra di 1)
110.01₂  = 1.1001₂ × 2^2     (virgola a sinistra di 2)
0.011₂   = 1.1₂ × 2^(−2)     (virgola a destra di 2)
0.00101₂ = 1.01₂ × 2^(−3)    (virgola a destra di 3)
~~~

- Virgola verso **sinistra** → esponente positivo.
- Virgola verso **destra** → esponente negativo.
- `011₂` come intero vale `11₂`: lo zero iniziale non cambia il valore, quindi `011₂ = 1.1₂×2^1`.
- `0.011₂` invece è **frazionario** e vale `1.1₂×2^−2`.

Per esempio, sullo stesso `3,75`:
- modello semplificato: `11.11₂ = 0.1111₂ × 2^2`;
- IEEE normale: `11.11₂ = 1.111₂ × 2^1`.

### 14.2 Esponente con bias 127

**Per codificare:** `E=e+127`, poi scrivi `E` su **8 bit**.
**Per decodificare:** converti `E` in decimale e fai `e=E−127`.

| Esponente reale | `e+127` | Campo su 8 bit |
|---:|---:|---|
| +3 | 130 | `10000010` |
| +2 | 129 | `10000001` |
| +1 | 128 | `10000000` |
| 0 | 127 | `01111111` |
| −1 | 126 | `01111110` |
| **−2** | **125** | **`01111101`** |
| −3 | 124 | `01111100` |
| **−5** | **122** | **`01111010`** |

Per esempio: `−5 + 127 = 122`, `122₁₀ = 1111010₂`, ma il campo ha 8 bit: **`01111010`**.

**Il bit di segno iniziale riguarda il numero, NON il segno dell'esponente.** Nell'IEEE 754 normale l'esponente negativo viene rappresentato tramite il bias, non con il CA2.

### 14.3 Codifica completa: decimale → IEEE 754 binary32

**`0,375`:**

~~~text
0,375₁₀ = 0.011₂ = 1.1₂ × 2^(−2)
Segno:       0
Esponente:  −2+127=125 = 01111101
Frazione:   10000000000000000000000 (1 implicito NON scritto)
Finale:     0 | 01111101 | 10000000000000000000000
32 bit:     00111110110000000000000000000000
Verifica:  1,5 × 0,25 = 0,375
~~~

**`−6,25`:**

~~~text
6,25₁₀ = 110.01₂ = 1.1001₂ × 2^2
Segno:       1
Esponente:  +2+127=129 = 10000001
Frazione:   10010000000000000000000
Finale:     1 | 10000001 | 10010000000000000000000
Verifica: −1,5625 × 4 = −6,25
~~~

Se l'esponente fosse `10000010` (=130, esponente reale +3) con la stessa frazione, il numero rappresentato sarebbe **−12,5**, non −6,25.

**`+3,75`:**

~~~text
3,75₁₀ = 11.11₂ = 1.111₂ × 2^1
Segno:       0
Esponente:  +1+127=128 = 10000000
Frazione:   11100000000000000000000
Finale:     0 | 10000000 | 11100000000000000000000
~~~

### 14.4 Decodifica: IEEE 754 → decimale

**`0 | 10000001 | 01000000000000000000000`:**

~~~text
Segno:       0 = positivo
Esponente:  10000001₂ = 129 → e = 129−127 = +2
Frazione:   010000... → significando 1.01₂ = 1,25
Valore:     +1,25 × 2^2 = +5
~~~

Il numero vale **+5**, non 1,25: devi sempre **applicare l'esponente** al significando.

**`1 | 01111110 | 10000000000000000000000`:**

~~~text
Segno:       1 = negativo
Esponente:  01111110₂ = 126 → e = 126−127 = −1
Frazione:   100000... → significando 1.1₂ = 1,5
Valore:     −1,5 × 2^(−1) = −0,75
~~~

Il numero vale **−0,75**, non −6. L'esponente memorizzato `01111110` non è +2.

### 14.5 Differenza da memorizzare

| | Modello semplificato 4.3 | IEEE 754 binary32 normale |
|---|---|---|
| Mantissa/significando | **`0.xxx`** | **`1.xxx`**, 1 implicito |
| Esponente | **CA2** su 8 bit | **Bias 127** su 8 bit |
| Esempio esponente reale −2 | `11111110` | `01111101` |

**Casi particolari IEEE 754:** `E=00000000` si usa per zero e numeri subnormali; `E=11111111` per infiniti e NaN. La formula sopra riguarda solo i **numeri normali**. (Questa è una precisazione di completezza: nella sessione non abbiamo svolto esercizi sui casi speciali.)

## 15. Riepilogo operativo degli errori da evitare

- Resti delle divisioni: **dal basso verso l'alto**; cifre delle frazioni da moltiplicazioni per 2: **dall'alto verso il basso**.
- Gruppi binari: **3 bit** per ottale, **4 bit** per esadecimale, partendo **da destra** per il numero intero.
- Somme fra basi diverse: prima **converti alla stessa base**.
- Su n bit: **signed e unsigned sono interpretazioni diverse**. In CA2 il bit più a sinistra ha peso negativo.
- **Riporto ≠ overflow signed.** In CA2 −3−5 su 4 bit dà `1000` (−8) senza overflow anche se la somma completa è `11000`.
- Moltiplicazioni binarie: **sposta e allinea** i prodotti parziali, poi somma tutti i bit e riporti.
- Pesi dopo la virgola: **0,5; 0,25; 0,125; 0,0625…**.
- Virgola mobile semplificata: **`0.xxx`**, esponente **CA2**.
- IEEE 754 normale: **`1.xxx`**, 1 iniziale implicito, esponente **con bias 127**.
- In IEEE 754: conta gli spostamenti per normalizzare, codifica l'esponente su **8 bit**, e **moltiplica il significando per `2^e`** prima di dare il risultato decimale.
- L'esponente del numero IEEE 754 può essere negativo anche se il **campo esponente memorizzato non ha un bit di segno**.

## 16. Esercizi e risultati da ripassare

| Esercizio | Risultato |
|---|---|
| `(110101)₂ + (37)₈` → esadecimale | **`54₁₆`** |
| `26₁₀` → esadecimale | **`1A₁₆`** |
| `1101₂ × 11₂` | **`100111₂`** (39) |
| `1011₂ × 101₂` | **`110111₂`** (55) |
| `7+3` CA2 4 bit | `1010`, **overflow** |
| `−3−5` CA2 4 bit | `1000` (−8), **nessun overflow** |
| `1111 + 0001` unsigned 4 bit | `0000` modulo 16 |
| `1101.011₂` → decimale | **13,375** |
| `2,5₁₀` → binario | `10.1₂` |
| `3,75₁₀` → binario | `11.11₂` |
| `0,375₁₀` → binario | `0.011₂` |
| `−2,5`, modello semplificato | `1 / 00000010 / 10100000000000000000000` |
| `3,75`, modello semplificato | `0 / 00000010 / 11110000000000000000000` |
| `0,375`, IEEE 754 binary32 | `0 / 01111101 / 10000000000000000000000` |
| `−6,25`, IEEE 754 binary32 | `1 / 10000001 / 10010000000000000000000` |
| `0 / 10000001 / 01000000000000000000000`, IEEE | **+5** |
| `1 / 01111110 / 10000000000000000000000`, IEEE | **−0,75** |

## 17. Riferimenti e ambito della scheda

- **Dispensa 4.4 — Operazioni e conversioni tra basi:** basi 2/8/10/16, conversioni, addizioni, moltiplicazioni e shift.
- **Dispensa 4.3 — Rappresentazione delle informazioni:** CA2, frazioni, virgola fissa, modello semplificato di virgola mobile e introduzione a IEEE 754.
- Gli esempi sono **quelli svolti nella conversazione o esempi illustrativi**: non vanno considerati domande d'esame trascritte.
- Le precisazioni su **comportamento dell'overflow in C**, **casi speciali IEEE 754** e **frazioni senza sviluppo binario finito** sono approfondimenti di contesto, non esercizi completati durante la sessione.

**Copertura attuale:** conversioni tra basi intere, somme e moltiplicazioni binarie, signed/unsigned, complemento a 2, overflow, numeri binari con virgola, rappresentazione a virgola fissa, modello semplificato della dispensa e IEEE 754 binary32 per numeri normali (codifica e decodifica).
