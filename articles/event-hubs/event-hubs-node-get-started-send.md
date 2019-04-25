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
ms.openlocfilehash: f03bfde8f7ea37989756ad47678369e94b831438
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203225"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Envoyer des événements à ou recevoir des événements d’Azure Event Hubs à l’aide de Node.js

Azure Event Hubs est une plateforme de streaming Big Data et un service d’ingestion d’événements, capable de recevoir et de traiter des millions d’événements par seconde. Les concentrateurs d’événements peuvent traiter et stocker des événements, des données ou la télémétrie produits par des logiciels et appareils distribués. Les données envoyées à un concentrateur d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analyse en temps réel. Pour une présentation détaillée d’Event Hubs, consultez [Vue d’ensemble d’Event Hubs](event-hubs-about.md) et [Fonctionnalités d’Event Hubs](event-hubs-features.md).

Ce didacticiel explique comment créer des applications Node.js à envoyer des événements à ou recevoir des événements à partir d’un concentrateur d’événements.

> [!NOTE]
> Vous pouvez télécharger ce guide de démarrage rapide sous forme d’exemple depuis [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), remplacer les chaînes `EventHubConnectionString` et `EventHubName` par les valeurs de votre hub d’événements, puis l’exécuter. Ou, vous pouvez suivre les étapes de ce didacticiel pour créer le vôtre.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

- Un compte Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- Node.js version 8.x ou ultérieure. Téléchargez la dernière version LTS à partir de [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (recommandé) ou un autre IDE
- **Créer un espace de noms Event Hubs et un concentrateur d’événements**. La première étape consiste à utiliser le [portail Azure](https://portal.azure.com) pour créer un espace de noms de type Event Hubs et obtenir les informations de gestion nécessaires à votre application pour communiquer avec le concentrateur d’événements. Pour créer un espace de noms et un hub d’événements, suivez la procédure décrite dans [cet article](event-hubs-create.md), puis passez aux étapes suivantes de ce tutoriel. Ensuite, obtenez la chaîne de connexion pour l’espace de noms event hub en suivant les instructions de l’article : [Obtenir la chaîne de connexion](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Vous utiliserez la chaîne de connexion plus loin dans ce tutoriel.
- Clone le [dépôt d’exemples GitHub](https://github.com/Azure/azure-event-hubs-node) sur votre ordinateur. 


## <a name="send-events"></a>Envoyer des événements
Cette section vous montre comment créer une application Node.js qui envoie des événements à un concentrateur d’événements. 

### <a name="install-nodejs-package"></a>Installer le package Node.js
Installez le package Node.js pour Azure Event Hubs sur votre ordinateur. 

```shell
npm install @azure/event-hubs
```

Si vous n’avez pas cloné le référentiel Git comme indiqué dans les conditions préalables, téléchargez le [exemple](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) à partir de GitHub. 

Le Kit de développement logiciel (SDK) que vous avez cloné contient plusieurs exemples qui vous montrent comment envoyer des événements à un hub d’événements à l’aide de Node.js. Dans ce démarrage rapide, vous utilisez l’exemple **simpleSender.js**. Pour observer les événements reçus, ouvrez un autre terminal et recevez les événements à l’aide de l’[exemple de réception](event-hubs-node-get-started-receive.md).

1. Ouvrez le projet dans Visual Studio Code. 
2. Créez un fichier nommé **.env** sous le dossier **client**. Copiez et collez les exemples de variables d’environnement du fichier **sample.env** dans le dossier racine.
3. Configurez la chaîne de connexion de votre Event Hub, le nom de l’Event Hub et le point de terminaison de stockage. Pour des instructions sur l’obtention d’une chaîne de connexion pour un Event Hub, consultez [Obtenir la chaîne de connexion](event-hubs-create.md#create-an-event-hubs-namespace).
4. Dans l’interface de ligne de commande Azure, accédez au chemin d’accès du dossier **client**. Installez les packages de nœud et générez le projet en exécutant les commandes suivantes :

    ```shell
    npm i
    npm run build
    ```
5. Commencez à envoyer des événements en exécutant la commande suivante : 

    ```shell
    node dist/examples/simpleSender.js
    ```


### <a name="review-the-sample-code"></a>Réviser l’exemple de code 
Examinez l’exemple de code du fichier simpleSender.js pour envoyer des événements à un Event Hub.

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
const lib_1 = require("../lib");
const dotenv = require("dotenv");
dotenv.config();
const connectionString = "EVENTHUB_CONNECTION_STRING";
const entityPath = "EVENTHUB_NAME";
const str = process.env[connectionString] || "";
const path = process.env[entityPath] || "";

async function main() {
    const client = lib_1.EventHubClient.createFromConnectionString(str, path);
    const data = {
        body: "Hello World!!"
    };
    const delivery = await client.send(data);
    console.log(">>> Sent the message successfully: ", delivery.tag.toString());
    console.log(delivery);
    console.log("Calling rhea-promise sender close directly. This should result in sender getting reconnected.");
    await Object.values(client._context.senders)[0]._sender.close();
    // await client.close();
}

main().catch((err) => {
    console.log("error: ", err);
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

## <a name="receive-events"></a>Recevoir des événements
Ce didacticiel montre comment recevoir des événements d’un Event Hub à l’aide d’Azure [EventProcessorHost](event-hubs-event-processor-host.md) dans une application Node.js. EventProcessorHost (EPH) vous permet de recevoir efficacement des événements d’un Event Hub en créant des récepteurs sur toutes les partitions du groupe de consommateurs d’un Event Hub. Il contrôle les métadonnées de messages reçus à intervalles réguliers dans un Azure Storage Blob. Cette approche permet ultérieurement de continuer à recevoir des messages à partir du point où vous vous étiez arrêté.

Le code associé à ce démarrage rapide est disponible sur [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor).

### <a name="clone-the-git-repository"></a>Cloner le référentiel Git
Téléchargez ou clonez l’[exemple](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/) à partir de GitHub. 

### <a name="install-the-eventprocessorhost"></a>Installer EventProcessorHost
Installez EventProcessorHost pour le module Event Hubs. 

```shell
npm install @azure/event-processor-host
```

### <a name="receive-events-using-eventprocessorhost"></a>Recevoir des événements à l’aide d’EventProcessorHost
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

### <a name="review-the-sample-code"></a>Réviser l’exemple de code 
Voici l’exemple de code permettant de recevoir des événements d’un Event Hub à l’aide de Node.js. Vous pouvez créer manuellement un fichier sampleEph.js et l’exécuter pour recevoir des événements d’un Event Hub. 

  ```javascript
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processor Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path,
        onEphError: (error) => {
          console.log("This handler will notify you of any internal errors that happen " +
          "during partition and lease management: %O", error);
        }
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
Consultez les articles suivants :

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Fonctionnalités et la terminologie dans Azure Event Hubs](event-hubs-features.md)
- [FAQ sur les hubs d’événements](event-hubs-faq.md)
- Consultez d’autres exemples Node.js pour Event Hubs sur [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
