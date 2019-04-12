---
title: Se connecter à Azure Media Services v3 API - Node.js
description: Découvrez comment vous connecter à Media Services v3 API avec Node.js.
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
ms.openlocfilehash: 40880a2c28ce28a671930ef8837082247e61e24b
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495086"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Se connecter à l’API de Media Services v3 - Node.js

Cet article vous montre comment se connecter à la node.js de v3 Azure Media Services SDK à l’aide de la connexion du principal du service dans la méthode.

## <a name="prerequisites"></a>Conditions préalables

- Installez [Node.js](https://nodejs.org/en/download/).
- [Créer un compte Media Services](create-account-cli-how-to.md). Veillez à mémoriser le nom de groupe de ressources et le nom du compte Media Services.

## <a name="create-packagejson"></a>Créer package.json

1. Créez un fichier package.json à l’aide de votre éditeur favori.
1. Ouvrez le fichier et collez le code suivant :

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
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>Pour vous assurer que vous utilisez le dernier package Azure Media Services, vérifiez [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Kit de développement logiciel de stockage. Utilisé lors du téléchargement des fichiers dans les ressources.|
|`ms-rest-azure`| Utilisé pour la connexion.|

Vous pouvez exécuter la commande suivante pour vous assurer que vous utilisez le dernier package :

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Se connecter à un client Node.js

1. Créez un fichier .js à l’aide de votre éditeur favori.
1. Ouvrez le fichier et collez-y le code suivant.
1. Définissez les valeurs dans la section « Configuration de point de terminaison » aux valeurs que vous avez obtenus [accéder aux API](access-api-cli-how-to.md).

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

Ouvrez une invite de commandes. Accédez au répertoire de l’exemple et exécutez les commandes suivantes :

```
npm install 
node index.js
```

## <a name="see-also"></a>Voir aussi

- [Concepts Media Services](concepts-overview.md)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Étapes suivantes

Explorez la documentation [Informations de référence sur Node.js](https://aka.ms/ams-v3-nodejs-ref) de Media Services et regardez les [exemples](https://github.com/Azure-Samples/media-services-v3-node-tutorials) qui montrent comment utiliser l’API Media Services avec node.js.

