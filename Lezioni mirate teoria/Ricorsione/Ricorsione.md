# Ricorsione in C — Scheda riassuntiva
**Dispensa:** 8.1 Programmazione Ricorsiva. Argomento distinto dalle nozioni generali sulle funzioni.

## Regole fondamentali
- **Ricorsione diretta:** una funzione chiama sé stessa.
- **Ricorsione indiretta:** f chiama g e g richiama f (eventualmente tramite altre funzioni).
- Ogni funzione ricorsiva corretta deve prevedere un **caso base** e un **passo ricorsivo** che avvicini al caso base.
- Ogni chiamata ha parametri e variabili locali proprie sullo **stack**; chiamate profonde usano più memoria.
- Le istruzioni **prima** della chiamata ricorsiva si eseguono in **discesa**; quelle **dopo** durante la **risalita**.
- Non confondere valore restituito da **return** e modifica di un argomento del chiamante.

## Fattoriale
~~~c
int fatt(int n) {
    if (n == 0) return 1;
    return n * fatt(n - 1);
}
// fatt(3) = 3 * 2 * 1 = 6
~~~
Valido per n >= 0, entro i limiti del tipo intero. Tempo O(n), spazio stack O(n).

## Fibonacci — due chiamate ricorsive
~~~c
int fib(int n) {
    if (n == 0) return 0;
    if (n == 1) return 1;
    return fib(n - 1) + fib(n - 2);
}
~~~
**fib(6) = 8**. La versione ricorsiva semplice ripete molti sottocalcoli, quindi il tempo cresce esponenzialmente. La **memoizzazione** conserva i risultati già trovati; in alternativa un ciclo usa tempo O(n) e spazio aggiuntivo O(1).

~~~c
int fib_iter(int n) {
    if (n <= 1) return n;
    int a = 0, b = 1;
    for (int i = 2; i <= n; ++i) {
        int t = a + b;
        a = b;
        b = t;
    }
    return b;
}
~~~
Negli esempi si assume n >= 0 e si trascurano gli overflow per valori grandi.

## Massimo comune divisore (Euclide)
~~~c
int mcd(int x, int y) {
    return y == 0 ? x : mcd(y, x % y);
}
~~~
Si assume x >= 0 e y >= 0. Il caso base è **y == 0**.

## Torri di Hanoi
Spostare n dischi da A a C usando B, senza mettere un disco grande su uno piccolo.
1. Spostare n-1 dischi da A a B (con C di appoggio).
2. Spostare il disco più grande da A a C.
3. Spostare n-1 dischi da B a C (con A di appoggio).

~~~c
#include <stdio.h>
void hanoi(char da, char a, char appoggio, int n) {
    if (n == 1) {
        printf("Disco 1: %c -> %c\n", da, a);
        return;
    }
    hanoi(da, appoggio, a, n - 1);
    printf("Disco %d: %c -> %c\n", n, da, a);
    hanoi(appoggio, a, da, n - 1);
}
// hanoi('A', 'C', 'B', 3); -> 7 mosse
~~~
Per n >= 1: **M(n) = 2 M(n-1) + 1 = 2^n - 1**. Le mosse crescono esponenzialmente. La versione **iterativa** è possibile: per n dispari si alternano le coppie A-C, A-B, B-C; per n pari A-B, A-C, B-C, eseguendo ogni volta la mossa legale fra la coppia prescelta.

## Errori tipici
- Caso base assente o passo che non vi si avvicina: ricorsione senza terminazione.
- Dimenticare che ogni chiamata ha una **propria** copia delle variabili locali automatiche.
- Dimenticare operazioni eseguite **dopo** la chiamata ricorsiva.
- Pensare che Fibonacci ricorsivo calcoli una sola volta ciascun numero.
