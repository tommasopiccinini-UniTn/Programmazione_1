# Stringhe in C — Scheda riassuntiva
**Dispensa:** 4.2 Stringhe. Appunti sintetici con alcune funzioni standard di **string.h** richiamate nel ripasso.

## Rappresentazione
Una stringa C è un **array di char terminato da '\0'** (carattere nullo). Il terminatore occupa una cella, ma non fa parte della lunghezza.

~~~c
char s[] = "ciao";       // 5 celle: 'c','i','a','o','\0'
char t[10] = "ciao";     // 10 celle, stringa lunga 4
char u[4] = "ciao";      // array valido, ma NON contiene '\0'
~~~

- **sizeof(s)**: dimensione totale dell'array in byte, se **s** è ancora un array nello scope in questione.
- **strlen(s)**: numero di caratteri prima del primo **'\0'**.
- Usare **strlen**, **%s** o altre funzioni di stringa su un array senza terminatore comporta letture oltre i limiti e può causare comportamento indefinito.
- Il nome di un array, in molte espressioni e nel passaggio a funzione, viene convertito in un puntatore al primo elemento.

## Input e output
~~~c
#include <stdio.h>
char parola[20];
scanf("%19s", parola);             // max 19 caratteri + '\0'
printf("%s\n", parola);            // stringa intera
printf("%c\n", parola[0]);         // primo carattere
~~~

- **%s** in **scanf** non richiede **&parola** quando **parola** è un array di **char**.
- **scanf("%s", ...)** si ferma agli spazi bianchi (non legge una frase con spazi).
- Per leggere anche gli spazi usare, per esempio, **fgets(parola, sizeof parola, stdin)**: legge fino a n-1 caratteri, aggiunge **'\0'** e conserva l'eventuale **'\n'** incontrato.

## Operazioni con string.h
~~~c
#include <string.h>
char a[30] = "ciao";
char b[] = "mondo";
size_t n = strlen(a);       // 4
strcpy(a, b);               // a -> "mondo"
strcat(a, "!");             // a -> "mondo!"
int cmp = strcmp(a, b);     // 0 se uguali; qui non lo sono
~~~

- **strcpy(dest, src)** copia anche **'\0'**; **dest** deve avere spazio sufficiente.
- **strcat(dest, src)** aggiunge **src** a **dest**; servono spazio sufficiente e due stringhe valide.
- **strcmp(a, b)** restituisce 0 se uguali, un numero negativo se **a** precede **b**, positivo se segue **b** (confronto lessicografico).

## Errori tipici
~~~c
char a[20] = "ciao";
char b[] = "ciao";
a = b;            // ERRORE: gli array non sono assegnabili
if (a == b) { }   // NON confronta i caratteri delle stringhe
if (strcmp(a, b) == 0) { }  // confronto corretto del contenuto
~~~

- Dimenticare una cella per **'\0'**.
- Confondere **'a'** (carattere) con **"a"** (stringa: 'a' + '\0').
- Confondere **sizeof** e **strlen**.
- Modificare un letterale stringa tramite un puntatore (comportamento indefinito).
- Copiare o concatenare senza verificare la capacità dell'array di destinazione.
