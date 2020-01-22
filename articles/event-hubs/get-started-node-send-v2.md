---
title: 'Envoyer et recevoir des événements à l’aide de Node.js : Azure Event Hubs'
description: Cet article décrit la procédure à suivre pour créer une application Node.js qui envoie des événements d’Azure Event Hubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/09/2020
ms.author: spelluru
ms.openlocfilehash: d4810c325acc42d5aa665002654cb01154cdc6bb
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981614"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Envoyer ou recevoir des événements avec Azure Event Hubs à l’aide de Node.js

Azure Event Hubs est une plateforme de streaming Big Data et un service d’ingestion d’événements, capable de recevoir et de traiter des millions d’événements par seconde. Les concentrateurs d’événements peuvent traiter et stocker des événements, des données ou la télémétrie produits par des logiciels et appareils distribués. Les données envoyées à un concentrateur d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analyse en temps réel. Pour une présentation détaillée d’Event Hubs, consultez [Vue d’ensemble d’Event Hubs](event-hubs-about.md) et [Fonctionnalités d’Event Hubs](event-hubs-features.md).

Ce tutoriel montre comment créer des applications Node.js pour recevoir des événements à partir d’un hub d’événements ou lui en envoyer.

> [!IMPORTANT]
> Ce guide de démarrage rapide utilise la version 5 du Kit de développement logiciel (SDK) Azure Event Hubs Java Script. Pour un démarrage rapide qui utilise la version 2 antérieure du Kit de développement logiciel (SDK) Java script, consultez [cet article](event-hubs-node-get-started-send.md). Si vous utilisez la version 2 du Kit de développement logiciel (SDK), nous vous recommandons de migrer votre code vers la dernière version. Pour plus d’informations, consultez le [guide de migration](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md).

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

- Un compte Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- Node.js version 8.x ou ultérieure. Téléchargez la dernière version de LTS à partir de [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (recommandé) ou un autre IDE
- **Créez un espace de noms Event Hubs et un Event Hub**. La première étape consiste à utiliser le [portail Azure](https://portal.azure.com) pour créer un espace de noms de type Event Hubs et obtenir les informations de gestion nécessaires à votre application pour communiquer avec le concentrateur d’événements. Pour créer un espace de noms et un hub d’événements, suivez la procédure décrite dans [cet article](event-hubs-create.md), puis passez aux étapes suivantes de ce tutoriel. Ensuite, obtenez la chaîne de connexion de l’espace de noms du hub d’événements en suivant les instructions de l’article : [Obtenir la chaîne de connexion](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Vous utiliserez la chaîne de connexion plus loin dans ce tutoriel.


### <a name="install-npm-packages"></a>Installer les packages npm
Pour installer le [package npm d’Event Hubs](https://www.npmjs.com/package/@azure/event-hubs), ouvrez une invite de commandes dont le chemin d’accès contient `npm`, remplacez le répertoire par le dossier où vous souhaitez stocker vos exemples, puis exécutez cette commande.

```shell
npm install @azure/event-hubs
```

Pour le côté récepteur, vous avez besoin d’installer deux autres packages. Dans ce guide de démarrage rapide, vous utiliserez le Stockage Blob Azure pour conserver les points de contrôle afin que le programme ne lise pas les événements qu’il a déjà lus. Il contrôle les métadonnées des messages reçus à intervalles réguliers dans un blob. Cette approche permet ultérieurement de continuer à recevoir des messages à partir du point où vous vous étiez arrêté.

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Envoyer des événements

Cette section montre comment créer une application Node.js pour envoyer des événements à un hub d’événements.

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com).
2. Créez un fichier appelé `send.js` et collez-y le code suivant.

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // create a producer client to send messages to the event hub
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // prepare a batch of three events
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // send the batch to the event hub
      await producer.sendBatch(batch);

      // close the producer client
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. N’oubliez pas de remplacer les valeurs **Chaîne de connexion** et **Nom du Event Hub** dans le code.
5. Exécutez la commande `node send.js` pour exécuter ce fichier. Cette opération envoie un lot de trois événements à votre Event Hub.
6. Dans le Portail Azure, vous pouvez vérifier que l’Event Hub a reçu les messages. Basculez vers l’affichage **Messages** dans la section **Métriques**. Actualisez la page pour mettre à jour le graphique. Cela peut prendre quelques secondes pour indiquer que les messages ont été reçus.

    [![Vérifier que l’Event Hub a reçu les messages](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Pour obtenir le code source complet avec des remarques plus détaillées, consultez [ce fichier sur GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js)

Félicitations ! Vous venez d’envoyer des événements à un hub d’événements.


## <a name="receive-events"></a>Recevoir des événements
Cette section montre comment recevoir des événements d’un Event Hub à l’aide d’un magasin de points de contrôle Azure Blob dans une application Node.js. Il contrôle les métadonnées de messages reçus à intervalles réguliers dans un Azure Storage Blob. Cette approche permet ultérieurement de continuer à recevoir des messages à partir du point où vous vous étiez arrêté.

### <a name="create-an-azure-storage-and-a-blob-container"></a>Créer un compte de stockage Azure et un conteneur d’objets blob
Pour créer un compte de stockage Azure dans lequel se trouve un conteneur d’objets blob, procédez comme suit.

1. [Création d’un compte de stockage Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. Créer un [conteneur d’objets blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obtenir la chaîne de connexion au compte de stockage](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Notez la chaîne de connexion et le nom du conteneur. Vous les utiliserez dans le code de réception.

### <a name="write-code-to-receive-events"></a>Écrire du code pour recevoir des événements

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com).
2. Créez un fichier appelé `receive.js` et collez-y le code suivant. Pour plus d’informations, consultez les commentaires du code.
    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // create a blob container client and a blob checkpoint store using the client
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // create a consumer client for the event hub by specifying the checkpoint store
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // subscribe for the events and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // update the checkpoint
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // after 30 seconds, stop processing
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
3. N’oubliez pas de spécifier le **valeurs suivantes** dans le code :
    - Chaîne de connexion à l’espace de noms Event Hubs
    - Nom du Event Hub
    - Chaîne de connexion à un compte de stockage Azure
    - Nom du conteneur d’objets blob
5. Ensuite, exécutez la commande `node receive.js` dans une invite de commandes pour exécuter ce fichier. Vous devez voir les messages concernant les événements reçus dans la fenêtre.

    > [!NOTE]
    > Pour obtenir le code source complet avec des remarques plus détaillées, consultez [ce fichier sur GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Félicitations ! Vous recevez maintenant des événements depuis le hub d’événements. Le programme de réception recevra les événements de toutes les partitions du groupe de consommateurs par défaut dans votre Event Hub.

## <a name="next-steps"></a>Étapes suivantes
Consultez ces exemples sur GitHub :

- [Exemples JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Exemples TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
