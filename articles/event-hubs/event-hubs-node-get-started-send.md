---
title: Recevoir des événements d’Azure Event Hubs ou lui en envoyer en utilisant JavaScript (hérité)
description: Cet article décrit la procédure à suivre pour créer une application JavaScript qui reçoit des événements d’Azure Event Hubs et lui en envoie à l’aide de l’ancien package azure/event-hubs version 2.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 0a4b76bd1febca864cab6060fbdbd96dd0061cff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162614"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Démarrage rapide : Recevoir des événements d’Azure Event Hubs ou lui en envoyer avec JavaScript (@azure/event-hubs version 2)
Ce guide de démarrage rapide montre comment créer des applications JavaScript pour recevoir des événements d’un hub d’événements ou lui en envoyer à l’aide du package JavaScript azure/event-hubs version 2. 

> [!WARNING]
> Ce guide de démarrage rapide utilise l’ancienne version du package azure/event-hubs (version 2). Pour obtenir un guide de démarrage rapide qui utilise la dernière version du package (**version 5**), consultez [Envoyer et recevoir des événements à l’aide d’azure/eventhubs version 5](get-started-node-send-v2.md). Pour migrer votre application de l’ancien package vers le nouveau, consultez [Guide to migrate from azure/eventhubs version 1 to version 5](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md) (Guide de migration de la version 1 vers la version 5 d’azure/eventhubs). 


## <a name="prerequisites"></a>Prérequis

Si vous débutez avec Azure Event Hubs, consultez la [vue d’ensemble d’Event Hubs](event-hubs-about.md) avant de suivre ce guide de démarrage rapide. 

Pour effectuer ce démarrage rapide, vous avez besoin de ce qui suit :

- **Abonnement Microsoft Azure**. Pour utiliser les services Azure, y compris Azure Event Hubs, vous avez besoin d’un abonnement.  Si vous n’avez pas de compte Azure, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/) ou utiliser les avantages de votre abonnement MSDN quand vous [créez un compte](https://azure.microsoft.com).
- Node.js version 8.x ou ultérieure. Téléchargez la dernière version de LTS à partir de [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (recommandé) ou un autre IDE
- **Créez un espace de noms Event Hubs et un Event Hub**. La première étape consiste à utiliser le [portail Azure](https://portal.azure.com) pour créer un espace de noms de type Event Hubs et obtenir les informations de gestion nécessaires à votre application pour communiquer avec le concentrateur d’événements. Pour créer un espace de noms et un hub d’événements, suivez la procédure décrite dans [cet article](event-hubs-create.md), puis passez aux étapes suivantes de ce tutoriel. Ensuite, obtenez la chaîne de connexion de l’espace de noms du hub d’événements en suivant les instructions de l’article : [Obtenir la chaîne de connexion](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Vous utiliserez la chaîne de connexion plus loin dans ce tutoriel.


### <a name="install-npm-package"></a>Installer le package npm
Pour installer le [package npm d’Event Hubs](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0), ouvrez une invite de commandes dont le chemin d’accès contient `npm`, remplacez le répertoire par le dossier où vous souhaitez stocker vos exemples, puis exécutez cette commande.

```shell
npm install @azure/event-hubs@2
```

Pour installer le [package npm d’Event Processor Host](https://www.npmjs.com/package/@azure/event-processor-host), exécutez la commande ci-dessous à la place.

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Envoyer des événements

Cette section montre comment créer une application JavaScript qui envoie des événements à un hub d’événements. 

> [!NOTE]
> Vous pouvez télécharger ce guide de démarrage rapide sous forme d’exemple depuis [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), remplacer les chaînes `EventHubConnectionString` et `EventHubName` par les valeurs de votre hub d’événements, puis l’exécuter. Ou, vous pouvez suivre les étapes de ce didacticiel pour créer le vôtre.

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com). 
2. Créez un fichier appelé `send.js` et collez-y le code ci-dessous. Pour obtenir la chaîne de connexion de l’espace de noms du hub d’événements, suivez les instructions de l’article : [Obtenir la chaîne de connexion](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

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

Cette section montre comment créer une application JavaScript qui reçoit des événements à partir d’une partition unique du groupe de consommateurs par défaut dans un hub d’événements. 

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com). 
2. Créez un fichier appelé `receive.js` et collez-y le code ci-dessous.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

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

Cette section montre comment recevoir des événements d’un hub d’événements à l’aide d’Azure [EventProcessorHost](event-hubs-event-processor-host.md) dans une application JavaScript. EventProcessorHost (EPH) vous permet de recevoir efficacement des événements d’un Event Hub en créant des récepteurs sur toutes les partitions du groupe de consommateurs d’un Event Hub. Il contrôle les métadonnées de messages reçus à intervalles réguliers dans un Azure Storage Blob. Cette approche permet ultérieurement de continuer à recevoir des messages à partir du point où vous vous étiez arrêté.

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com). 
2. Créez un fichier appelé `receiveAll.js` et collez-y le code ci-dessous.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

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
- Consultez d’autres exemples JavaScript pour [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) et [Event Processor Host](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) sur GitHub.
