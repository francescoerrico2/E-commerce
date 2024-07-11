# E-coomerce Backend

## Introduzione
È stato creato un backend per un sito di e-commerce. Ogni attore del sistema dispone di un proprio terminale separato, con funzioni specifiche per i suoi compiti. La comunicazione tra i vari processi avviene tramite gli stream di **Redis**, che consentono agli attori in esecuzione di ascoltare e, se necessario, di scrivere. I dati vengono archiviati in un database utilizzando **Postgres**. È richiesta la presenza delle librerie **hiredis** e **libpq**.

## Installazione e configurazione

Una volta clonata la repo di github, è possibile compilare tutto il codice in automatico eseguendo lo script *make.sh*. 

Una volta compilato il codice, bisogna avviare il server redis e il db.
Per avviare il server redis bisogna lanciare su un terminale apposito (che non va chiuso):

```
redis-server
```

Per avviare il db, bisogna posizionarsi all'interno della cartella db-scripts, presente all'interno del progetto, ed eseguire singolarmente nell'ordine in cui sono presentati i seguenti comandi:

```
cd db-scripts

sudo -u postgres psql postgres -f parameters.sql -f create-db-user.sql  -f schema.sql  -f grant.sql
//crea il database
```

Se si desidera accedere al database per visualizzare i dati dopo l'esecuzione del programma, è necessario eseguire i seguenti comandi ed eseguire le query desiderate:
```
sudo -i -u postgres

psql

\c ecommercedb //si connette al db creato
```

## Utilizzo
> **DISCLAIMER**: se si vuole disabilitare la generazione dei prodotti da parte del fornitore nel db, modificare la variabile DEBUG con il valore 0.

Una volta compilato tutto il codice, basterà eseguire in diversi terminali i seguenti codici:

Il modello **utente** è utilizzato per rappresentare un utente che ancora deve essere autenticato (previa registrazione o login).

```
cd models/utente/bin
./main
```

Il modello **fornitore** è utilizzato per rappresentare un fornitore tipico del sistema, una volta avviato si connetterà in automatico alle stream redis. Rimane in ascolto sullo stream in attesa di nuovi ordini, una volta ricevuto, si occuperà di assegnarlo al trasportatore scelto.

```
cd models/fornitore/bin
./main
```

Il modello **trasportatore** è utilizzato per rappresentare un trasportatore tipico del sistema, una volta avviato si connetterà in automatico alle stream redis. Nelle stream si occuperà di apprendere quali ordini gli sono stati assegnati e lavorarli, aggiornandone lo stato.

```
cd models/trasportatore/bin
./main
```

Il modello **costumer** è utilizzato per rappresentare un utente autenticato, si connette alle stream redis predisposte, che gli permettono di inoltrare un ordine al fornitore corrispondente, e di ricevere informazioni sullo stato degli ordini effettuati.

```
cd models/costumer/bin
./main
```
