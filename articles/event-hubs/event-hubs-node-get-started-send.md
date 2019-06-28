---
title: Envoyer et recevoir des événements à l’aide de Node.js - Azure Event Hubs | Microsoft Docs
description: Cet article décrit la procédure à suivre pour créer une application Node.js qui envoie des événements d’Azure Event Hubs.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: e67be59e0ed78b2080986acb73a33fc87599c9d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539348"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Envoyer ou recevoir des événements avec Azure Event Hubs à l’aide de Node.js

Azure Event Hubs est une plateforme de streaming Big Data et un service d’ingestion d’événements, capable de recevoir et de traiter des millions d’événements par seconde. Les concentrateurs d’événements peuvent traiter et stocker des événements, des données ou la télémétrie produits par des logiciels et appareils distribués. Les données envoyées à un concentrateur d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analyse en temps réel. Pour une présentation détaillée d’Event Hubs, consultez [Vue d’ensemble d’Event Hubs](event-hubs-about.md) et [Fonctionnalités d’Event Hubs](event-hubs-features.md).

Ce tutoriel montre comment créer des applications Node.js pour recevoir des événements à partir d’un hub d’événements ou lui en envoyer.

> [!NOTE]
> Vous pouvez télécharger ce guide de démarrage rapide sous forme d’exemple depuis [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), remplacer les chaînes `EventHubConnectionString` et `EventHubName` par les valeurs de votre hub d’événements, puis l’exécuter. Ou, vous pouvez suivre les étapes de ce didacticiel pour créer le vôtre.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

- Un compte Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- Node.js version 8.x ou ultérieure. Téléchargez la dernière version LTS à partir de [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (recommandé) ou un autre IDE
- **Créer un espace de noms Event Hubs et un hub d’événements** La première étape consiste à utiliser le [portail Azure](https://portal.azure.com) pour créer un espace de noms de type Event Hubs et obtenir les informations de gestion nécessaires à votre application pour communiquer avec le concentrateur d’événements. Pour créer un espace de noms et un hub d’événements, suivez la procédure décrite dans [cet article](event-hubs-create.md), puis passez aux étapes suivantes de ce tutoriel. Ensuite, obtenez la chaîne de connexion de l’espace de noms du hub d’événements en suivant les instructions de l’article : [Obtenir la chaîne de connexion](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Vous utiliserez la chaîne de connexion plus loin dans ce tutoriel.


### <a name="install-npm-package"></a>Installer le package npm
Pour installer le [package npm d’Event Hubs](https://www.npmjs.com/package/@azure/event-hubs), ouvrez une invite de commandes dont le chemin d’accès contient `npm`, remplacez le répertoire par le dossier où vous souhaitez stocker vos exemples, puis exécutez cette commande.

```shell
npm install @azure/event-hubs
```

Pour installer le [package npm d’Event Processor Host](https://www.npmjs.com/package/@azure/event-processor-host), exécutez la commande ci-dessous à la place.

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Envoyer des événements

Cette section montre comment créer une application Node.js pour envoyer des événements à un hub d’événements. 

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com). 
2. Créez un fichier appelé `send.js` et collez-y le code ci-dessous.
    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Define connection string and the name of the Event Hub
    const connectionString = "";
    const eventHubsName = "";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Entrez la chaîne de connexion et le nom de votre hub d’événements dans le code ci-dessus.
4. Ensuite, exécutez la commande `node send.js` dans une invite de commandes pour exécuter ce fichier. Ce script envoie 100 événements à votre hub d’événements.

Félicitations ! Vous venez d’envoyer des événements à un hub d’événements.


## <a name="receive-events"></a>Recevoir des événements

Cette section vous montre comment créer une application Node.js qui reçoit des événements à partir d’une partition unique du groupe de consommateurs par défaut dans un hub d’événements. 

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com). 
2. Créez un fichier appelé `receive.js` et collez-y le code ci-dessous.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Define connection string and related Event Hubs entity name here
    const connectionString = "";
    const eventHubsName = "";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Entrez la chaîne de connexion et le nom de votre hub d’événements dans le code ci-dessus.
4. Ensuite, exécutez la commande `node receive.js` dans une invite de commandes pour exécuter ce fichier. Il recevra les événements d’une des partitions du groupe de consommateurs par défaut dans votre hub d’événements

Félicitations ! Vous recevez maintenant des événements depuis le hub d’événements.

## <a name="receive-events-using-event-processor-host"></a>Recevoir des événements avec l’hôte du processeur d’événements

Cette section montre comment recevoir des événements d’un hub d’événements à l’aide d’Azure [EventProcessorHost](event-hubs-event-processor-host.md) dans une application Node.js. EventProcessorHost (EPH) vous permet de recevoir efficacement des événements d’un Event Hub en créant des récepteurs sur toutes les partitions du groupe de consommateurs d’un Event Hub. Il contrôle les métadonnées de messages reçus à intervalles réguliers dans un Azure Storage Blob. Cette approche permet ultérieurement de continuer à recevoir des messages à partir du point où vous vous étiez arrêté.

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com). 
2. Créez un fichier appelé `receiveAll.js` et collez-y le code ci-dessous.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Define connection string and related Event Hubs entity name here
    const eventHubConnectionString = "";
    const eventHubName = "";
    const storageConnectionString = "";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. Entrez la chaîne de connexion et le nom de votre hub d’événements dans le code ci-dessus, ainsi que la chaîne de connexion pour un Stockage Blob Azure.
4. Ensuite, exécutez la commande `node receiveAll.js` dans une invite de commandes pour exécuter ce fichier.

Félicitations ! Vous recevez maintenant des messages depuis un hub d’événements à l’aide de l’hôte du processeur d’événements. Il recevra les événements de toutes les partitions du groupe de consommateurs par défaut dans votre hub d’événements.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Fonctionnalités et terminologie dans Azure Event Hubs](event-hubs-features.md)
- [FAQ sur les hubs d’événements](event-hubs-faq.md)
- Consultez d’autres exemples Node.js pour [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) et [Event Processor Host](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) sur GitHub.
