# Gestione dei file in C — Scheda riassuntiva
**Dispensa:** 8.2 GestioneFile.

## FILE, stream e apertura
Uno **stream** è un'astrazione per leggere/scrivere dati. La libreria **stdio.h** rappresenta un file aperto tramite **FILE \***. Gli stream predefiniti sono **stdin**, **stdout** e **stderr**.

~~~c
#include <stdio.h>
FILE *fp = fopen("dati.txt", "r");
if (fp == NULL) { /* apertura fallita */ }
else { fclose(fp); }
~~~

- **fopen(nome, modalità)** apre il file, restituendo **NULL** in caso di fallimento.
- **fclose(fp)** chiude il file; verifica eventuali errori se necessario, soprattutto dopo scritture.

| Modalità | Significato |
|---|---|
| **r** | Lettura, file già esistente |
| **w** | Scrittura, crea o **svuota** il file |
| **a** | Scrittura in append, non cancella i dati precedenti |
| **r+** | Lettura e scrittura, file esistente |
| **w+** | Lettura e scrittura, crea o svuota |
| **rb / wb** | Lettura / scrittura binaria |

## Lettura e scrittura di testo
~~~c
FILE *fp = fopen("dati.txt", "r");
if (fp != NULL) {
    int c;
    while ((c = fgetc(fp)) != EOF)
        putchar(c);
    fclose(fp);
}
~~~
- **fgetc(fp)** restituisce un **int**: un carattere oppure **EOF**.
- **fputc(c, fp)** scrive un carattere.
- **fgets(buf, n, fp)** legge al massimo n-1 caratteri, aggiunge **'\0'**, include **'\n'** se letto; restituisce **NULL** se non legge una riga.
- **fputs(s, fp)** scrive la stringa escludendo **'\0'**.
- **fprintf(fp, formato, ...)** e **fscanf(fp, formato, ...)** sono le versioni su file di output/input formattato.

~~~c
// Esempio di scrittura
FILE *fp = fopen("dati.txt", "w");
if (fp != NULL) {
    fprintf(fp, "%d %s\n", 42, "ciao");
    fclose(fp);
}
~~~

~~~c
// numeri.txt contiene interi separati da spazi o righe nuove
FILE *fp = fopen("numeri.txt", "r");
if (fp == NULL) {
    printf("Errore in apertura file numeri.txt\n");
} else {
    int numero, cntPari = 0;
    while (fscanf(fp, "%d", &numero) == 1) {
        if (numero % 2 == 0)
            cntPari++;
    }
    printf("Ci sono %d numeri pari\n", cntPari);
    fclose(fp);
}
~~~
`fscanf(fp, "%d", &numero)` legge un intero alla volta, ignorando gli spazi e gli a capo tra i numeri. Il confronto con `1` verifica che sia stato letto e assegnato **un intero**; `fclose` viene chiamato solo se l'apertura è riuscita.

## EOF e gestione errori
- **EOF** è il risultato speciale di operazioni di lettura fallite per fine file o errore; non è un normale carattere del file.
- **feof(fp)** verifica l'indicatore di fine file *dopo* una lettura che ha raggiunto la fine.
- **ferror(fp)** verifica un errore sullo stream; **clearerr(fp)** azzera gli indicatori.
- **fscanf** restituisce il numero delle conversioni assegnate (o **EOF** se il fallimento di input avviene prima della prima conversione); **fprintf** restituisce il numero di caratteri scritti, o un valore negativo se fallisce.

## Accesso binario e posizionamento
~~~c
// fp aperto in una modalità binaria compatibile
int v[3] = {1, 2, 3};
size_t scritti = fwrite(v, sizeof(int), 3, fp);
size_t letti = fread(v, sizeof(int), 3, fp);
~~~
**fwrite** e **fread** restituiscono quanti *elementi* sono stati trasferiti, non quanti byte. I dati binari grezzi (es. struct) possono avere rappresentazioni non portabili tra sistemi.

**fseek(fp, offset, origine)** sposta la posizione corrente:
- **SEEK_SET**: inizio file; **SEEK_CUR**: posizione corrente; **SEEK_END**: fine file.
- **rewind(fp)** torna all'inizio del file.

## Errori tipici
- Aprire con **w** pensando di conservare il contenuto precedente.
- Usare **fp** senza controllare **NULL**.
- Scrivere un ciclo **while (!feof(fp))** e poi elaborare dati non letti: meglio controllare il risultato della lettura.
- Conservare il risultato di **fgetc** in **char**, perdendo la distinzione affidabile con **EOF**.
- Dimenticare **fclose** o il controllo dei valori restituiti da lettura/scrittura.
