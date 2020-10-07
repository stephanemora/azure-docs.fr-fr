---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.custom: devx-track-js
ms.openlocfilehash: 90927109a78d387ed3a535128e98ae7910c222dc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321053"
---
Ce guide fournit des instructions et un exemple de code pour vous aider à commencer à utiliser la bibliothèque de client Custom Vision pour Node.js afin de générer un modèle de classification d’images. Vous allez créer un projet, ajouter des étiquettes, entraîner le projet et utiliser l’URL de point de terminaison de prédiction du projet pour le tester programmatiquement. Utilisez cet exemple comme modèle pour générer votre propre application de reconnaissance d’image.

> [!NOTE]
> Si vous souhaitez créer et entraîner un modèle de classification _sans_ écrire de code, consultez à la place les [instructions basées sur le navigateur](../../getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Prérequis

- [Node.js 8](https://www.nodejs.org/en/download/) ou ultérieur installé.
- [npm](https://www.npmjs.com/) installé.
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>Installer la bibliothèque de client Custom Vision

Pour écrire une application d’analyse d’image avec Custom Vision pour Node.js, vous avez besoin des packages NPM Custom Vision. Pour les installer, exécutez la commande suivante dans PowerShell :

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

[!INCLUDE [get-keys](../../includes/get-keys.md)]

[!INCLUDE [node-get-images](../../includes/node-get-images.md)]

## <a name="add-the-code"></a>Ajouter le code

Créez un fichier nommé *sample.js* dans le répertoire de projet de votre choix.

## <a name="create-the-custom-vision-project"></a>Créer le projet Custom Vision

Pour créer un nouveau projet Service Vision personnalisée, ajoutez le code suivant à votre script. Insérez vos clés d’abonnement dans les définitions appropriées et définissez la valeur de chemin sampleDataRoot sur le chemin de votre dossier d’image. Assurez-vous que la valeur endPoint correspond aux points de terminaison d’entraînement et de prédiction que vous avez créés sur [Customvision.ai](https://www.customvision.ai/). Notez que la différence entre la création d’un projet de détection d’objet et un projet de classification d’images dépend du domaine spécifié dans l’appel de **createProject**.

```javascript
const util = require('util');
const fs = require('fs');
const TrainingApi = require("@azure/cognitiveservices-customvision-training");
const PredictionApi = require("@azure/cognitiveservices-customvision-prediction");
const msRest = require("@azure/ms-rest-js");

const setTimeoutPromise = util.promisify(setTimeout);

const trainingKey = "<your training key>";
const predictionKey = "<your prediction key>";
const predictionResourceId = "<your prediction resource id>";
const sampleDataRoot = "<path to image files>";

const endPoint = "https://<my-resource-name>.cognitiveservices.azure.com/"

const publishIterationName = "classifyModel";

const credentials = new msRest.ApiKeyCredentials({ inHeader: { "Training-key": trainingKey } });
const trainer = new TrainingApi.TrainingAPIClient(credentials, endPoint);

(async () => {
    console.log("Creating project...");
    const sampleProject = await trainer.createProject("Sample Project");
```

## <a name="create-tags-in-the-project"></a>Créer des balises dans un projet

Pour créer des balises de classification dans votre projet, ajoutez le code suivant à la fin du fichier *sample.js* :

```javascript
    const hemlockTag = await trainer.createTag(sampleProject.id, "Hemlock");
    const cherryTag = await trainer.createTag(sampleProject.id, "Japanese Cherry");
```

## <a name="upload-and-tag-images"></a>Charger et étiqueter des images

Pour ajouter les exemples d’images au projet, insérez le code suivant après la création de mots clés. Ce code charge chaque image avec la balise correspondante. Vous pouvez charger jusqu’à 64 images dans un même lot.

> [!NOTE]
> Vous devez remplacer *sampleDataRoot* par le chemin des images, selon l’emplacement auquel vous avez préalablement téléchargé l’exemple de projet du SDK Cognitive Services pour Node.js.

```javascript
    console.log("Adding images...");
    let fileUploadPromises = [];
    
    const hemlockDir = `${sampleDataRoot}/Hemlock`;
    const hemlockFiles = fs.readdirSync(hemlockDir);
    hemlockFiles.forEach(file => {
        fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${hemlockDir}/${file}`), { tagIds: [hemlockTag.id] }));
    });
    
    const cherryDir = `${sampleDataRoot}/Japanese Cherry`;
    const japaneseCherryFiles = fs.readdirSync(cherryDir);
    japaneseCherryFiles.forEach(file => {
        fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${cherryDir}/${file}`), { tagIds: [cherryTag.id] }));
    });
    
    await Promise.all(fileUploadPromises);
```

## <a name="train-and-publish-the-classifier"></a>Entraîner et publier le classifieur

Ce code crée la première itération du modèle de prédiction, puis publie cette itération sur le point de terminaison de prédiction. Le nom donné à l’itération publiée peut être utilisé pour envoyer des requêtes de prédiction. Les itérations ne sont pas disponibles sur le point de terminaison de prédiction tant qu’elles n’ont pas été publiées.

```javascript
    console.log("Training...");
    let trainingIteration = await trainer.trainProject(sampleProject.id);
    
    // Wait for training to complete
    console.log("Training started...");
    while (trainingIteration.status == "Training") {
        console.log("Training status: " + trainingIteration.status);
        await setTimeoutPromise(1000, null);
        trainingIteration = await trainer.getIteration(sampleProject.id, trainingIteration.id)
    }
    console.log("Training status: " + trainingIteration.status);
    
    // Publish the iteration to the end point
    await trainer.publishIteration(sampleProject.id, trainingIteration.id, publishIterationName, predictionResourceId);
```

## <a name="use-the-prediction-endpoint"></a>Utiliser le point de terminaison de prédiction

Pour envoyer une image au point de terminaison de prédiction et récupérer la prédiction, ajoutez le code suivant à la fin du fichier :

```javascript
    const predictor_credentials = new msRest.ApiKeyCredentials({ inHeader: { "Prediction-key": predictionKey } });
    const predictor = new PredictionApi.PredictionAPIClient(predictor_credentials, endPoint);
    const testFile = fs.readFileSync(`${sampleDataRoot}/Test/test_image.jpg`);

    const results = await predictor.classifyImage(sampleProject.id, publishIterationName, testFile);

    // Step 6. Show results
    console.log("Results:");
    results.predictions.forEach(predictedResult => {
        console.log(`\t ${predictedResult.tagName}: ${(predictedResult.probability * 100.0).toFixed(2)}%`);
    });
})()
```

## <a name="run-the-application"></a>Exécution de l'application

Exécutez *sample.js*.

```shell
node sample.js
```

Le résultat de l’application ressemble au texte suivant :

```console
Creating project...
Adding images...
Training...
Training started...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Results:
         Hemlock: 94.97%
         Japanese Cherry: 0.01%
```

Vous pouvez ensuite vérifier que l’image test (trouvée dans **<base_image_url>/Images/Test**) est balisée de façon appropriée. Vous pouvez aussi revenir sur le [site web Custom Vision](https://customvision.ai) et consulter l’état actuel de votre nouveau projet.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous savez maintenant comment effectuer la détection d’objet dans le code. Cet exemple exécute une seule itération d’entraînement, mais vous aurez souvent besoin d’entraîner et de tester votre modèle à plusieurs reprises pour le rendre plus précis.

> [!div class="nextstepaction"]
> [Tester et réentraîner un modèle](../../test-your-model.md)

* [Qu’est-ce que Custom Vision ?](../../overview.md)
* [Documentation de référence du SDK (entraînement)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-customvision-training/?view=azure-node-latest)
* [Documentation de référence du SDK (prédiction)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-customvision-prediction/?view=azure-node-latest)