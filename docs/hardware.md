---
sidebar_position: 4
title: L'ESP8266 o Agrosmart
---

La nostra scheda ESP8266, o Agrosmart, è stata programmata usando la FreeRTOS SDK in modo che la nostra scheda sia in grado di eseguire più controlli e operazioni contemporaneamente.

Ad esempio è fondamentale per noi prendere dati da 2 sensori contemporaneamente e mandarli al nostro broker MQTT.

Con altri tipi di SDK, che non supportano le task nativamente, sarebbe stato impossibile o quasi eseguire queste operazioni.

## I componenti hardware

I componenti hardware che compongono l'Agrosmart sono:

- L'ESP8266(qualsiasi modello va bene, noi usiamo una Wemos D1 R2)
- Un DHT11 per prendere i dati di temperatura e umidità
- Un vaporizzatore ad ultrasuoni, per aumentare l'umidità in caso di necessità
- Sensore d'umidità del terreno, per misurare l'umidità del terriccio, e, se configurato dall'utente, bagnare la pianta
- Una pompa da 5V, per irrigare le piante
- Un servo motore, per aprire la serra e consentire il ricambio d'aria in caso di alta umidita

## I componenti software

Il firmware dell'Agrosmart è diviso in vari componenti software che, in alcuni casi, controllano quelli hardware

- Wi-Fi, controlla la connessione al Wi-Fi, consente di instaurare una connessione tra l'Agrosmart e il telefono per impostare le credenziali WI-Fi all'Agrosmart ed infine, permette il reset dell'Agrosmart attraverso pulsante reset
- Keystore, gestisce la memoria non volatile dell'Agrosmart
- Sensors, gestisce la sensoristica, l'avvio dell'umidificatore e tutte le statistiche della serra
- MQTT helper, gestisce la connessione al broker MQTT e l'aggiunta di nuove piante
- Pump, gestisce l'irrigazione della pianta

## Il circuito

![Il circuito](/img/schema.png "Il circuito")