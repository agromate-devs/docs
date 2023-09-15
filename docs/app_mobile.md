---
sidebar_position: 3
---

# L'app mobile

L'app mobile è scritta con Framework7 e Svelte in modo che sia completamente Cross-Platform(Android, iOS e PWA).

L'app si compone di queste schermate:

- Home page
- Esplora, dove c'è un insieme di piante
- Coltiva planta, dove si può assegnare una pianta ad un AgroSmart e iniziare a coltivare
- Dispositivi, dove c'è l'insieme di tutti gli AgroSmart
- Dashboard del profilo, dove possiamo controllare lo stato delle nostre piante
- Wishlist, dove possiamo creare liste dei desideri con le piante

## Login

Il login dell'app avviene attraverso l'SDK nativa di Firebase per le massime prestazioni e sicurezza(solo l'id app di AgroMate con il certificato valido è nella whitelist di Firebase), grazie a capacitor-community/firebase

I tipi di login supportati sono Email-Password e Account Google.

## Il Database delle piante

Il database delle piante di AgroMate è derivato dal Database delle piante americano(usda) con la rimozione di tutte le piante che non possono essere piantate in una serra( questa rimozione non è accurata al 100% :) )

Il database è di tipo Sqlite ed è integrato con l'applicazione attraverso sql.js per la PWA mentre usa le librerie native su Android/iOS, grazie al plugin capacitor-community/sqlite

## La cache

Per evitare richieste non necessarie verso AWS, che aumenterebbero i costi, abbiamo ideato un sistema di caching dei dati in casi specifici.

Questo sistema si applica nella WishList e nella vista dei dispositivi, sarà ampliato a breve.

Il sistema, nella parte delle wishlist quindi, salva l'ultimo dispositivo che ha modificato la wishlist in un Database firebase e allo stesso tempo salva la wishlist nel localstorage così rimane salvato nelle varie sessioni dell'applicazione. Se noi abbiamo un solo dispositivo registrato, non ha senso aggiornare la wishlist dall'API ogni volta che andiamo nella pagina wishlist ma prendiamo i dati dal localstorage.

Allo stesso tempo, se nel nostro database Firebase, è segnato che la wishlist è stata cambiata da un dispositivo che non è il nostro, aggiorneremo la wishlist dall'API

## La registrazione di un nuovo Agrosmart

Quando accoppiamo un nuovo Agrosmart, il telefono attiverà l'ESPTouch V2  per cercare ESP8266 nelle vicinanze e l'Agrosmart, se nessuna rete Wi-Fi è registrata, parte l'ESPTouch V2.

Quindi il telefono, una volta che instaura la connessione all'ESP8266, assegnarà uno UUID all'ESP8266 e chiamerà un API che registrerà l'Agrosmart nel DynamoDB in modo che l'utente possa vederli nell'app e possa controllare lo stato della pianta associata all'Agrosmart
