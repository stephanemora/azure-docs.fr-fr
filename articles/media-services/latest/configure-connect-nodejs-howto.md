---
title: Se connecter à l’API Azure Media Services v3 – Node.js
description: Cet article explique comment se connecter à l’API Media Services v3 avec Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/17/2021
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: fcb9fd9f0539b42d9253db783fd5da840f358e66
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960722"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Se connecter à l’API Media Services v3 – Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Cet article explique comment se connecter au kit SDK node.js Azure Media Services v3 suivant la méthode de connexion au principal de service. Vous travaillerez avec les fichiers contenus dans le dépôt d’exemples *media-services-v3-node-tutorials*. L’exemple *HelloWorld-ListAssets* contient le code pour la connexion, puis liste les actifs multimédias dans le compte.

## <a name="prerequisites"></a>Prérequis

- Une installation de Visual Studio Code.
- Installez [Node.js](https://nodejs.org/en/download/).
- Installez [Typescript](https://www.typescriptlang.org/download).
- [Créer un compte Media Services](./account-create-how-to.md). Veillez à mémoriser le nom du groupe de ressources et le nom du compte Media Services.
- Créez un principal du service pour votre application. Consultez [Accéder aux API](./access-api-howto.md).<br/>**Conseil pro** Laissez cette fenêtre ouverte ou copiez tout le contenu de l’onglet JSON dans le Bloc-notes. 
- Procurez-vous la dernière version du [kit de développement logiciel (SDK) AzureMediaServices pour JavaScript](https://www.npmjs.com/package/@azure/arm-mediaservices).

> [!IMPORTANT]
> Passez en revue les [conventions de nommage](media-services-apis-overview.md#naming-conventions) Azure Media Services pour comprendre les restrictions de nommage importantes concernant les entités.

## <a name="clone-the-nodejs-samples-repo"></a>Cloner le dépôt d’exemples Node.JS

Vous travaillerez avec certains fichiers contenus dans des exemples Azure. Clonez le dépôt d’exemples Node.JS.

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

## <a name="install-the-packages"></a>Installer les packages

### <a name="install-azurearm-mediaservices"></a>Installer @azure/arm-mediaservices

```bash
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>Installer @azure/ms-rest-nodeauth

Installez la version minimale de « @azure/ms-rest-nodeauth » : « ^3.0.0 ».

```bash
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

Pour cet exemple, vous utiliserez les packages suivants dans le fichier `package.json`.

|Package|Description|
|---|---|
|`@azure/arm-mediaservices`|Kit SDK Azure Media Services. <br/>Pour veiller à utiliser le dernier package Azure Media Services, vérifiez [npm install @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices).|
|`@azure/ms-rest-nodeauth` | Nécessaire pour l’authentification AAD à l’aide d’un principal de service ou d’une identité managée|
|`@azure/storage-blob`|Kit SDK Stockage servant à charger des fichiers dans les ressources.|
|`@azure/ms-rest-js`| Package servant à se connecter.|
|`@azure/storage-blob` | Utilisé pour charger et télécharger des fichiers dans Acctifs multimédias dans Azure Media Services pour l’encodage.|
|`@azure/abort-controller`| Utilisé avec le client de stockage pour faire expirer les opérations de téléchargement de longue durée|

### <a name="create-the-packagejson-file"></a>Créer le fichier package.json

1. Créez un fichier `package.json` dans votre éditeur favori.
1. Ouvrez le fichier et collez-y le code suivant :

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.ts",
  "dependencies": {
    "@azure/arm-mediaservices": "^8.0.0",
    "@azure/abort-controller": "^1.0.2",
    "@azure/ms-rest-nodeauth": "^3.0.6",
    "@azure/storage-blob": "^12.4.0",
  }
}
```

## <a name="connect-to-nodejs-client-using-typescript"></a>Se connecter à un client Node.js à l’aide de TypeScript



### <a name="sample-env-file"></a>Exemple de fichier *.env*

Copiez le contenu de ce fichier dans un fichier nommé *.env*. Il doit être stocké à la racine de votre référentiel de travail. Il s’agit des valeurs que vous avez obtenues à partir de la page Accès aux API pour votre compte Media Services dans le portail.

Une fois le fichier *.env* créé, vous pouvez commencer à utiliser les exemples.

```nodejs
# Values from the API Access page in the portal
AZURE_CLIENT_ID=""
AZURE_CLIENT_SECRET= ""
AZURE_TENANT_ID= ""

# Change this to match your AAD Tenant domain name. 
AAD_TENANT_DOMAIN = "microsoft.onmicrosoft.com"

# Set this to your Media Services Account name, resource group it is contained in, and location
AZURE_MEDIA_ACCOUNT_NAME = ""
AZURE_LOCATION= ""
AZURE_RESOURCE_GROUP= ""

# Set this to your Azure Subscription ID
AZURE_SUBSCRIPTION_ID= ""

# You must change these if you are using Gov Cloud, China, or other non standard cloud regions
AZURE_ARM_AUDIENCE= "https://management.core.windows.net"
AZURE_ARM_ENDPOINT="https://management.azure.com"

# DRM Testing
DRM_SYMMETRIC_KEY="add random base 64 encoded string here"
```

## <a name="run-the-sample-application-helloworld-listassets"></a>Exécuter l’exemple d’application *HelloWorld-ListAssets*

1. Accédez au dossier *AMSv3Samples*.

```bash
cd AMSv3Samples
```

2. Installez les packages utilisés dans le fichier *packages.json*.

```
npm install 
```

3. Accédez au dossier *HelloWorld-ListAssets*.

```bash
cd HelloWorld-ListAssets
```

4. Lancez Visual Studio Code à partir du dossier AMSv3Samples. Cela est nécessaire pour effectuer le lancement à partir du dossier où se trouvent le dossier « .vscode » et les fichiers tsconfig.js.

```dotnetcli
cd ..
code .
```

Ouvrez le dossier *HelloWorld-ListAssets*, puis ouvrez le fichier *index.ts* dans l’éditeur Visual Studio Code.

Tout en étant dans le fichier *index.ts*, appuyez sur F5 pour lancer le débogueur. Une liste d’actifs multimédias doit s’afficher si le compte en contient déjà. Si le compte est vide, une liste vide s’affiche.  

Pour voir rapidement les actifs multimédias, utilisez le portail afin de charger quelques fichiers vidéo. Les actifs multimédias sont créés automatiquement, et une nouvelle exécution de ce script retourne leurs noms.

### <a name="a-closer-look-at-the-helloworld-listassets-sample"></a>Analyse détaillée de l’exemple *HelloWorld-ListAssets*

L’exemple *HelloWorld-ListAssets* illustre comment se connecter au client Media Services avec un principal de service et lister les actifs multimédias dans le compte. Pour obtenir une explication détaillée de ce qu’il fait, consultez les commentaires dans le code.

```ts
import * as msRestNodeAuth from "@azure/ms-rest-nodeauth";
import { AzureMediaServices } from '@azure/arm-mediaservices';
import { AzureMediaServicesOptions } from "@azure/arm-mediaservices/esm/models";
// Load the .env file if it exists
import * as dotenv from "dotenv";
dotenv.config();

export async function main() {
  // Copy the samples.env file and rename it to .env first, then populate it's values with the values obtained 
  // from your Media Services account's API Access page in the Azure portal.
  const clientId = process.env.AZURE_CLIENT_ID as string;
  const secret = process.env.AZURE_CLIENT_SECRET as string;
  const tenantDomain = process.env.AAD_TENANT_DOMAIN as string;
  const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID as string;
  const resourceGroup = process.env.AZURE_RESOURCE_GROUP as string;
  const accountName = process.env.AZURE_MEDIA_ACCOUNT_NAME as string;

  let clientOptions: AzureMediaServicesOptions = {
    longRunningOperationRetryTimeout: 5, // set the time out for retries to 5 seconds
    noRetryPolicy: false // use the default retry policy.
  }

  const creds = await msRestNodeAuth.loginWithServicePrincipalSecret(clientId, secret, tenantDomain);
  const mediaClient = new AzureMediaServices(creds, subscriptionId, clientOptions);

  // List Assets in Account
  console.log("Listing Assets Names in account:")
  var assets = await mediaClient.assets.list(resourceGroup, accountName);

  assets.forEach(asset => {
    console.log(asset.name);
  });

  if (assets.odatanextLink) {
    console.log("There are more than 1000 assets in this account, use the assets.listNext() method to continue listing more assets if needed")
    console.log("For example:  assets = await mediaClient.assets.listNext(assets.odatanextLink)");
  }
}

main().catch((err) => {
  console.error("Error running sample:", err.message);
});
```

## <a name="more-samples"></a>Autres exemples

Les exemples suivants sont disponibles dans le [dépôt](https://github.com/Azure-Samples/media-services-v3-node-tutorials).

|Nom du projet|Cas d’usage|
|---|---|
|Live/index.ts| Exemple de base de streaming en direct. **AVERTISSEMENT** : Veillez à vérifier que toutes les ressources sont nettoyées et qu’elles ne sont plus facturées dans le portail lors du streaming en direct|
|StreamFilesSample/index.ts| Exemple de base pour le chargement d’un fichier local ou l’encodage à partir d’une URL source. L’exemple montre comment utiliser le SDK de stockage pour télécharger du contenu et comment diffuser en streaming sur un lecteur |
|StreamFilesWithDRMSample/index.ts| Illustre comment encoder et diffuser en streaming à l’aide de Widevine et PlayReady DRM |
|VideoIndexerSample/index.ts| Exemple d’utilisation des présélections de l’Analyseur de vidéo et d'audio pour générer des métadonnées et des insights à partir d’un fichier vidéo ou audio |

## <a name="see-also"></a>Voir aussi

- [Documentation de référence pour les modules Azure Media Services pour Node.js](/javascript/api/overview/azure/media-services)
- [Azure pour les développeurs JavaScript et Node.js](/azure/developer/javascript/)
- [Code source de Media Services dans le dépôt GitHub @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentation sur les packages Azure pour les développeurs Node.js](/javascript/api/overview/azure/)
- [Concepts Media Services](concepts-overview.md)
- [npm install @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure pour les développeurs JavaScript et Node.js](/azure/developer/javascript/)
- [Code source de Media Services dans le dépôt @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Étapes suivantes

Explorez la documentation [Informations de référence sur Node.js](/javascript/api/overview/azure/mediaservices/management) de Media Services et regardez les [exemples](https://github.com/Azure-Samples/media-services-v3-node-tutorials) qui montrent comment utiliser l’API Media Services avec node.js.
