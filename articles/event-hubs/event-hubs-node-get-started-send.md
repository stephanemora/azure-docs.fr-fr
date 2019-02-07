---
title: Envoyer des événements à l’aide de Node.js - Azure Event Hubs | Microsoft Docs
description: Cet article décrit la procédure à suivre pour créer une application Node.js qui envoie des événements d’Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 7281e6bb2dda5dc3fddb5f39bf271293ebb88a73
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55732009"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Envoyer des événements vers Azure Event Hubs avec Node.js

Azure Event Hubs est une plateforme de diffusion de données volumineuses et un service d’ingestion d’événements, capable de recevoir et de traiter des millions d’événements par seconde. Les concentrateurs d’événements peuvent traiter et stocker des événements, des données ou la télémétrie produits par des logiciels et appareils distribués. Les données envoyées à un concentrateur d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analyse en temps réel. Pour une présentation détaillée d’Event Hubs, consultez [Vue d’ensemble d’Event Hubs](event-hubs-about.md) et [Fonctionnalités d’Event Hubs](event-hubs-features.md).

Ce tutoriel décrit comment envoyer des événements à un hub d’événements à partir d’une application écrite en Node.js.

> [!NOTE]
> Vous pouvez télécharger ce guide de démarrage rapide sous forme d’exemple depuis [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), remplacer les chaînes `EventHubConnectionString` et `EventHubName` par les valeurs de votre hub d’événements, puis l’exécuter. Ou, vous pouvez suivre les étapes de ce didacticiel pour créer le vôtre.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

- Node.js version 8.x ou ultérieure. Téléchargez la dernière version LTS à partir de [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (recommandé) ou un autre IDE

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Création d’un espace de noms Event Hubs et d’un concentrateur d’événements
La première étape consiste à utiliser le [portail Azure](https://portal.azure.com) pour créer un espace de noms de type Event Hubs et obtenir les informations de gestion nécessaires à votre application pour communiquer avec le concentrateur d’événements. Pour créer un espace de noms et un hub d’événements, suivez la procédure décrite dans [cet article](event-hubs-create.md), puis passez aux étapes suivantes de ce tutoriel.

Pour obtenir la chaîne de connexion de l’espace de noms du hub d’événements, suivez les instructions de l’article : [Obtenir la chaîne de connexion](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Vous utiliserez la chaîne de connexion plus loin dans ce tutoriel.

## <a name="clone-the-sample-git-repository"></a>Cloner l’exemple de référentiel Git
Clonez l’exemple de référentiel Git de [GitHub](https://github.com/Azure/azure-event-hubs-node) sur votre ordinateur. 

## <a name="install-nodejs-package"></a>Installer le package Node.js
Installez le package Node.js pour Azure Event Hubs sur votre ordinateur. 

```shell
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>Cloner le référentiel Git
Téléchargez ou clonez l’[exemple](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) à partir de GitHub. 

## <a name="send-events"></a>Envoyer des événements
Le Kit de développement logiciel (SDK) que vous avez cloné contient plusieurs exemples qui vous montrent comment envoyer des événements à un hub d’événements à l’aide de Node.js. Dans ce démarrage rapide, vous utilisez l’exemple **simpleSender.js**. Pour observer les événements reçus, ouvrez un autre terminal et recevez les événements à l’aide de l’[exemple de réception](event-hubs-node-get-started-receive.md).

1. Ouvrez le projet dans Visual Studio Code. 
2. Créez un fichier nommé **.env** sous le dossier **client**. Copiez et collez les exemples de variables d’environnement du fichier **sample.env** dans le dossier racine.
3. Configurez la chaîne de connexion de votre Event Hub, le nom de l’Event Hub et le point de terminaison de stockage. Vous pouvez copier la chaîne de connexion de votre Event Hub de la **clé primaire de chaîne de connexion** sous **RootManageSharedAccessKey** sur la page Event Hub dans le portail Azure. Pour des instructions détaillées, consultez la rubrique [Obtenir la chaîne de connexion](event-hubs-create.md#create-an-event-hubs-namespace).
4. Dans l’interface de ligne de commande Azure, accédez au chemin d’accès du dossier **client**. Installez les packages de nœud et générez le projet en exécutant les commandes suivantes :

    ```shell
    npm i
    npm run build
    ```
5. Commencez à envoyer des événements en exécutant la commande suivante : 

    ```shell
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>Passer en revue l’exemple de code 
Voici l’exemple de code permettant d’envoyer des événements à un hub d’événements à l’aide de Node.js. Vous pouvez manuellement créer un fichier sampleSender.js et l’exécuter pour envoyer des événements à un hub d’événements. 


```javascript
const { EventHubClient, EventPosition } = require('@azure/event-hubs');

const client = EventHubClient.createFromConnectionString(process.env["EVENTHUB_CONNECTION_STRING"], process.env["EVENTHUB_NAME"]);

async function main() {
    // NOTE: For receiving events from Azure Stream Analytics, please send Events to an EventHub where the body is a JSON object/array.
    // const eventData = { body: { "message": "Hello World" } };
    const data = { body: "Hello World 1" };
    const delivery = await client.send(data);
    console.log("message sent successfully.");
}

main().catch((err) => {
    console.log(err);
});

```

N’oubliez pas de définir les variables d’environnement avant d’exécuter le script. Vous pouvez les configurer dans la ligne de commande, comme indiqué dans l’exemple suivant, ou utiliser le [package dotenv](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez envoyé des messages à un hub d’événements à l’aide de Node.js. Pour découvrir comment recevoir des événements depuis un hub d’événements à l’aide de Node.js, consultez [Recevoir des événements depuis Event Hub - Node.js](event-hubs-node-get-started-receive.md)

Consultez d’autres exemples Node.js pour Event Hubs sur [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
