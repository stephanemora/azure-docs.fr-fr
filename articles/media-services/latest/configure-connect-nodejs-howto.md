---
title: Se connecter à l’API Azure Media Services v3 – Node.js
description: Cet article explique comment se connecter à l’API Media Services v3 avec Node.js.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 0381a2e2b8fd2a8b60e7cb702e0336a5678df057
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896096"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Se connecter à l’API Media Services v3 – Node.js

Cet article explique comment se connecter au kit de développement logiciel (SDK) Node.js Azure Media Services v3 suivant la méthode de connexion au principal du service.

## <a name="prerequisites"></a>Prérequis

- Installez [Node.js](https://nodejs.org/en/download/).
- [Créer un compte Media Services](create-account-cli-how-to.md). Veillez à mémoriser le nom du groupe de ressources et le nom du compte Media Services.

> [!IMPORTANT]
> Examinez les [conventions d’appellation](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Créer un fichier package.json

1. Créez un fichier package.json dans votre éditeur favori.
1. Ouvrez le fichier et collez-y le code suivant :

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

Les packages suivants doivent être spécifiés :

|Package|Description|
|---|---|
|`azure-arm-mediaservices`|Kit SDK Azure Media Services. <br/>Pour vérifier que vous utilisez le dernier package Azure Media Services, exécutez [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Kit SDK Stockage servant à charger des fichiers dans les ressources.|
|`ms-rest-azure`| Package servant à se connecter.|

Vous pouvez exécuter la commande suivante pour vérifier que vous utilisez le dernier package :

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Se connecter à un client Node.js

1. Créez un fichier .js dans votre éditeur favori.
1. Ouvrez le fichier et collez-y le code suivant.
1. Remplacez les valeurs de la section « endpoint config » par celles que vous avez obtenues dans [Accéder aux API](access-api-cli-how-to.md).

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>Exécutez l'application.

Ouvrez une invite de commandes. Accédez au répertoire de l’exemple et exécutez les commandes suivantes :

```
npm install 
node index.js
```

## <a name="see-also"></a>Voir aussi

- [Concepts Media Services](concepts-overview.md)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Étapes suivantes

Explorez la documentation [Informations de référence sur Node.js](/javascript/api/overview/azure/mediaservices/management) de Media Services et regardez les [exemples](https://github.com/Azure-Samples/media-services-v3-node-tutorials) qui montrent comment utiliser l’API Media Services avec node.js.

