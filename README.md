# Progetto Bingo Automatizzato

Un sistema integrato Hardware e Software sviluppato come progetto per l'Esame di Stato (Capolavoro) dall'I.I.S. Galileo Galilei di Crema (Classe 5ID, A.S. 2025/2026).

Il progetto nasce dalla richiesta di modernizzare e automatizzare il tradizionale gioco del Bingo, integrandolo con un tabellone fisico a LED gestito da microcontrollori e un'infrastruttura software sicura e portabile.

## Realizzato da:
* **Gabriele Ogliar Badessi**
* **Alessandro Donida Labati**
* **Diego Moscardi**

---

## Architettura del Sistema

Il progetto si divide in quattro componenti principali che dialogano tra loro:

### 1. Software di Gestione (Java)
Applicazione desktop sviluppata in ambiente **NetBeans** con un'interfaccia grafica (GUI) dedicata all'operatore della sala. 
* Gestisce interamente la partita (avvio, pausa, reset).
* Estrae i numeri in modo casuale tramite un Timer automatico.
* Verifica istantaneamente le cartelle vincenti (cinquina o bingo) confrontando i dati del database con i numeri estratti.
* Comunica con l'hardware tramite la libreria `jSerialComm` via UART.

### 2. Base Dati & Sicurezza (MySQL)
Database locale (gestito tramite XAMPP) progettato per salvare le partite, i numeri estratti e i vincitori, garantendo il rispetto del D.P.R. 430/2001. Per la conformità al **GDPR**, la sicurezza è integrata direttamente nel codice:
* **Hashing delle Password:** Le credenziali degli operatori sono protette con algoritmo **SHA-256** (irreversibile).
* **Cifratura dei Dati Personali:** I dati anagrafici dei vincitori sono protetti con cifratura simmetrica **AES-128** (codificata in Base64), permettendo al sistema di decifrarli solo per mostrarli nella pagina dello storico.

### 3. Sviluppo Firmware e Hardware (C)
Architettura hardware basata su un modello **Master-Slave** programmata in linguaggio C con **Code Composer Studio**:
* **Scheda Master (LaunchPad MSPM0):** Riceve il numero estratto dal software Java via UART. Se il numero è tra 1 e 48, attiva direttamente i pin GPIO per accendere i LED della prima metà del tabellone. Se è tra 49 e 90, invia il dato via **I2C** alla scheda Slave.
* **Scheda Slave (CA48):** Espansore di I/O da 48 pin che mappa i numeri da 49 a 90 per accendere i LED della seconda metà del tabellone.

### 4. Containerizzazione (Docker)
Per rendere il progetto portabile e immediato da avviare senza configurazioni manuali sul PC dell'operatore, l'intero sistema è strutturato a microservizi tramite Docker:
* **X11 Forwarding:** Utilizza *XLaunch (VcXsrv)* per esportare l'interfaccia grafica dall'interno del container Linux a Windows.
* **Condivisione Hardware:** Utilizza lo strumento *usbipd* per mappare la porta USB/UART fisica all'interno del container isolato, permettendo alla libreria Java di dialogare con la scheda Master.

---

## Tecnologie Utilizzate

* **Linguaggi:** Java (OOP), C (Firmware embedded), SQL, HTML
* **Ambienti di Sviluppo:** NetBeans, Code Composer Studio (CCS)
* **Database:** MySQL / XAMPP
* **Virtualizzazione:** Docker, Docker Compose
* **Librerie Java:** `java.sql` (connessione DB), `jSerialComm` (comunicazione seriale)
* **Strumenti di gestione:** WBS (Work Breakdown Structure) e Diagramma di Gantt

---

## Sviluppi Futuri & Note di Rilascio
A causa di tempistiche di consegna dell'hardware da parte del committente, l'algoritmo di accensione LED è stato completato e collaudato con successo per la prima metà del tabellone (numeri 1-48). 

I futuri aggiornamenti prevedono:
* Completamento del firmware I2C per la seconda scheda Slave (49-90).
* Implementazione del *Multiplexing* per ridurre il numero di pin necessari.
* Introduzione di un modulo Wi-Fi/Bluetooth per eliminare il cavo USB di collegamento.
* Sviluppo di un'app per tablet per consentire ai giocatori di segnare i numeri digitalmente.
