---
title: Démarrage rapide avec la bibliothèque de client JavaScript Détecteur d’anomalies (multivarié)
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
ms.openlocfilehash: e55b4329105230f023d890983c79aa6c5244009d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114339758"
---
Démarrez avec la bibliothèque de client Détecteur d’anomalies (multivarié) pour JavaScript. Effectuez les étapes suivantes pour installer le package et commencer à utiliser les algorithmes fournis par le service. Les nouvelles API de détection d’anomalie multivariée permettent aux développeurs d’intégrer facilement l’intelligence artificielle avancée pour détecter les anomalies à partir de groupes de métriques, sans avoir besoin d’une connaissance du machine learning ni de données étiquetées. Les dépendances et inter-corrélations entre différents signes sont automatiquement comptabilisées comme des facteurs clés. Cela vous permet de protéger de manière proactive vos systèmes complexes contre les défaillances.

Utilisez la bibliothèque de client Détecteur d’anomalies (multivarié) pour JavaScript pour :

* Détecter les anomalies au niveau du système à partir d’un groupe de séries chronologiques.
* Quand des séries chronologiques individuelles ne contiennent pas beaucoup d’informations et que vous devez examiner tous les signes pour détecter un problème.
* La maintenance prédictive des ressources physiques coûteuses avec des dizaines ou des centaines de types de capteurs différents mesurant divers aspects de l’intégrité du système.

[Documentation de référence sur la bibliothèque](/javascript/api/overview/azure/ai-anomaly-detector-readme) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/anomalydetector/ai-anomaly-detector) | [Package (npm)](https://www.npmjs.com/package/@azure/ai-anomaly-detector) | [Exemple de code](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/anomalydetector/ai-anomaly-detector/samples/v3/javascript/sample_multivariate_detection.js)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* Version actuelle de [Node.js](https://nodejs.org/)
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Créer une ressource Détecteur d’anomalies"  target="_blank">créez une ressource Détecteur d’anomalies </a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Attendez qu’elle se déploie, puis cliquez sur le bouton **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Détecteur d’anomalies. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. 

```console
mkdir myapp && cd myapp
```

Exécutez la commande `npm init` pour créer une application de nœud avec un fichier `package.json`. 

```console
npm init
```

Créez un fichier nommé `index.js` et importez les bibliothèques suivantes :
```javascript
'use strict'

const fs = require('fs');
const parse = require("csv-parse/lib/sync");
const { AnomalyDetectorClient } = require('@azure/ai-anomaly-detector');
const { AzureKeyCredential } = require('@azure/core-auth');
```

Créez des variables pour le point de terminaison et la clé Azure de votre ressource. Créez une autre variable pour l’exemple de fichier de données.

> [!NOTE]
> Vous avez toujours la possibilité d’utiliser l’une des deux clés. Cela permet d’autoriser la rotation des clés sécurisées. Dans le cadre de ce guide de démarrage rapide, utilisez la première clé. 
   

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

Pour utiliser les API multivariées Détecteur d’anomalies, vous devez d’abord entraîner vos propres modèles. Les données d’entraînement sont un ensemble de plusieurs séries chronologiques qui satisfont les exigences suivantes :

Chaque série chronologique doit être un fichier CSV comportant deux (et seulement deux) colonnes, « timestamp » et « value » (tout en minuscules) en ligne d’en-tête. Les valeurs « timestamp » doivent être conformes à la norme ISO 8601 ; la colonne « value » peut contenir des entiers ou des nombres décimaux avec n’importe quel nombre de décimales. Par exemple :

|timestamp | value|
|-------|-------|
|2019-04-01T00:00:00Z| 5|
|2019-04-01T00:01:00Z| 3.6|
|2019-04-01T00:02:00Z| 4|
|`...`| `...` |

Chaque fichier CSV doit être nommé d’après une variable différente qui sera utilisée pour entraîner le modèle. Par exemple, « température.csv » et « humidité.csv ». Tous les fichiers CSV doivent être compressés dans un seul fichier zip ne contenant aucun sous-dossier. Le fichier zip peut porter le nom de votre choix. Le fichier zip doit être chargé dans le stockage Blob Azure. Une fois que vous avez généré l’URL des signatures d’accès partagé (SAS) des objets blob pour le fichier zip, vous pouvez l’utiliser pour l’entraînement. Reportez-vous à ce document pour savoir comment générer des URL SAS à partir du stockage Blob Azure.

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Installez les packages NPM `ms-rest-azure` et `azure-ai-anomalydetector`. La bibliothèque d’analyse de volume partagé de cluster est également utilisée dans ce guide de démarrage rapide :

```console
npm install @azure/ai-anomaly-detector csv-parse
```

Le fichier `package.json` de votre application sera mis à jour avec les dépendances.

## <a name="code-examples"></a>Exemples de code

Ces extraits de code vous montrent comment effectuer les tâches suivantes avec la bibliothèque de client Détecteur d’anomalies pour Node.js :

* [Authentifier le client](#authenticate-the-client)
* [Entraîner un modèle](#train-a-model)
* [Détecter les anomalies](#detect-anomalies)
* [Exporter le modèle](#export-model)
* [Supprimer le modèle](#delete-model)

## <a name="authenticate-the-client"></a>Authentifier le client

Instanciez un objet `AnomalyDetectorClient` avec votre point de terminaison et vos informations d’identification.

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey));
```

## <a name="train-a-model"></a>Entraîner un modèle

### <a name="construct-a-model-result"></a>Construire un résultat de modèle

Nous devons tout d’abord construire une demande de modèle. Vérifiez que les heures de début et de fin concordent avec votre source de données.

```javascript
const Modelrequest = {
      source: data_source,
      startTime: new Date(2021,0,1,0,0,0),
      endTime: new Date(2021,0,2,12,0,0),
      slidingWindow:200
    };    
```

### <a name="train-a-new-model"></a>Entraîner un nouveau modèle

Vous devrez passer votre demande de modèle à la méthode `trainMultivariateModel` de client Détecteur d’anomalies.

```javascript
console.log("Training a new model...")
const train_response = await client.trainMultivariateModel(Modelrequest)
const model_id = train_response.location?.split("/").pop() ?? ""
console.log("New model ID: " + model_id)
```

Pour vérifier si l’entraînement de votre modèle est terminé, vous pouvez suivre l’état du modèle :

```javascript
let model_response = await client.getMultivariateModel(model_id)
let model_status = model_response.modelInfo?.status

while (model_status != 'READY'){
    await sleep(10000).then(() => {});
    model_response = await client.getMultivariateModel(model_id)
    model_status = model_response.modelInfo?.status
}

console.log("TRAINING FINISHED.")
```

## <a name="detect-anomalies"></a>Détecter les anomalies

Utilisez les fonctions `detectAnomaly` et `getDectectionResult` pour déterminer s’il existe des anomalies dans votre source de données.

```javascript
console.log("Start detecting...")
const detect_request = {
    source: data_source,
    startTime: new Date(2021,0,2,12,0,0),
    endTime: new Date(2021,0,3,0,0,0)
};
const result_header = await client.detectAnomaly(model_id, detect_request)
const result_id = result_header.location?.split("/").pop() ?? ""
let result = await client.getDetectionResult(result_id)
let result_status = result.summary.status

while (result_status != 'READY'){
    await sleep(2000).then(() => {});
    result = await client.getDetectionResult(result_id)
    result_status = result.summary.status
}
```

## <a name="export-model"></a>Exporter le modèle

> [!NOTE]
> L’utilisation de la commande d’exportation est prévue pour permettre l’exécution de modèles multivariés du Détecteur d’anomalies dans un environnement en conteneur. Cette possibilité n’est pas reconnue actuellement pour l’élément multivarié, mais une prise en charge sera ajoutée à l’avenir.

Pour exporter votre modèle entraîné, utilisez la fonction `exportModel`.

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody?.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>Supprimer le modèle

Pour supprimer un modèle existant qui est disponible pour la ressource actuelle, utilisez la fonction `deleteMultivariateModel`.

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## <a name="run-the-application"></a>Exécution de l'application

Avant d’exécuter l’application, vous pouvez vérifier votre code par rapport à l’[exemple de code complet](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/anomalydetector/ai-anomaly-detector/samples/v3/javascript/sample_multivariate_detection.js)

Exécutez l’application avec la commande `node` de votre fichier de démarrage rapide.

```console
node index.js
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources liées au groupe de ressources.

* [Portail](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

* [Présentation de l’API Détecteur d’anomalies](../../overview-multivariate.md)
* [Bonnes pratiques concernant l’utilisation de l’API Détecteur d’anomalies.](../../concepts/best-practices-multivariate.md)