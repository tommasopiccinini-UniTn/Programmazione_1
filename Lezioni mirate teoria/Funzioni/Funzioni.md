# Funzioni in C — Scheda riassuntiva
**Dispensa:** 6.2 Funzioni. Appunti per ripasso della teoria; esempi sintetici, non trascrizioni dei test.

## Dichiarazione, chiamata e ritorno
- Una funzione ha **tipo di ritorno**, nome e **parametri formali**; gli argomenti della chiamata sono i **parametri effettivi**.
- Il **prototipo** rende noto al compilatore nome, tipo del risultato e tipi degli argomenti prima dell'uso.
- **return** interrompe immediatamente la funzione e restituisce il risultato; le procedure di tipo **void** non restituiscono un valore.
- Una funzione può restituire un valore scalare, un puntatore o una **struct**, ma non un array direttamente.

~~~c
int doppio(int n);           // prototipo
int doppio(int n) { return n * 2; }
int main(void) { int x = doppio(4); return 0; }
~~~

## Passaggio dei parametri
In C **tutti gli argomenti sono passati per valore**. Anche quando si passa un indirizzo, viene copiata la *variabile puntatore*; dereferenziando la copia si può modificare l'oggetto originale.

~~~c
void modifica(int a, int *b) {
    a += 2;      // cambia solo la copia locale
    *b += 2;     // cambia il dato del chiamante
}
// x=3, y=5; modifica(x, &y); -> x=3, y=7
~~~

- **struct per valore:** viene copiata completamente, inclusi eventuali campi array; una modifica locale non cambia l'originale.
- **Array come parametro:** nella dichiarazione di parametro **int v[]** equivale a **int *v**; gli elementi originali possono essere modificati e la lunghezza non è trasmessa automaticamente.
- **Parametri di output:** una funzione **void** può aggiornare più risultati usando puntatori.
- **Puntatore a puntatore (int **):** permette alla funzione di cambiare il puntatore conservato dal chiamante.

~~~c
void reindirizza(int **p, int *q) { *p = q; }
// int a=4, b=9, *p=&a;
// reindirizza(&p, &b); -> ora p punta a b
~~~

## Scope, durata di vita e stack
- Una variabile locale automatica appartiene alla specifica attivazione della funzione; quando questa termina, la variabile cessa di esistere.
- Una variabile locale **static** conserva il proprio valore fra chiamate, pur mantenendo visibilità locale.
- Una variabile locale può **nascondere** una globale con lo stesso nome.
- Ogni chiamata ha il proprio ambiente sullo **stack**; le chiamate annidate terminano dall'ultima alla prima.

**Pericolo:** non restituire l'indirizzo di una variabile locale automatica.

~~~c
int *errata(void) { int x = 10; return &x; } // puntatore non valido dopo return
int *valida(void) { static int x = 10; return &x; }
~~~

**Da ricordare:** se due puntatori indicano la stessa variabile **static**, leggono entrambi il suo valore attuale, non quello presente quando il puntatore è stato ottenuto.

## Const sui puntatori
| Dichiarazione | Posso cambiare il dato con *p? | Posso riassegnare p? |
|---|---|---|
| **int *p** | Sì | Sì |
| **const int *p** | No | Sì |
| **int *const p** | Sì | No |
| **const int *const p** | No | No |

## Errori tipici
- Confondere **p = q** (cambia la copia del puntatore) con **\*p = \*q** (cambia il dato puntato).
- Credere che cambiare un parametro scalare modifichi l'argomento del chiamante.
- Confondere copia di una **struct** con accesso agli elementi di un **array** passato a funzione.
- Credere che il valore restituito da **return** modifichi automaticamente un puntatore passato alla funzione.
- Utilizzare un puntatore a una variabile locale automatica dopo la fine della funzione.

**Collegamento:** la ricorsione è trattata separatamente in [Ricorsione](../Ricorsione/Ricorsione.md).
