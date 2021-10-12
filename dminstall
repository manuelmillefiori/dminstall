#!/bin/sh
# Autore: Manuel Millefiori
# Data: 2021-10-08
# Ultima modifica: 2021-10-12
# Versione: 1.0

# Creazione eseguibile:
# sudo chmod +x dminstall.sh
# sudo chown root:root dminstall.sh
# mv dminstall.sh dminstall

# Specifiche: Programma che facilita l'installazione di un programma
# archiviato e/o compattato in formato "gz", "xz", "bzip2"

# Funzione per estrarre il contenuto del file "gz"
extract()
{
   # Controllo se la modalità verbosa è attiva
   if [ $VERBOSE = "YES" ]
   then
      # Informo l'utente delle istruzioni che sto eseguendo
      echo "Estrazione dei file all'interno dell'archivio in corso..."
   fi
   # Estraggo e decompatto il file dall'archivio nella directory
   # dove ci sono tutti i dati di tutti i programmi
   tar xvzf "$ARCHIVE" -C "$DATA_PATH" 2> /dev/null > .temp.txt

   # Se l'estrazione tramite decompattazione di un file "tar.gz"
   # non è avvenuta correttamente, il file all'interno dell'archivio
   # verrà estratto e decompattato come un file "tar.xz"
   if [ "$?" -ne "0" ]
   then
      # Estraggo e decompatto il file con un algoritmo
      # di decompattazione di un archivio "tar.xz"
      tar xvJf "$ARCHIVE" -C "$DATA_PATH" 2> /dev/null > .temp.txt
   fi

   # Se l'estrazione tramite decompattazione di un file "tar.gz"
   # non è avvenuta correttamente, il file all'interno dell'archivio
   # verrà estratto e decompattato come un file "tar.bzip2"
   if [ "$?" -ne "0" ]
   then
      # Estraggo e decompatto il file con un algoritmo
      # di decompattazione di un archivio "tar.bzip2"
      tar xvjf "$ARCHIVE" -C "$DATA_PATH" 2> /dev/null > .temp.txt
   fi

   # Se l'estrazione tramite decompattazione non è avvenuta
   # correttamente, il file all'interno dell'archivio verrà
   # estratto senza essere decompattato
   if [ "$?" -ne "0" ]
   then
      # Estraggo il file senza decompattarlo
      tar xvf "$ARCHIVE" -C "$DATA_PATH" 2> /dev/null > .temp.txt
   fi

   # Controllo se tutte e due le estrazioni sono fallite
   if [ "$?" -ne "0" ]
   then
      # Informo l'utente che le estrazioni sono fallite
      echo "Il file scelto non è un archivio supportato dal programma!"

      # Rimuovo il file temporaneo che è stato generato per
      # ottenere il nome della directory estratta
      rm .temp.txt

      # Chiudo il programma in errore con codice d'uscita "1"
      exit 1
   else
      # Controllo se la modalità verbosa è attiva
      if [ $VERBOSE = "YES" ]
      then
         # Informo l'utente delle istruzioni che sto eseguendo
         echo "Estrazione dei file all'interno della directory ${DATA_PATH} completata con successo!"
      fi

      # Ottengo il nome della directory estratta
      EXTRACTED_DIRECTORY=`head -1 .temp.txt`

      # Rimuovo il file temporaneo che è stato generato per
      # ottenere il nome della directory estratta
      rm .temp.txt
   fi
}

# Funzione per ottenere la cartella "bin" della directory estratta
bin_research()
{
   # Controllo se la modalità verbosa è attiva
   if [ $VERBOSE = "YES" ]
   then
      # Informo l'utente delle istruzioni che sto eseguendo
      echo "Ricerca directory \"bin\" in corso..."
   fi

   # Ottengo l'url della directory "bin"
   BIN_DIR="${DATA_PATH}${EXTRACTED_DIRECTORY}bin/"

   # Controllo se la modalità verbosa è attiva
   if [ $VERBOSE = "YES" ]
   then
      # Informo l'utente delle istruzioni che sto eseguendo
      echo "Directory dei bin trovata: ${BIN_DIR}"
   fi
}

# Funzione per linkare tutti i programmi della directory bin
# nella cartella "/usr/local/bin"
bin_linker()
{
   # Scorro tutti i file della directory dei bin
   for file in ${BIN_DIR}*
   do
      # Controllo se il file è un eseguibile
      if [ -x $file ]
      then
         # Controllo se la modalità verbosa è attiva
         if [ $VERBOSE = "YES" ]
         then
            # Informo l'utente delle istruzioni che sto eseguendo
            echo "Creo un collegamento dell'eseguibile \"${file}\" nella directory \"${BIN_PATH}\""
         fi

         # Creo un collegamento nella path dei bin locali
         # dell'eseguibile del programma
         ln -sf "$file" "$BIN_PATH"

         # Ottengo il nome dell'eseguibile
         echo $file | cut -d"/" -f7 > .temp.txt
         LINKED_FILENAME=`head -1 .temp.txt`
      fi
   done
}

# Funzione per la creazione di un file ".desktop"
app_creation()
{
   # Controllo se la modalità verbosa è attiva
   if [ $VERBOSE = "YES" ]
   then
      # Informo l'utente delle istruzioni che sto eseguendo
      echo "Ottengo l'url del file in formato \"png\" per impostare l'immagine dell'applicazione"
   fi

   # Ottengo il nome del file "png"
   PNG_FILE="${LINKED_FILENAME}.png"

   # Ottengo l'url del file "png"
   PNG_FILE_URL=`find "${DATA_PATH}${EXTRACTED_DIRECTORY}" | grep "${PNG_FILE}$"`

   # Controllo se la modalità verbosa è attiva
   if [ $VERBOSE = "YES" ]
   then
      # Informo l'utente delle istruzioni che sto eseguendo
      echo "Genero l'applicazione con estensione \"desktop\" nella directory \"${APPLICATION_PATH}\""
   fi

   # Genero il file ".desktop"
   echo "[Desktop Entry]
Encoding=UTF-8
Version=1.0
Type=Application
Terminal=false
Exec=${BIN_DIR}${LINKED_FILENAME}
Name=${APPLICATION_NAME}
Icon=${PNG_FILE_URL}
Comment=Applicazione generata dal programma \"dminstall\" sviluppata da Manuel Millefiori!" | tee ${APPLICATION_PATH}${LINKED_FILENAME}.desktop > /dev/null
}

# Controllo se lo script è stato eseguito con i permessi d'amministratore
if [ `whoami` != "root" ]
then
   # Informo l'utente che il programma deve essere lanciato come "root"
   echo "Il programma deve essere eseguito con i permessi di amministratore!"

   # Chiudo il programma in errore con codice d'uscita "1"
   exit 1
fi

# Imposto di predefinito la modalità verbosa a "NO"
VERBOSE="NO"

# Imposto di predefinito la modalità application a "NO"
APPLICATION="NO"

# Controllo se l'utente ha avviato correttamente il programma
if [ -z "$1" ]
then
   # Informo l'utente di come si possono visualizzare le modalità di utilizzo del programma
   echo "Utilizza il programma in questo modo per visualizzare le modalità di utilizzo:
   sudo dminstall --help"

   # Chiudo il programma in errore con codice d'uscita "1"
   exit 1
fi

# Controllo se l'utente ha richiesto le modalità di utilizzo del programma
if [ "$1" = "--help" ]
then
   # Informo l'utente delle varie modalità di utilizzo del programma
   echo "Utilizzo:
   sudo dminstall [OPTIONS [ARGUMENT]] ARCHIVE
Lista dei vari argomenti e degli utilizzi associati:
   -v, --verbose       Modalità verbose, stampa di tutte le istruzioni
                       che vengono eseguite.
   -a, --application   Modalità application, genera un collegamento al
                       programma nel menù delle applicazioni.
   --help              Stampa delle modalità d'utilizzo del programma.
Autore: Manuel Millefiori
Data: 2021-10-08
Versione: 1.0"

   # Chiudo il programma correttamente con codice d'uscita "0"
   exit 0
fi

# Controllo se l'utente vuole utilizzare la modalità verbosa
if [ "$1" = "-v" ] || [ "$1" = "--verbose" ]
then
   # Imposto la modalità verbosa a "YES"
   VERBOSE="YES"

   # Eseguo lo shift degli argomenti del programma
   shift
fi

# Controllo se l'utente vuole utilizzare la modalità application
if [ "$1" = "-a" ] || [ "$1" = "--application" ]
then
   # Shifto di un elemento gli argomenti
   shift

   # Controllo se l'argomento dell'argomento "--application" è scritto correttamente
   if [ -n "$1" ] && [ -n "$2" ]
   then
      APPLICATION="YES"

      # Inizializzo il nome dell'applicazione
      APPLICATION_NAME="$1"

      # Controllo se la modalità verbosa è attiva
      if [ $VERBOSE = "YES" ]
      then
         # Informo l'utente delle istruzione che sto eseguendo
         echo "Modalità \"Application\" attivata correttamente, nome dell'applicazione: \"${APPLICATION_NAME}\""
      fi

      # Shifto di un elemento gli argomenti
      shift
   else
      # Informo l'utente che il nome dell'applicazione non è stato specificato
      echo "Nome dell'applicazione non specificato correttamente! Consulta il manuale con:
   sudo dminstall --help"

      # Chiudo il programma in errore con codice d'uscita "1"
      exit 1
   fi
fi

# Imposto la path per i dati
DATA_PATH="/usr/local/etc/"

# Imposto la path per i bin
BIN_PATH="/usr/local/bin/"

# Imposto la path per le applicazioni
APPLICATION_PATH="/home/${USER}/.local/share/applications/"

# Inizializzo il nome dell'archivio come il primo argomento
ARCHIVE=$1

# Controllo se il file passato come argomento è realmente un file
if [ -f $ARCHIVE ]
then
   # Invoco la funzione per estrarre il contenuto
   extract

   # Invoco la funzione per la ricerca della cartella dei bin
   bin_research

   # Invoco la funzione per linkare i bin nella path dei bin locali
   bin_linker

   # Controllo se la modalità verbosa è attiva
   if [ $VERBOSE = "YES" ]
   then
      # Informo l'utente delle istruzioni che sto eseguendo
      echo "Creazione del collegamento al programma nella path effettuata correttamente!"
   fi

   # Controllo se la modalità "Application" è attiva
   if [ $APPLICATION = "YES" ]
   then
      # Invoco la funzione per la creazione del file ".desktop"
      app_creation

      # Controllo se la modalità verbosa è attiva
      if [ $VERBOSE = "YES" ]
      then
         # Informo l'utente delle istruzioni che sto eseguendo
         echo "Collegamento dell'applicazione al menù effettuato correttamente!"
      fi
   fi

   # Chiudo il programma correttamente con codice d'uscita "0"
   exit 0
else
   # Informo l'utente che il file non esiste
   echo "Il file non esiste!"

   # Chiudo il programma in errore con codice d'uscita "1"
   exit 1
fi
