---
sidebar_position: 2
title: La nostra infrastruttura serverless
---

## L'infrastruttura mista(AWS + Firebase)

La nostra infrastruttura si basa principalmente su AWS e Firebase.

Questo sistema è stato scelto per ottimizzare i costi, Firebase viene usato esclusivamente per la autenticazione dato che ha un prezzo molto inferiore a Amazon Cognito. 

Firebase Auth ci consente di ricevere un token JWT dato un utente loggato ed usare questo JWT nella nostra infrastruttura AWS.

## L'infrastruttura AWS

In AWS abbiamo usato i seguenti servizi:

- IoT Core, il nostro server MQTT altamente scalabile
- Lambda e API Gateway per le API che interagiscono con l'app
- DynamoDB per salvare le metriche dell'AgroSmart(ESP8266)

## Lo schema

<img src="https://github.com/agromate-devs/cloud_architecture/blob/main/architecture.drawio.png?raw=true" />

### IoT Core

IoT core ci serve come ponte tra l'ESP8266 e l'applicazione, grazie alle regole che abbiamo scritto, appena arriva un dato nel topic vengono avviate dell'elaborazioni in base al tipo di messaggio che l'ESP8266 comunica. Per il momento abbiamo 2 regole:

- Monitoraggio orario, che salva i dati dei sensori nel DynamoDB senza bisogno di lambda
- Calcolo media mensile, l'ESP8266 si occuperà di tenere traccia per 30 giorni delle media giornaliere e al 30esimo giorno manderà un messaggio al topic MQTT, verrà quindi chiamata una lambda che pulirà il Database dalle precedenti misurazioni per risparmiare spazio e metterà la media mensile.
- Notifiche dai sensori, se i sensori rilevano una temperatura o umidità sbagliata per un periodo di 3 ore, manderanno un avviso sul telefono dell'utente(quest'opzione va abilitata)

### Lambda e API Gateway

Per le API dell'applicazione abbiamo deciso di integrare le nostre lambda con API Gateway

Tutte le API sono protette da autenticazione JWT Google quindi non salviamo nessun dato dell'utente ma verrà gestito tutto da Firebase Auth.

Questo è l'elenco delle API usate dall'app:

- Device API, gestisce i dispositivi, ([specifica OpenAPI](https://agromate-devs.github.io/lambda/device_api.html))
- Plant Info API, gestisce le piante, ([specifica OpenAPI](https://agromate-devs.github.io/lambda/plant_info.html))
- Wishlist API, gestisce la Wishlist ([specifica OpenAPI](https://agromate-devs.github.io/lambda/wishlist.html))
- Sensors API, prende i dati dei sensori ([specifica OpenAPI](https://agromate-devs.github.io/docs/docs/AWS#lambda-e-api-gateway))
- Notification sender, manda una notifica all'utente attraverso SNS. Viene chiamata dalla regola IoT core. Cerca l'ARN del telefono dell'utente grazie allo user_id nel messaggio MQTT, infatti tutti gli ARN legati all'utente sono salvati in una tabella DynamoDB.

### DynamoDB

Il nostro DB è DynamoDB, altamente scalabile e veloce.

Consente di salvare le informazioni sui sensori, le piante, gli AgroSmart e gli utenti che hanno attivato le notifiche. Nessuna informazione privata degli utenti viene salvata perchè l'autenticazione è gestita completamente da Firebase Authentication
