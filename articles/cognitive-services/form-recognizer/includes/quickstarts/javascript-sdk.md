---
title: 'Démarrage rapide : Bibliothèque de client Form Recognizer pour JavaScript'
description: Dans ce guide de démarrage rapide, démarrez avec la bibliothèque de client Form Recognizer pour JavaScript.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/08/2020
ms.author: pafarley
ms.custom: devx-track-javascript
ms.openlocfilehash: 17b0ede8d917ea24bf6fa6fbcda49b1860ef83b9
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246087"
---
[Documentation de référence](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [Package (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [Exemples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* Un blob Stockage Azure qui contient un jeu de données d’entraînement. Consultez [Créer un jeu de données d’entraînement pour un modèle personnalisé](../../build-training-data-set.md) pour obtenir des conseils et des options pour constituer votre jeu de données d’entraînement. Dans le cadre de ce guide de démarrage rapide, vous pouvez utiliser les fichiers disponibles dans le dossier **Train** de l’[exemple de jeu de données](https://go.microsoft.com/fwlink/?linkid=2090451).
* Version actuelle de [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>Configuration

### <a name="create-a-form-recognizer-azure-resource"></a>Créer une ressource Azure Form Recognizer

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Créer des variables d’environnement

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. 

```console
mkdir myapp && cd myapp
```

Exécutez la commande `npm init` pour créer une application de nœud avec un fichier `package.json`. 

```console
npm init
```

Créez un fichier sous le nom `index.js`, ouvrez-le, puis importez les bibliothèques suivantes :

```javascript
const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
const fs = require("fs");
```

De même, chargez le fichier de variables d’environnement.

```javascript
// Load the .env file if it exists
require("dotenv").config();
```

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Installez les packages NPM `ai-form-recognizer` et `dotenv` :

```console
npm install @azure/ai-form-recognizer dotenv
```

Le fichier `package.json` de votre application sera mis à jour avec les dépendances.


<!-- 
    Object model
-->

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client Form Recognizer pour JavaScript :

* [Authentifier le client](#authenticate-the-client)
* [Reconnaître le contenu d’un formulaire](#recognize-form-content)
* [Reconnaître les tickets de caisse](#recognize-receipts)
* [Entraîner un modèle personnalisé](#train-a-custom-model)
* [Analyser les formulaires avec un modèle personnalisé](#analyze-forms-with-a-custom-model)
* [Gérer vos modèles personnalisés](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Authentifier le client

Dans la fonction `main`, créez des variables pour le point de terminaison et la clé Azure de votre ressource. Si vous avez créé la variable d’environnement après avoir lancé l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou l’interpréteur de commandes pour accéder à la variable.

```javascript
// You will need to set these environment variables or edit the following values
const endpoint = process.env["FORM_RECOGNIZER_ENDPOINT"] || "<cognitive services endpoint>";
const apiKey = process.env["FORM_RECOGNIZER_KEY"] || "<api key>";
```

Authentifiez ensuite un objet client à l’aide des variables d’abonnement que vous avez définies. Vous allez utiliser un objet **AzureKeyCredential** pour que, le cas échéant, vous puissiez mettre à jour la clé d’API sans créer de nouveaux objets clients. Vous allez aussi créer un objet client d’entraînement.

```javascript
const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
```

### <a name="call-client-specific-functions"></a>Appeler des fonctions propres au client

Le bloc de code suivant dans `main` utilise les objets clients pour appeler des fonctions pour chacune des principales tâches dans le kit SDK Form Recognizer. Vous définirez ces fonctions plus tard.

Vous devrez aussi ajouter des références aux URL pour vos données d’entraînement et de test.
* Pour récupérer l’URL SAS des données d’entraînement de votre modèle personnalisé, ouvrez l’Explorateur Stockage Microsoft Azure, cliquez avec le bouton droit sur votre conteneur, puis sélectionnez **Obtenir une signature d’accès partagé**. Assurez-vous que les autorisations de **Lecture** et **Écriture** sont cochées, puis cliquez sur **Créer**. Copiez alors la valeur dans la section **URL**. Il doit avoir le format : `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Pour obtenir l’URL d’un formulaire à tester, vous pouvez utiliser les étapes ci-dessus pour obtenir l’URL SAS d’un document individuel dans le stockage d’objets blob. Vous pouvez aussi prendre l’URL d’un document situé ailleurs.
* Employez également la méthode ci-dessus pour obtenir l’URL d’une image de ticket de caisse ou utilisez l’exemple d’URL d’image fourni.

> [!NOTE]
> Les extraits de code présentés dans ce guide utilisent des formulaires distants accessibles par URL. Si vous voulez traiter des documents de formulaire locaux, consultez les méthodes correspondantes dans la [documentation de référence](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).


```javascript
const trainingDataUrl = "<url/path to the labeled training documents>";
const formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
const receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media/contoso-allinone.jpg";


// Call Form Recognizer scenarios:
await GetContent(recognizerClient, formUrl);
await AnalyzeReceipt(recognizerClient, receiptUrl);
modelId = await TrainModel(trainingClient, trainingDataUrl);
await AnalyzePdfForm(recognizerClient, modelId, formUrl);
await ManageModels(trainingClient, trainingDataUrl);
```

## <a name="recognize-form-content"></a>Reconnaître le contenu d’un formulaire

Vous pouvez utiliser Form Recognizer pour reconnaître les tables, les lignes et les mots dans les documents, sans avoir besoin d’entraîner un modèle. Pour reconnaître le contenu d’un fichier à un URI donnée, utilisez la méthode **beginRecognizeContentFromUrl**.

```javascript
async function GetContent( recognizerClient, invoiceUri)
{
    const poller = await client.beginRecognizeContentFromUrl(invoiceUri);
    await poller.pollUntilDone();
    const response = poller.getResult();
```

La valeur retournée est une collection d’objets **FormPage** : un pour chaque page du document envoyé. Le code suivant itère au sein de ces objets et imprime les paires clé/valeur extraites et les données de table.

```javascript
    for (const page of response.pages) {
    console.log(
        `Page ${page.pageNumber}: width ${page.width} and height ${page.height} with unit ${page.unit}`
    );
        for (const table of page.tables) {
            for (const row of table.rows) {
                for (const cell of row.cells) {
                    console.log(`cell [${cell.rowIndex},${cell.columnIndex}] has text ${cell.text}`);
                }
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>Reconnaître les tickets de caisse

Cette section montre comment reconnaître et extraire les champs communs de tickets de caisse émis aux États-Unis à l’aide d’un modèle de ticket préentraîné.

Pour reconnaître les tickets à partir d’un URI, utilisez la méthode **beginRecognizeReceiptsFromUrl**. La valeur retournée est une collection d’objets **RecognizedReceipt** : un pour chaque page du document envoyé. Le code suivant traite un ticket à l’URI donné et imprime les champs principaux et les valeurs dans la console.

```javascript
async function AnalyzeReceipt( client, receiptUri)
{
    const poller = await client.beginRecognizeReceiptsFromUrl(url, {
        includeTextDetails: true,
        onProgress: (state) => {
            console.log(`analyzing status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();


    const receipt = receipts[0];
    console.log("First receipt:");
    // For supported fields recognized by the service, please refer to https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult.
    const receiptTypeField = receipt.recognizedForm.fields["ReceiptType"];
    if (receiptTypeField.valueType === "string") {
        console.log(`  Receipt Type: '${receiptTypeField.value || "<missing>"}', with confidence of ${receiptTypeField.confidence}`);
    }
    const merchantNameField = receipt.recognizedForm.fields["MerchantName"];
    if (merchantNameField.valueType === "string") {
        console.log(`  Merchant Name: '${merchantNameField.value || "<missing>"}', with confidence of ${merchantNameField.confidence}`);
    }
    const transactionDate = receipt.recognizedForm.fields["TransactionDate"];
    if (transactionDate.valueType === "date") {
        console.log(`  Transaction Date: '${transactionDate.value || "<missing>"}', with confidence of ${transactionDate.confidence}`);
    }
```

Le bloc de code suivant itère au sein des éléments individuels détectés sur le ticket et en imprime les détails dans la console.

```javascript
    const itemsField = receipt.recognizedForm.fields["Items"];
    if (itemsField.valueType === "array") {
        for (const itemField of itemsField.value || []) {
            if (itemField.valueType === "object") {
                const itemNameField = itemField.value["Name"];
                if (itemNameField.valueType === "string") {
                    console.log(`    Item Name: '${itemNameField.value || "<missing>"}', with confidence of ${itemNameField.confidence}`);
                }
            }
        }
    }
}
```


## <a name="train-a-custom-model"></a>Entraîner un modèle personnalisé

Cette section montre comment entraîner un modèle avec vos propres données. Un modèle entraîné peut restituer des données structurées qui incluent les relations clé/valeur du document de formulaire d’origine. Une fois que le modèle est entraîné, vous pouvez le tester, le réentraîner et l’utiliser ensuite pour extraire de manière fiable des données d’autres formulaires, en fonction de vos besoins.

> [!NOTE]
> Vous pouvez aussi entraîner des modèles à l’aide d’une interface graphique utilisateur telle que l’[outil d’étiquetage des exemples Form Recognizer](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Entraîner un modèle sans étiquettes

Entraînez les modèles personnalisés à reconnaître tous les champs et valeurs rencontrés dans vos formulaires personnalisés sans étiqueter manuellement les documents d’entraînement.

La fonction suivante entraîne un modèle sur un ensemble donné de documents et imprime l’état du modèle dans la console. 

```javascript
async function TrainModel(trainingClient, trainingDataUrl)
{
    const poller = await trainingClient.beginTraining(trainingDataUrl, false, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
    
    if (!response) {
        throw new Error("Expecting valid response!");
    }
    
    console.log(`Model ID: ${response.modelId}`);
    console.log(`Status: ${response.status}`);
    console.log(`Created on: ${response.requestedOn}`);
    console.log(`Last modified: ${response.completedOn}`);
```

L’objet **CustomFormModel** retourné contient des informations sur les types de formulaire que le modèle peut reconnaître et les champs qu’il peut extraire de chaque type de formulaire. Le bloc de code suivant imprime ces informations dans la console.

```javascript
    if (response.submodels) {
        for (const submodel of response.submodels) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
```

Enfin, cette méthode retourne l’ID unique du modèle.

```csharp
    return response.modelId;
}
```

### <a name="train-a-model-with-labels"></a>Entraîner un modèle avec des étiquettes

Vous pouvez aussi entraîner les modèles personnalisés en étiquetant manuellement les documents d’entraînement. L’entraînement avec étiquettes offre de meilleures performances dans certains scénarios. Pour entraîner avec des étiquettes, vous devez disposer de fichiers d’informations spéciaux sur les étiquettes ( *\<filename\>.pdf.labels.json*) dans votre conteneur de stockage d’objets blob, en même temps que les documents d’entraînement. L’[outil d’étiquetage des exemples Form Recognizer](../../quickstarts/label-tool.md) propose une interface utilisateur qui facilite la création de ces fichiers d’étiquettes. Une fois ceux-ci à disposition, vous pouvez appeler la méthode **beginTraining** avec le paramètre *uselabels* défini sur `true`.

```javascript
async function TrainModelWithLabelsAsync(
    const poller = await trainingClient.beginTraining(trainingDataUrl, true, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const model = poller.getResult();
    
    return model.modelId;
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Analyser les formulaires avec un modèle personnalisé

Cette section montre comment extraire des informations clé/valeur et d’autres contenus de vos types de formulaires personnalisés à l’aide de modèles que vous avez entraînés avec vos propres formulaires.

> [!IMPORTANT]
> Pour implémenter ce scénario, vous devez avoir déjà entraîné un modèle pour pouvoir passer son ID dans la méthode ci-dessous. Consultez la section [Entraîner un modèle](#train-a-model-without-labels).

Vous allez utiliser la méthode **beginRecognizeCustomFormsFromUrl**. La valeur retournée est une collection d’objets **RecognizedForm** : un pour chaque page du document envoyé.

```javascript
// Analyze PDF form document at an accessible URL
async function AnalyzePdfForm(client, modelId, formUrl)
{    
    const poller = await client.beginRecognizeCustomFormsFromUrl(modelId, formUrl, {
        onProgress: (state) => {
            console.log(`status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
```

Le code suivant imprime les résultats de l’analyse dans la console. Il imprime chaque champ reconnu et la valeur correspondante ainsi qu’un score de confiance.

```javascript
    console.log("Fields:");
    for (const fieldName in form.fields) {
        // each field is of type FormField
        const field = form.fields[fieldName];
        console.log(
            `Field ${fieldName} has value '${field.value}' with a confidence score of ${field.confidence}`
        );
    }
}
```

## <a name="manage-your-custom-models"></a>Gérer vos modèles personnalisés

Cette section explique comment gérer les modèles personnalisés stockés dans votre compte. Le code suivant effectue toutes les tâches de gestion de modèles dans une même fonction, à titre d’exemple. Commencez par copier la signature de fonction ci-dessous :

```javascript
async function ManageModels(trainingClient, trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Vérifier le nombre de modèles dans le compte de ressource FormRecognizer

Le bloc de code suivant vérifie le nombre de modèles que vous avez enregistrés dans votre compte Form Recognizer et le compare à la limite du compte.

```csharp
    // First, we see how many custom models we have, and what our limit is
    const accountProperties = await trainingClient.getAccountProperties();
    console.log(
        `Our account has ${accountProperties.customModelCount} custom models, and we can have at most ${accountProperties.customModelLimit} custom models`
    );
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Lister les modèles actuellement stockés dans le compte de ressource

Le bloc de code suivant liste les modèles actifs dans votre compte et en imprime les détails dans la console. Il enregistre également une référence au premier modèle.

```javascript
    // Next, we get a paged async iterator of all of our custom models
    const result = trainingClient.listCustomModels();

    // We could print out information about first ten models
    // and save the first model id for later use
    let i = 0;
    let firstModel;
    for await (const model of result) {
        console.log(`model ${i++}:`);
        console.log(model);
        if (i === 1) {
            firstModel = model;
        }
        if (i > 10) {
            break;
        }
    }
```

### <a name="get-a-specific-model-using-the-models-id"></a>Obtenir un modèle spécifique à l’aide de l’ID du modèle

Le bloc de code suivant utilise l’ID de modèle enregistré dans la section précédente et l’utilise pour récupérer des détails sur le modèle.

```csharp
    // Now we'll get the first custom model in the paged list
    const model = await trainingClient.getModel(firstModel.modelId);
    console.log(`Model Id: ${model.modelId}`);
    console.log(`Status: ${model.status}`);
    console.log("Documents used in training: [");
    for (const doc of model.trainingDocuments || []) {
        console.log(`  ${doc.documentName}`);
    }
    console.log("]");
```

### <a name="delete-a-model-from-the-resource-account"></a>Supprimer un modèle du compte de ressource

Vous pouvez aussi supprimer un modèle de votre compte en référençant son ID. Ce code supprime le modèle utilisé dans la section précédente.

```javascript
    await client.deleteModel(firstModel.modelId);
    try {
        const deleted = await trainingClient.deleteModel(firstModel.modelId);
        console.log(deleted);
    } catch (err) {
        // Expected
        console.log(`Model with id ${firstModel.modelId} has been deleted`);
    }
}
```


## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `node` de votre fichier de démarrage rapide.

```console
node index.js
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Dépannage

### <a name="enable-logs"></a>Activer les journaux d’activité
Vous pouvez définir la variable d’environnement suivante pour afficher les journaux de débogage quand vous utilisez cette bibliothèque.

```
export DEBUG=azure*
```

Pour obtenir des instructions plus détaillées sur l’activation des journaux, consultez les [documents sur le package @azure/logger](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).


## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez utilisé la bibliothèque de client Python Form Recognizer pour entraîner des modèles et analyser des formulaires de différentes manières. Découvrez ensuite les astuces pour créer un jeu de données d’apprentissage plus performant et produire des modèles plus précis.

> [!div class="nextstepaction"]
> [Créer un jeu de données d’apprentissage](../../build-training-data-set.md)

* [Qu’est-ce que Form Recognizer ?](../../overview.md)
* Vous trouverez l’exemple de code de ce guide (et bien plus encore) sur [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples).