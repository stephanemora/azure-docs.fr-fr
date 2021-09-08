---
title: Référence - Kit de développement logiciel (SDK) JavaScript pour le service Azure Web PubSub
description: La référence décrit le kit de développement logiciel (SDK) JavaScript pour le service Azure Web PubSub
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 1d507f29b184403cb9ef31d84111af60f75c1584
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116023"
---
# <a name="javascript-sdk-for-the-azure-web-pubsub-service"></a>SDK JavaScript pour le service Azure Web PubSub

Il existe 2 bibliothèques proposées pour JavaScript :
- Utilisez la [bibliothèque cliente de services](#service-client-library) pour
    - Envoyer des messages aux hubs et aux groupes.
    - Envoyer des messages à des utilisateurs particuliers et à des connexions.
    - Organiser les utilisateurs et les connexions en groupes.
    - Fermer les connexions
    - Accorder/révoquer/vérifier les autorisations pour une connexion existante
- [Intergiciel express](#express) pour gérer les événements clients entrants
  - Gérer les demandes de validation d’abus
  - Gérer les demandes d’événements client

<a name="service-client-library"></a>

## <a name="azure-web-pubsub-service-client-library-for-javascript"></a>Bibliothèque cliente du service Azure Web PubSub pour JavaScript
Utilisez la bibliothèque pour :

- Envoyer des messages aux hubs et aux groupes.
- Envoyer des messages à des utilisateurs particuliers et à des connexions.
- Organiser les utilisateurs et les connexions en groupes.
- Fermer les connexions
- Accorder/révoquer/vérifier les autorisations pour une connexion existante

[Code source](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub) |
[Package (NPM)](https://www.npmjs.com/package/@azure/web-pubsub) |
[Documentation de référence API](/javascript/api/@azure/web-pubsub/) |
[Documentation de produits](https://aka.ms/awps/doc) |
[Exemples][samples_ref]

### <a name="getting-started"></a>Prise en main

#### <a name="currently-supported-environments"></a>Environnements actuellement pris en charge

- [Node.js](https://nodejs.org/) version 8.x.x ou ultérieure

#### <a name="prerequisites"></a>Prérequis

- Un [abonnement Azure][azure_sub].
- Une instance de service Azure Web PubSub existante.

#### <a name="1-install-the-azureweb-pubsub-package"></a>1. Installez le package `@azure/web-pubsub`

```bash
npm install @azure/web-pubsub
```

#### <a name="2-create-and-authenticate-a-webpubsubserviceclient"></a>2. Créer et authentifier un client WebPubSubService

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
```

Vous pouvez également authentifier le `WebPubSubServiceClient` qui utilise un point de terminaison et un `AzureKeyCredential` :

```js
const { WebPubSubServiceClient, AzureKeyCredential } = require("@azure/web-pubsub");

const key = new AzureKeyCredential("<Key>");
const serviceClient = new WebPubSubServiceClient("<Endpoint>", key, "<hubName>");
```

### <a name="key-concepts"></a>Concepts clés

[!INCLUDE [Terms](includes/terms.md)]

### <a name="examples"></a>Exemples

#### <a name="broadcast-a-json-message-to-all-users"></a>Diffuser un message JSON à tous les utilisateurs

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll({ message: "Hello world!" });
```

#### <a name="broadcast-a-plain-text-message-to-all-users"></a>Diffuser un message en texte brut à tous les utilisateurs

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll("Hi there!", { contentType: "text/plain" });
```

#### <a name="broadcast-a-binary-message-to-all-users"></a>Diffuser un message binaire à tous les utilisateurs

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

const payload = new Uint8Array(10);
await serviceClient.sendToAll(payload.buffer);
```

### <a name="troubleshooting"></a>Dépannage

#### <a name="enable-logs"></a>Activer les journaux d’activité

Vous pouvez définir la variable d’environnement suivante pour afficher les journaux de débogage quand vous utilisez cette bibliothèque.

- Obtention des journaux de débogage à partir de la bibliothèque cliente Signalr

```bash
export AZURE_LOG_LEVEL=verbose
```

Pour obtenir des instructions plus détaillées sur l’activation des journaux, consultez les [@azure/loggerdocuments sur le package ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).

<a name="express"></a>

## <a name="azure-web-pubsub-cloudevents-handlers-for-express"></a>Gestionnaires Azure Web Pubsub CloudEvents pour Express

Utilisez la bibliothèque Express pour :
- Ajouter un intergiciel Web PubSub CloudEvents pour gérer les événements entrants du client
  - Gérer les demandes de validation d’abus
  - Gérer les demandes d’événements client

[Code source](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub-express) |
[Package (NPM)](https://www.npmjs.com/package/@azure/web-pubsub-express) |
[Documentation de référence API](/javascript/api/@azure/web-pubsub-express/) |
[Documentation de produits](https://aka.ms/awps/doc) |
[Exemples][samples_ref]

### <a name="getting-started"></a>Prise en main

#### <a name="currently-supported-environments"></a>Environnements actuellement pris en charge

- [Node.js](https://nodejs.org/) version 8.x.x ou ultérieure
- [Node.js](https://expressjs.com/) version 4.x.x ou ultérieure

#### <a name="prerequisites"></a>Prérequis

- Un [abonnement Azure][azure_sub].
- Un point de terminaison Azure Web PubSub existant.

#### <a name="1-install-the-azureweb-pubsub-express-package"></a>1. Installez le package `@azure/web-pubsub-express`

```bash
npm install @azure/web-pubsub-express
```

#### <a name="2-create-a-webpubsubeventhandler"></a>2. Créez un gestionnaire WebPubSubEventHandler

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler(
  "chat",
  ["https://<yourAllowedService>.webpubsub.azure.com"],
  {
    handleConnect: (req, res) => {
      // auth the connection and set the userId of the connection
      res.success({
        userId: "<userId>"
      });
    }
  }
);

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

### <a name="key-concepts"></a>Concepts clés

#### <a name="client-events"></a>Événements du client

Les événements sont créés pendant le cycle de vie d’une connexion cliente. Par exemple, une connexion cliente WebSocket simple crée un événement `connect` lorsqu’il tente de se connecter au service, un événement `connected` quand il s’est correctement connecté au service, un événement `message` lorsqu’il envoie des messages au service et à un événement `disconnected` lorsqu’il se déconnecte du service.

#### <a name="event-handler"></a>Gestionnaire d'événements

Le gestionnaire d’événements contient la logique permettant de gérer les événements du client. Le gestionnaire d’événements doit être inscrit et configuré dans le service via le portail ou Azure CLI au préalable. L’emplacement d’hébergement de la logique du gestionnaire d’événements est généralement considéré comme côté serveur.

### <a name="troubleshooting"></a>Résolution des problèmes

#### <a name="dump-request"></a>Demande de vidage

Paramétrez `dumpRequest` sur `true` pour afficher les demandes entrantes.

#### <a name="live-trace"></a>Trace dynamique

Utilisez **Live Trace** du portail de service Web PubSub pour afficher le trafic.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript


## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]
