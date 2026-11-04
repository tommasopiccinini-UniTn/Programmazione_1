# Tipi di dati in C — Scheda riassuntiva

**Dispensa:** 5.1 Tipi di dati, in particolare la sezione *Conversioni di tipo* e la slide *Conversione implicita — Regole* (pagina 93 del PDF). Esercizio collegato: [prova del 10/02/2025, domanda 5](../../Prove_Teoriche/100225%20ProvaTeorica%20PROG1.pdf).

## Che cos'è un tipo

Il **tipo** di una variabile stabilisce quali valori può rappresentare, quali operazioni si possono fare e come interpretare i dati conservati in memoria. La **dichiarazione** associa un tipo a un nome; l'**inizializzazione** assegna il primo valore.

~~~c
int eta = 20;       // tipo int, valore iniziale 20
double media = 2.5; // tipo double
char lettera = 'A'; // tipo char
~~~

Una variabile locale automatica non inizializzata non ha un valore iniziale determinato. Le variabili globali e quelle dichiarate `static` vengono invece inizializzate a zero se non si specifica un inizializzatore.

## Tipi fondamentali

| Tipo | Uso principale | Esempio |
|---|---|---|
| `char` | Carattere o piccolo valore intero | `char c = 'A';` |
| `short` (`short int`) | Intero con intervallo generalmente più piccolo di `int` | `short n = 12;` |
| `int` | Intero | `int n = 120;` |
| `long` (`long int`) | Intero con intervallo almeno pari a quello di `int` | `long n = 100000L;` |
| `long long` | Intero con intervallo almeno pari a quello di `long` | `long long n = 100000LL;` |
| `float` | Numero reale a precisione finita | `float x = 1.5f;` |
| `double` | Numero reale con precisione generalmente maggiore di `float` | `double x = 1.5;` |
| `long double` | Numero reale con precisione almeno pari a `double` | `long double x = 1.5L;` |
| `void` | Assenza di valore di ritorno o di parametri, nei contesti appropriati | `void stampa(void);` |

`short` e `short int` sono due scritture dello **stesso tipo**; lo stesso vale per `long` e `long int`. I qualificatori `signed` e `unsigned` distinguono gli interi con segno da quelli senza segno. Un `unsigned` non rappresenta valori negativi; a parità di spazio può rappresentare valori positivi più grandi. Il fatto che il semplice `char` si comporti come `signed char` o `unsigned char` dipende dall'implementazione.

Per i valori di verità, in C si può usare `_Bool` (o `bool` includendo `<stdbool.h>` nelle versioni che lo forniscono). Array, `struct`, puntatori ed `enum` sono altri tipi costruiti o definiti a partire dai tipi di base.

## Dimensioni: cosa è garantito e cosa no

`sizeof` restituisce la dimensione in **byte** dell'oggetto o del tipo. Un `char` occupa sempre **1 byte C**; il numero di bit in un byte si può leggere tramite `CHAR_BIT` in `<limits.h>` (spesso 8). La dimensione degli altri tipi dipende dal sistema e dal compilatore. Per gli interi vale almeno questo ordinamento:

~~~text
sizeof(char) <= sizeof(short) <= sizeof(int) <= sizeof(long) <= sizeof(long long)
~~~

Le uguaglianze sono possibili: `long` non è necessariamente più grande di `int`. Negli esercizi che chiedono un numero di byte, usare le dimensioni date nel testo; se non sono note, lasciare la risposta come `sizeof(tipo)`.

**Attenzione:** `sizeof(espressione)` misura il **tipo dell'espressione completa**; non somma le dimensioni dei suoi operandi. In un caso ordinario come `sizeof(c + i)`, non serve eseguire la somma per conoscerne il tipo.

## Conversioni implicite nelle operazioni aritmetiche

Quando gli operandi hanno tipi diversi, C può convertirli temporaneamente per eseguire l'operazione. Le variabili originali mantengono il loro tipo dichiarato.

1. **Promozioni intere:** `char` e `short`, anche nelle versioni senza segno, diventano `int` se `int` può rappresentarne tutti i valori; altrimenti diventano `unsigned int`.
2. **Tipo comune:** dopo le promozioni, gli operandi vengono convertiti a un tipo comune secondo le regole delle conversioni aritmetiche usuali. Nei semplici casi con segno studiati qui, `int + long` produce `long` e `long + long long` produce `long long`.
3. **Risultato:** l'espressione ha il tipo comune. Un'eventuale assegnazione successiva può convertirne di nuovo il valore nel tipo della variabile di destinazione.

### Schema rapido per interi con segno

Assumiamo `char` da 1 byte, `short` da 2, `int` da 4 e `long` da 8, con tutti i valori di `char` e `short` rappresentabili da `int`.

| Espressione | Dopo le promozioni | Tipo del risultato | `sizeof` del risultato nell'esempio |
|---|---|---|---:|
| `char + char` | `int + int` | `int` | 4 byte |
| `short + char` | `int + int` | `int` | 4 byte |
| `short + short` | `int + int` | `int` | 4 byte |
| `long + char` | `long + int` | `long` | 8 byte |
| `long + short` | `long + int` | `long` | 8 byte |
| `int + long` | `int + long` | `long` | 8 byte |

Il punto spesso inatteso è **`short + short`**: il risultato ha tipo `int`, anche se entrambi gli operandi sono `short`. Una somma come `30000 + 30000` non starebbe in uno `short` con segno da 2 byte, mentre sta in un `int` da 4 byte.

### Caso della prova: `sizeof(c + i)`

~~~c
short int i = 20;
char c = 97;
// Supponiamo sizeof(short) == 2 e sizeof(int) == 4.
sizeof(i);       // 2
sizeof(c);       // 1
sizeof(c + i);   // 4: char e short sono promossi a int
~~~

`97 + 20` vale `117`, ma per rispondere a `sizeof(c + i)` serve conoscere **il tipo del risultato**, cioè `int`. Non si calcola `1 + 2` byte e non si passa automaticamente a `long`.

### Tipi senza segno e reali

Con `unsigned` la regola **non è sempre “vince il tipo con più byte”**. Per esempio, `int + unsigned int` produce `unsigned int`; in combinazioni come `long + unsigned int` occorre confrontare i rispettivi intervalli di valori. La gerarchia lineare riportata nella dispensa è utile come prima approssimazione, ma non copre ogni caso di segno e dimensione.

Se un operando è reale, una conversione può rendere reale anche l'altro: `int + float` ha tipo `float`, `float + double` ha tipo `double`. Un cast permette di richiedere esplicitamente la conversione prima dell'operazione:

~~~c
int a = 5, b = 2;
float x = a / b;          // divisione intera: x diventa 2.0
float y = (float)a / b;   // divisione reale: y diventa 2.5
~~~

## Conversione nell'assegnazione

L'espressione a destra di `=` viene prima valutata secondo le sue regole. Poi il risultato viene convertito nel tipo della variabile a sinistra, se necessario. Questa seconda conversione può perdere informazioni.

~~~c
int n = 3;
double d = n;   // d vale 3.0
n = 2.9;        // n vale 2: la parte frazionaria è troncata
~~~

**Da ricordare all'esame:** distinguere sempre il **tipo delle variabili**, il **tipo dell'espressione** e il **tipo della destinazione**. Non sono necessariamente uguali.

**Collegamenti:** [Array e struct](../Array_Struct/Array_Struct.md) · [Funzioni](../Funzioni/Funzioni.md) · [Stringhe](../Stringhe/Stringhe.md).
