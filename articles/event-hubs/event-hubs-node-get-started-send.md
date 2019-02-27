---
title: Envoyer des événements à l’aide de Node.js - Azure Event Hubs | Microsoft Docs
description: Cet article décrit la procédure à suivre pour créer une application Node.js qui envoie des événements d’Azure Event Hubs.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: ec3182d11f1b2ffa31acd05fa1f2db695f3f2cf7
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447716"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Envoyer des événements vers Azure Event Hubs avec Node.js

Azure Event Hubs est une plateforme de streaming Big Data et un service d’ingestion d’événements, capable de recevoir et de traiter des millions d’événements par seconde. Les concentrateurs d’événements peuvent traiter et stocker des événements, des données ou la télémétrie produits par des logiciels et appareils distribués. Les données envoyées à un concentrateur d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analyse en temps réel. Pour une présentation détaillée d’Event Hubs, consultez [Vue d’ensemble d’Event Hubs](event-hubs-about.md) et [Fonctionnalités d’Event Hubs](event-hubs-features.md).

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


## <a name="review-the-sample-code"></a>Réviser l’exemple de code 
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

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez envoyé des messages à un hub d’événements à l’aide de Node.js. Pour découvrir comment recevoir des événements depuis un hub d’événements à l’aide de Node.js, consultez [Recevoir des événements depuis Event Hub - Node.js](event-hubs-node-get-started-receive.md)

Consultez d’autres exemples Node.js pour Event Hubs sur [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
