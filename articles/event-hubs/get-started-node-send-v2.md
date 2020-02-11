---
title: Envoyer ou recevoir des événements d’Azure Event Hubs à l’aide de Node.js (dernière version)
description: Cet article décrit la procédure à suivre pour créer une application Node.js qui reçoit des événements d’Azure Event Hubs et lui en envoie à l’aide du dernier package azure/event-hubs version 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: b523e4a7b463564cbfeb407c91b7bb05317f8166
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906373"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-nodejs--azureevent-hubs-version-5"></a>Recevoir des événements des hubs d’événements ou leur en envoyer à l’aide de Node.js (azure/event-hubs version 5)

Azure Event Hubs est une plateforme de streaming Big Data et un service d’ingestion d’événements, capable de recevoir et de traiter des millions d’événements par seconde. Les hubs d’événements peuvent traiter et stocker des événements, des données ou la télémétrie produits par des logiciels et appareils distribués. Les données envoyées à un hub d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analytique en temps réel. Pour plus d’informations, consultez [Vue d’ensemble d’Event Hubs](event-hubs-about.md) et [Fonctionnalités d’Event Hubs](event-hubs-features.md).

Ce guide de démarrage rapide montre comment créer des applications Node.js qui peuvent recevoir des événements d’un hub d’événements ou lui en envoyer.

> [!IMPORTANT]
> Ce guide de démarrage rapide utilise la version 5 du SDK Azure Event Hubs Java Script. Pour un guide de démarrage rapide qui utilise la version 2 du SDK JavaScript, consultez [cet article](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer ce démarrage rapide, vous avez besoin de ce qui suit :

- Un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.  
- Node.j version 8.x ou ultérieure. Téléchargez la [version LTS (prise en charge à long terme)](https://nodejs.org) la plus récente.  
- Visual Studio Code (recommandé) ou tout autre environnement de développement intégré (IDE).  
- Un hub d’événements et un espace de noms Event Hubs actifs. Pour les créer, effectuez les étapes suivantes : 

   1. Dans le [portail Azure](https://portal.azure.com), créez un espace de noms de type *Event Hubs*, puis obtenez les informations de gestion nécessaires à votre application pour communiquer avec le hub d’événements. 
   1. Pour créer l’espace de noms et le hub d’événements, suivez les instructions du [Guide de démarrage rapide : Créer un hub d’événement à l’aide du portail Azure](event-hubs-create.md).
   1. Continuez en suivant les instructions de ce guide de démarrage rapide. 
   1. Pour obtenir la chaîne de connexion de votre espace de noms Event Hub, suivez les instructions indiquées dans [Obtenir une chaîne de connexion](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Enregistrez cette chaîne de connexion pour l’utiliser plus tard dans ce guide de démarrage rapide.

### <a name="install-the-npm-package"></a>Installer le package npm
Pour installer le [package npm (Node Package Manager) d’Event Hubs](https://www.npmjs.com/package/@azure/event-hubs), ouvrez une invite de commandes dont le chemin d’accès contient *npm*, remplacez le répertoire par le dossier où vous souhaitez conserver vos exemples, puis exécutez cette commande :

```shell
npm install @azure/event-hubs
```

Pour le côté récepteur, vous avez besoin d’installer deux autres packages. Dans ce guide de démarrage rapide, vous utilisez le stockage Blob Azure pour conserver les points de contrôle afin que le programme ne lise pas les événements qu’il a déjà lus. Il effectue des points de contrôle de métadonnées sur les messages reçus à intervalles réguliers dans un blob. Cette approche permet ultérieurement de continuer à recevoir des messages à partir du point où vous vous étiez arrêté.

Exécutez les commandes suivantes :

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Envoyer des événements

Dans cette section, vous créez une application Node.js pour envoyer des événements à un hub d’événements.

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com).
1. Créez un fichier appelé *send.js* et collez-y le code suivant :

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. Dans le code, utilisez des valeurs réelles pour remplacer les valeurs suivantes :
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. Exécutez `node send.js` pour exécuter ce fichier. Cette commande envoie un lot de trois événements à votre hub d’événements.
1. Dans le portail Azure, vérifiez que le hub d’événements a reçu les messages. Dans la section **Métriques**, basculez vers l’affichage **Messages**. Actualisez la page pour mettre à jour le graphique. Cela peut prendre quelques secondes pour indiquer que les messages ont été reçus.

    [![Vérifier que le hub d’événements a reçu les messages](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Pour obtenir le code source complet, y compris des commentaires d’informations supplémentaires, accédez à la [page GitHub sendEvents.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Félicitations ! Vous venez d’envoyer des événements à un hub d’événements.


## <a name="receive-events"></a>Recevoir des événements
Dans cette section, vous recevez des événements d’un hub d’événements à l’aide d’un magasin de points de contrôle de stockage Blob Azure dans une application Node.js. Il effectue des points de contrôle de métadonnées sur les messages reçus à intervalles réguliers dans un blob Stockage Azure. Cette approche permet ultérieurement de continuer à recevoir des messages à partir du point où vous vous étiez arrêté.

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Créer un compte de stockage Azure et un conteneur d’objets blob
Pour créer un compte de stockage Azure et un conteneur d’objets blob dans celui-ci, effectuez les actions suivantes :

1. [Créer un compte de stockage Azure](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Créer un conteneur d’objets blob dans le compte de stockage](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [Obtenir la chaîne de connexion au compte de stockage](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Veillez à enregistrer la chaîne de connexion et le nom du conteneur en vue de les utiliser dans le code de réception.

### <a name="write-code-to-receive-events"></a>Écrire du code pour recevoir des événements

1. Ouvrez votre éditeur favori, tel que [Visual Studio Code](https://code.visualstudio.com).
1. Créez un fichier appelé *receive.js* et collez-y le code suivant :

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
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
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
1. Dans le code, utilisez des valeurs réelles pour remplacer les valeurs suivantes :
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. Ensuite, exécutez `node receive.js` dans une invite de commandes pour exécuter ce fichier. La fenêtre doit afficher les messages relatifs aux événements reçus.

    > [!NOTE]
    > Pour obtenir le code source complet, y compris des commentaires d’informations supplémentaires, accédez à la [page GitHub receiveEventsUsingCheckpointStore.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Félicitations ! Vous recevez maintenant des événements depuis votre hub d’événements. Le programme récepteur recevra les événements de toutes les partitions du groupe de consommateurs par défaut dans le hub d’événements.

## <a name="next-steps"></a>Étapes suivantes
Consultez ces exemples sur GitHub :

- [Exemples JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Exemples TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
