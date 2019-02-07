---
title: Recevoir des événements à l’aide de Node.js - Azure Event Hubs | Microsoft Docs
description: Cet article décrit la procédure à suivre pour créer une application Node.js qui reçoit des événements d’Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6986a5c40b51300b631f3fdce6c3a88b43fb5b11
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730881"
---
# <a name="receive-events-from-azure-event-hubs-using-nodejs"></a>Recevoir des événements d’Azure Event Hubs avec Node.js

Azure Event Hubs est un système de gestion des événements capable de traiter des millions d’événements par seconde, ce qui permet aux applications de traiter et d’analyser d’énormes quantités de données produites par des appareils connectés et autres applications. Une fois les événements collectés dans le hub d'événements, vous pouvez les recevoir et les gérer à l’aide de gestionnaires intra-processus ou par transfert vers d’autres systèmes d’analyse. Vous pouvez également capturer des données d’événement dans Stockage Azure ou Azure Data Lake Store avant leur traitement.  

Pour en savoir plus sur Event Hubs, consultez [Vue d’ensemble d’Event Hubs](event-hubs-about.md).

Ce didacticiel montre comment recevoir des événements d’un Event Hub à l’aide d’Azure [EventProcessorHost](event-hubs-event-processor-host.md) dans une application Node.js. EventProcessorHost (EPH) vous permet de recevoir efficacement des événements d’un Event Hub en créant des récepteurs sur toutes les partitions du groupe de consommateurs d’un Event Hub. Il contrôle les métadonnées de messages reçus à intervalles réguliers dans un Azure Storage Blob. Cette approche permet ultérieurement de continuer à recevoir des messages à partir du point où vous vous étiez arrêté.

Le code associé à ce démarrage rapide est disponible sur [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor).

> [!NOTE]
>  Pour envoyer des événements à Event Hubs avec Node.js, consultez cet article : [Envoyer des événements à Azure Event Hubs avec Node.js](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

- Node.js version 8.x ou ultérieure. Téléchargez la dernière version de LTS à partir de [https://nodejs.org](https://nodejs.org). N’utilisez pas une ancienne version de LTS de Node.js. 
- Un compte Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit][] avant de commencer.

## <a name="create-a-namespace-and-event-hub"></a>Créer un concentrateur Event Hub et un espace de noms
La première étape consiste à créer un espace de noms Event Hubs avec un Event Hub via le portail Azure. Si vous n’en avez pas, vous pouvez créer ces entités en suivant les instructions de l’article [Créer un espace de noms Event Hubs et un Event Hub avec le portail Azure](event-hubs-create.md).

## <a name="create-a-storage-account-and-container"></a>Créer un compte de stockage et un conteneur
Pour utiliser EventProcessorHost, vous devez disposer d’un compte de Stockage Azure. Les informations sur l’état telles que des baux sur des partitions et des points de contrôle dans le flux d’événements sont partagées entre les destinataires à l’aide d’un conteneur Stockage Azure. Vous pouvez créer un compte de Stockage Azure en suivant les instructions de [cet article](../storage/common/storage-quickstart-create-account.md).

## <a name="clone-the-git-repository"></a>Cloner le référentiel Git
Téléchargez ou clonez l’[exemple](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/) à partir de GitHub. 

## <a name="install-the-eventprocessorhost"></a>Installer EventProcessorHost
Installez EventProcessorHost pour le module Event Hubs. 

```shell
npm install @azure/event-processor-host
```

## <a name="receive-events-using-eventprocessorhost"></a>Recevoir des événements à l’aide d’EventProcessorHost
Le Kit de développement logiciel (SDK) que vous avez cloné contient plusieurs exemples qui vous montrent comment recevoir des événements d’un Event Hub à l’aide de Node.js. Dans ce démarrage rapide, vous utilisez l’exemple **singleEPH.js**. Pour observer les événements reçus, ouvrez un autre terminal et envoyez des événements à l’aide de l’[exemple d’envoi](event-hubs-node-get-started-send.md).

1. Ouvrez le projet dans Visual Studio Code. 
2. Créez un fichier nommé **.env** sous le dossier **processor**. Copiez et collez les exemples de variables d’environnement du fichier **sample.env** dans le dossier racine.
3. Configurez la chaîne de connexion de votre Event Hub, le nom de l’Event Hub et le point de terminaison de stockage. Vous pouvez copier la chaîne de connexion de votre Event Hub de la **clé primaire de chaîne de connexion** sous **RootManageSharedAccessKey** sur la page Event Hub dans le portail Azure. Pour des instructions détaillées, consultez [Obtenir la chaîne de connexion](event-hubs-create.md#create-an-event-hubs-namespace).
4. Dans l’interface de ligne de commande Azure, accédez au chemin d’accès du dossier **processor**. Installez les packages de nœud et générez le projet en exécutant les commandes suivantes :

    ```shell
    npm i
    npm run build
    ```
5. Recevez des événements avec votre hôte de processeur d’événements en exécutant la commande suivante :

    ```shell
    node dist/examples/singleEph.js
    ```

## <a name="review-the-sample-code"></a>Réviser l’exemple de code 
Voici l’exemple de code permettant de recevoir des événements d’un Event Hub à l’aide de Node.js. Vous pouvez créer manuellement un fichier sampleEph.js et l’exécuter pour recevoir des événements d’un Event Hub. 

  ```javascript
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processo Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path
      },
      onEphError: (error) => {
        console.log("This handler will notify you of any internal errors that happen " +
        "during partition and lease management: %O", error);
      }
    );
    let count = 0;
    // Message event handler
    const onMessage = async (context/*PartitionContext*/, data /*EventData*/) => {
      console.log(">>>>> Rx message from '%s': '%s'", context.partitionId, data.body);
      count++;
      // let us checkpoint every 100th message that is received across all the partitions.
      if (count % 100 === 0) {
        return await context.checkpoint();
      }
    };
    // Error event handler
    const onError = (error) => {
      console.log(">>>>> Received Error: %O", error);
    };
    // start the EPH
    await eph.start(onMessage, onError);
    // After some time let' say 2 minutes
    await delay(120000);
    // This will stop the EPH.
    await eph.stop();
  }
  
  main().catch((err) => {
    console.log(err);
  });
      
  ```

N’oubliez pas de définir vos variables d’environnement avant d’exécuter le script. Vous pouvez les configurer dans la ligne de commande, comme indiqué dans l’exemple suivant, ou utiliser le [package dotenv](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

Vous trouverez plus d’exemples [ici](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples).


## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une application Node.js qui a reçu des messages d’un hub d’événements. Pour découvrir comment envoyer des événements à un hub d’événements à l’aide de Node.js, consultez [Envoyer des événements à un hub d’événements - Node.js](event-hubs-node-get-started-send.md).

<!-- Links -->
[compte gratuit]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
