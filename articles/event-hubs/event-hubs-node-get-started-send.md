---
title: Envoyer des événements à Azure Event Hubs avec Node.js | Microsoft Docs
description: Prise en main de l’envoi d’événements à Azure Event Hubs avec Node.js
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/18/2018
ms.author: shvija
ms.openlocfilehash: 413f36a12dee135cc1a7dc99a34d8b7b2be6c46f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801061"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Envoyer des événements vers Azure Event Hubs avec Node.js

Azure Event Hubs est un système de gestion des événements capable de traiter des millions d’événements par seconde, ce qui permet aux applications de traiter et d’analyser d’énormes quantités de données produites par des appareils connectés et autres applications. Une fois les événements collectés dans le hub d'événements, vous pouvez les recevoir et les gérer à l’aide de gestionnaires intra-processus ou par transfert vers d’autres systèmes d’analyse.

Pour en savoir plus sur Event Hubs, voir [Vue d’ensemble d’Event Hubs](event-hubs-about.md).

Ce tutoriel décrit comment envoyer des événements à un hub d’événements à partir d’une application écrite en Node.js. Pour recevoir des événements à l’aide du package d’hôte du processeur d’événements Node.js, consultez l’[article correspondant sur la réception](event-hubs-node-get-started-receive.md).

Le code associé à ce démarrage rapide est stocké sur [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client). 

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

- Node.js version 8.x ou ultérieure. Téléchargez la dernière version LTS à partir de [https://nodejs.org](https://nodejs.org).
- Un compte Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit][] avant de commencer.
- Visual Studio Code (recommandé) ou un autre IDE

## <a name="create-a-namespace-and-event-hub"></a>Créer un concentrateur Event Hub et un espace de noms
La première étape consiste à créer un espace de noms Event Hubs avec un hub d’événements via le portail Azure. Si vous n’avez pas encore d’espace de noms, vous pouvez créer ces entités en suivant les instructions de la rubrique [Créer un espace de noms Event Hubs et un hub d’événements avec le portail Azure](event-hubs-create.md).

## <a name="clone-the-sample-git-repository"></a>Cloner l’exemple de référentiel Git
Clonez l’exemple de référentiel Git de [Github](https://github.com/Azure/azure-event-hubs-node) sur votre ordinateur. 

## <a name="install-nodejs-package"></a>Installer le package Node.js
Installez le package Node.js pour Azure Event Hubs sur votre ordinateur. 

```nodejs
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>Cloner le référentiel Git
Téléchargez ou clonez l’[exemple](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) à partir de GitHub. 

## <a name="send-events"></a>Envoyer des événements
Le Kit de développement logiciel (SDK) que vous avez cloné contient plusieurs exemples qui vous montrent comment envoyer des événements à un hub d’événements à l’aide de Node.js. Dans ce démarrage rapide, vous utilisez l’exemple **simpleSender.js**. Pour observer les événements reçus, ouvrez un autre terminal et recevez les événements à l’aide de l’[exemple de réception](event-hubs-node-get-started-receive.md).

1. Ouvrez le projet dans Visual Studio Code. 
2. Créez un fichier nommé **.env** sous le dossier **client**. Copiez et collez les exemples de variables d’environnement du fichier **sample.env** dans le dossier racine.
3. Configurez la chaîne de connexion au hub d’événements, le nom du hub d’événements et le point de terminaison de stockage. Vous pouvez copier la chaîne de connexion du concentrateur d’événements à partir de la **clé primaire de chaîne connexion** sous **RootManageSharedAccessKey** sur la page Events Hub dans le portail Azure. Pour des instructions détaillées, consultez la rubrique [Obtenir la chaîne de connexion](event-hubs-quickstart-portal.md#create-an-event-hubs-namespace).
4. Dans l’interface de ligne de commande Azure, accédez au chemin d’accès du dossier **client**. Installez les packages de nœud et générez le projet en exécutant les commandes suivantes :

    ```nodejs
    npm i
    npm run build
    ```
5. Commencez à envoyer des événements en exécutant la commande suivante : 

    ```nodejs
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>Passer en revue l’exemple de code 
Voici l’exemple de code permettant d’envoyer des événements à un hub d’événements à l’aide de Node.js. Vous pouvez manuellement créer un fichier sampleSender.js et l’exécuter pour envoyer des événements à un hub d’événements. 


```nodejs
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

N’oubliez pas de définir les variables d’environnement avant d’exécuter le script. Vous pouvez configurer cela dans la ligne de commande, comme indiqué dans l’exemple suivant, ou utiliser le [package dotenv](https://www.npmjs.com/package/dotenv#dotenv). 

```
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Events Hub, consultez les articles suivants :

* [Recevoir des événements à l’aide de Node.js](event-hubs-node-get-started-receive.md)
* [Exemples sur GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/)
* [FAQ sur les hubs d’événements](event-hubs-faq.md)

<!-- Links -->
[compte gratuit]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
