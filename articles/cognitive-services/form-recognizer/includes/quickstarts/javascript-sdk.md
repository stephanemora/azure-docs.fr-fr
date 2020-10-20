---
title: 'Démarrage rapide : Bibliothèque de client Form Recognizer pour JavaScript'
description: Utilisez la bibliothèque de client Form Recognizer pour JavaScript afin de créer une application de traitement des formulaires qui extrait des paires clé/valeur et des données de table de vos documents personnalisés.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 09/21/2020
ms.author: pafarley
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 5e5d7c48508cc13d2ad36906df7d31c8926c75f1
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963045"
---
> [!IMPORTANT]
> * Le kit SDK Form Recognizer cible actuellement la version 2.0 du service Form Recognizer.
> * Le code indiqué dans cet article utilise des méthodes synchrones et un stockage d’informations d’identification non sécurisé pour des raisons de simplicité. Consultez la documentation de référence ci-dessous. 

[Documentation de référence](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [Package (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [Exemples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* Un blob Stockage Azure qui contient un jeu de données d’apprentissage. Consultez [Créer un jeu de données d’entraînement pour un modèle personnalisé](../../build-training-data-set.md) pour obtenir des conseils et des options pour constituer votre jeu de données d’entraînement. Dans le cadre de ce guide de démarrage rapide, vous pouvez utiliser les fichiers disponibles dans le dossier **Train** de l’[exemple de jeu de données](https://go.microsoft.com/fwlink/?linkid=2090451) (téléchargez et extrayez *sample_data.zip*).
* Version actuelle de [Node.js](https://nodejs.org/)
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="créez une ressource Form Recognizer"  target="_blank">créer une ressource Form Recognizer<span class="docon docon-navigate-external x-hidden-focus"></span></a> sur le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Form Recognizer. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

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

Créez un fichier sous le nom `index.js`, ouvrez-le, puis importez les bibliothèques suivantes :

```javascript
const { FormRecognizerClient, FormTrainingClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
const fs = require("fs");
```

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Installez le package NPM `ai-form-recognizer` :

```console
npm install @azure/ai-form-recognizer
```

Le fichier `package.json` de votre application sera mis à jour avec les dépendances.

## <a name="object-model"></a>Modèle objet 

Avec Form Recognizer, vous pouvez créer deux types de client différents. Le premier, `FormRecognizerClient`, sert à interroger le service pour reconnaître les champs et le contenu de formulaires. Le deuxième, `FormTrainingClient`, sert à créer et gérer des modèles personnalisés dont vous pouvez vous servir pour améliorer la reconnaissance. 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`FormRecognizerClient` propose des opérations pour :

 * Reconnaître les champs et le contenu de formulaires à l’aide de modèles personnalisés entraînés pour reconnaître vos formulaires personnalisés. Ces valeurs sont retournées dans une collection d’objets `RecognizedForm`.
 * Reconnaître le contenu des formulaires, notamment les tableaux, les lignes et les mots, sans avoir à entraîner un modèle. Le contenu des formulaires est retourné dans une collection d’objets `FormPage`.
 * Reconnaître les champs communs des tickets de caisse, à l’aide d’un modèle de ticket de caisse préentraîné dans le service Form Recognizer. Ces champs et métadonnées sont retournés dans une collection de `RecognizedReceipt`.

### <a name="formtrainingclient"></a>FormTrainingClient
`FormTrainingClient` propose des opérations pour :

* Entraîner les modèles personnalisés à reconnaître tous les champs et les valeurs rencontrés dans vos formulaires personnalisés. Un `CustomFormModel` est retourné indiquant les types de formulaire que le modèle va reconnaître et les champs qu’il va extraire pour chaque type de formulaire. Pour obtenir des informations plus détaillées sur la création d’un jeu de données d’entraînement, consultez la [documentation du service sur l’entraînement d’un modèle sans étiquettes](#train-a-model-without-labels).
* Entraîner les modèles personnalisés à reconnaître des champs et des valeurs spécifiques que vous spécifiez en étiquetant vos formulaires personnalisés. Un `CustomFormModel` est retourné indiquant les champs que le modèle va extraire ainsi que la précision estimée pour chaque champ. Pour obtenir des informations plus détaillées sur l’application d’étiquettes à un jeu de données d’entraînement, consultez la [documentation du service sur l’entraînement d’un modèle avec étiquettes](#train-a-model-with-labels).
* Gérer les modèles créés dans votre compte.
* Copier un modèle personnalisé d’une ressource Form Recognizer vers une autre.

> [!NOTE]
> Les modèles peuvent aussi être entraînés à partir d’une interface graphique utilisateur comme l’[outil d’étiquetage Form Recognizer](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool).


## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client Form Recognizer pour JavaScript :

* [Authentifier le client](#authenticate-the-client)
* [Reconnaître le contenu d’un formulaire](#recognize-form-content)
* [Reconnaître les tickets de caisse](#recognize-receipts)
* [Entraîner un modèle personnalisé](#train-a-custom-model)
* [Analyser les formulaires avec un modèle personnalisé](#analyze-forms-with-a-custom-model)
* [Gérer vos modèles personnalisés](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Authentifier le client

Dans votre application, créez des variables pour le point de terminaison et la clé Azure de votre ressource. 

```javascript
// You will need to set these environment variables or edit the following values
const endpoint = "<paste-your-form-recognizer-endpoint-here>";
const apiKey = "<paste-your-form-recognizer-key-here>";
```

Authentifiez ensuite un objet client à l’aide des variables d’abonnement que vous avez définies. Vous allez utiliser un objet `AzureKeyCredential` pour que, le cas échéant, vous puissiez mettre à jour la clé d’API sans créer de nouveaux objets clients. Vous allez aussi créer un objet client d’entraînement.

```javascript
const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));
const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
```

## <a name="get-assets-for-testing"></a>Obtenir des ressources à des fins de test

Les extraits de code présentés dans ce guide utilisent des formulaires distants accessibles par URL. Si vous voulez traiter des documents de formulaire locaux, consultez les méthodes correspondantes dans la [documentation de référence](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) et les [exemples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

Vous devrez aussi ajouter des références aux URL pour vos données d’entraînement et de test.
* Pour récupérer l’URL SAS des données d’entraînement de votre modèle personnalisé, ouvrez l’Explorateur Stockage Microsoft Azure, cliquez avec le bouton droit sur votre conteneur, puis sélectionnez **Obtenir une signature d’accès partagé**. Assurez-vous que les autorisations de **Lecture** et **Écriture** sont cochées, puis cliquez sur **Créer**. Copiez alors la valeur dans la section **URL**. Il doit avoir le format : `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Utilisez les exemples de formulaires et d’images de tickets de caisse fournis avec les exemples ci-dessous (également disponibles sur [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms)) ou utilisez les étapes ci-dessus pour récupérer l’URL SAS d’un document individuel dans le stockage d’objets blob. 

> [!NOTE]
> Les extraits de code présentés dans ce guide utilisent des formulaires distants accessibles par URL. Si vous voulez traiter des documents de formulaire locaux, consultez les méthodes correspondantes dans la [documentation de référence](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

## <a name="recognize-form-content"></a>Reconnaître le contenu d’un formulaire

Vous pouvez utiliser Form Recognizer pour reconnaître les tables, les lignes et les mots dans les documents, sans avoir besoin d’entraîner un modèle. Pour reconnaître le contenu d’un fichier à un URI donné, utilisez la méthode `beginRecognizeContentFromUrl`.

```javascript
async function recognizeContent() {
    const formUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf";
    const poller = await client.beginRecognizeContentFromUrl(formUrl);
    const pages = await poller.pollUntilDone();

    if (!pages || pages.length === 0) {
        throw new Error("Expecting non-empty list of pages!");
    }

    for (const page of pages) {
        console.log(
            `Page ${page.pageNumber}: width ${page.width} and height ${page.height} with unit ${page.unit}`
        );
        for (const table of page.tables) {
            for (const cell of table.cells) {
                console.log(`cell [${cell.rowIndex},${cell.columnIndex}] has text ${cell.text}`);
            }
        }
    }
}

recognizeContent().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Output

```console
Page 1: width 8.5 and height 11 with unit inch
cell [0,0] has text Invoice Number
cell [0,1] has text Invoice Date
cell [0,2] has text Invoice Due Date
cell [0,3] has text Charges
cell [0,5] has text VAT ID
cell [1,0] has text 34278587
cell [1,1] has text 6/18/2017
cell [1,2] has text 6/24/2017
cell [1,3] has text $56,651.49
cell [1,5] has text PT
```

## <a name="recognize-receipts"></a>Reconnaître les tickets de caisse

Cette section montre comment reconnaître et extraire les champs communs de tickets de caisse émis aux États-Unis à l’aide d’un modèle de ticket préentraîné.

Pour reconnaître les tickets à partir d’un URI, utilisez la méthode `beginRecognizeReceiptsFromUrl`. Le code suivant traite un ticket à l’URI donné et imprime les champs principaux et les valeurs dans la console.

```javascript
async function recognizeReceipt() {
    receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png";
    const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
    const poller = await client.beginRecognizeReceiptsFromUrl(receiptUrl, {
        onProgress: (state) => { console.log(`status: ${state.status}`); }
    });

    const receipts = await poller.pollUntilDone();

    if (!receipts || receipts.length <= 0) {
        throw new Error("Expecting at lease one receipt in analysis result");
    }

    const receipt = receipts[0];
    console.log("First receipt:");
    const receiptTypeField = receipt.fields["ReceiptType"];
    if (receiptTypeField.valueType === "string") {
        console.log(`  Receipt Type: '${receiptTypeField.value || "<missing>"}', with confidence of ${receiptTypeField.confidence}`);
    }
    const merchantNameField = receipt.fields["MerchantName"];
    if (merchantNameField.valueType === "string") {
        console.log(`  Merchant Name: '${merchantNameField.value || "<missing>"}', with confidence of ${merchantNameField.confidence}`);
    }
    const transactionDate = receipt.fields["TransactionDate"];
    if (transactionDate.valueType === "date") {
        console.log(`  Transaction Date: '${transactionDate.value || "<missing>"}', with confidence of ${transactionDate.confidence}`);
    }
    const itemsField = receipt.fields["Items"];
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
    const totalField = receipt.fields["Total"];
    if (totalField.valueType === "number") {
        console.log(`  Total: '${totalField.value || "<missing>"}', with confidence of ${totalField.confidence}`);
    }
}

recognizeReceipt().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Output

```console
status: notStarted
status: running
status: succeeded
First receipt:
  Receipt Type: 'Itemized', with confidence of 0.659
  Merchant Name: 'Contoso Contoso', with confidence of 0.516
  Transaction Date: 'Sun Jun 09 2019 17:00:00 GMT-0700 (Pacific Daylight Time)', with confidence of 0.985
    Item Name: '8GB RAM (Black)', with confidence of 0.916
    Item Name: 'SurfacePen', with confidence of 0.858
  Total: '1203.39', with confidence of 0.774
```

## <a name="train-a-custom-model"></a>Entraîner un modèle personnalisé

Cette section montre comment entraîner un modèle avec vos propres données. Un modèle entraîné peut restituer des données structurées qui incluent les relations clé/valeur du document de formulaire d’origine. Une fois que le modèle est entraîné, vous pouvez le tester, le réentraîner et l’utiliser ensuite pour extraire de manière fiable des données d’autres formulaires, en fonction de vos besoins.

> [!NOTE]
> Vous pouvez aussi entraîner des modèles à l’aide d’une interface graphique utilisateur telle que l’[outil d’étiquetage des exemples Form Recognizer](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Entraîner un modèle sans étiquettes

Entraînez les modèles personnalisés à reconnaître tous les champs et valeurs rencontrés dans vos formulaires personnalisés sans étiqueter manuellement les documents d’entraînement.

La fonction suivante entraîne un modèle sur un ensemble donné de documents et imprime l’état du modèle dans la console. 

```javascript
async function trainModel() {

    const containerSasUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await trainingClient.beginTraining(containerSasUrl, false, {
        onProgress: (state) => { console.log(`training status: ${state.status}`); }
    });
    const model = await poller.pollUntilDone();

    if (!model) {
        throw new Error("Expecting valid training result!");
    }

    console.log(`Model ID: ${model.modelId}`);
    console.log(`Status: ${model.status}`);
    console.log(`Training started on: ${model.trainingStartedOn}`);
    console.log(`Training completed on: ${model.trainingCompletedOn}`);

    if (model.submodels) {
        for (const submodel of model.submodels) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
    // Training document information
    if (model.trainingDocuments) {
        for (const doc of model.trainingDocuments) {
            console.log(`Document name: ${doc.name}`);
            console.log(`Document status: ${doc.status}`);
            console.log(`Document page count: ${doc.pageCount}`);
            console.log(`Document errors: ${doc.errors}`);
        }
    }
}

trainModel().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Output

Ceci est la sortie d’un modèle entraîné avec les données d’entraînement disponibles dans le [SDK Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training). Cet exemple de sortie a été tronqué dans un souci de lisibilité.

```console
training status: creating
training status: ready
Model ID: 9d893595-1690-4cf2-a4b1-fbac0fb11909
Status: ready
Training started on: Thu Aug 20 2020 20:27:26 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:27:37 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'field-0'
The model found field 'field-1'
The model found field 'field-2'
The model found field 'field-3'
The model found field 'field-4'
The model found field 'field-5'
The model found field 'field-6'
The model found field 'field-7'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: 
...
```

### <a name="train-a-model-with-labels"></a>Entraîner un modèle avec des étiquettes

Vous pouvez aussi entraîner les modèles personnalisés en étiquetant manuellement les documents d’entraînement. L’entraînement avec étiquettes offre de meilleures performances dans certains scénarios. Pour effectuer un entraînement avec des étiquettes, vous devez disposer de fichiers d’informations spéciaux sur les étiquettes (`\<filename\>.pdf.labels.json`) dans votre conteneur de stockage d’objets blob, en même temps que les documents d’entraînement. L’[outil d’étiquetage des exemples Form Recognizer](../../quickstarts/label-tool.md) propose une interface utilisateur qui facilite la création de ces fichiers d’étiquettes. Une fois ceux-ci à disposition, vous pouvez appeler la méthode `beginTraining` avec le paramètre `uselabels` défini sur `true`.

```javascript
async function trainModelLabels() {

    const containerSasUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await trainingClient.beginTraining(containerSasUrl, true, {
        onProgress: (state) => { console.log(`training status: ${state.status}`); }
    });
    const model = await poller.pollUntilDone();

    if (!model) {
        throw new Error("Expecting valid training result!");
    }

    console.log(`Model ID: ${model.modelId}`);
    console.log(`Status: ${model.status}`);
    console.log(`Training started on: ${model.trainingStartedOn}`);
    console.log(`Training completed on: ${model.trainingCompletedOn}`);

    if (model.submodels) {
        for (const submodel of model.submodels) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
    // Training document information
    if (model.trainingDocuments) {
        for (const doc of model.trainingDocuments) {
            console.log(`Document name: ${doc.name}`);
            console.log(`Document status: ${doc.status}`);
            console.log(`Document page count: ${doc.pageCount}`);
            console.log(`Document errors: ${doc.errors}`);
        }
    }
}

trainModelLabels().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Output 

Ceci est la sortie d’un modèle entraîné avec les données d’entraînement disponibles dans le [SDK Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training). Cet exemple de sortie a été tronqué dans un souci de lisibilité.

```console
training status: creating
training status: ready
Model ID: 789b1b37-4cc3-4e36-8665-9dde68618072
Status: ready
Training started on: Thu Aug 20 2020 20:30:37 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:30:43 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'CompanyAddress'
The model found field 'CompanyName'
The model found field 'CompanyPhoneNumber'
The model found field 'DatedAs'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
...
```

## <a name="analyze-forms-with-a-custom-model"></a>Analyser les formulaires avec un modèle personnalisé

Cette section montre comment extraire des informations clé/valeur et d’autres contenus de vos types de formulaires personnalisés à l’aide de modèles que vous avez entraînés avec vos propres formulaires.

> [!IMPORTANT]
> Pour implémenter ce scénario, vous devez avoir déjà entraîné un modèle pour pouvoir passer son ID dans la méthode ci-dessous. Consultez la section [Entraîner un modèle](#train-a-model-without-labels).

Vous allez utiliser la méthode `beginRecognizeCustomFormsFromUrl`. La valeur retournée est une collection d’objets `RecognizedForm` : un pour chaque page du document envoyé.

```javascript
async function recognizeCustom() {
    // Model ID from when you trained your model.
    const modelId = "<modelId>";
    const formUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf";

    const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
    const poller = await client.beginRecognizeCustomForms(modelId, formUrl, {
        onProgress: (state) => { console.log(`status: ${state.status}`); }
    });
    const forms = await poller.pollUntilDone();

    console.log("Forms:");
    for (const form of forms || []) {
        console.log(`${form.formType}, page range: ${form.pageRange}`);
        console.log("Pages:");
        for (const page of form.pages || []) {
            console.log(`Page number: ${page.pageNumber}`);
            console.log("Tables");
            for (const table of page.tables || []) {
                for (const cell of table.cells) {
                    console.log(`cell (${cell.rowIndex},${cell.columnIndex}) ${cell.text}`);
                }
            }
        }

        console.log("Fields:");
        for (const fieldName in form.fields) {
            // each field is of type FormField
            const field = form.fields[fieldName];
            console.log(
                `Field ${fieldName} has value '${field.value}' with a confidence score of ${field.confidence}`
            );
        }
    }
}

recognizeCustom().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Output

```console
status: notStarted
status: succeeded
Forms:
custom:form, page range: [object Object]
Pages:
Page number: 1
Tables
cell (0,0) Invoice Number
cell (0,1) Invoice Date
cell (0,2) Invoice Due Date
cell (0,3) Charges
cell (0,5) VAT ID
cell (1,0) 34278587
cell (1,1) 6/18/2017
cell (1,2) 6/24/2017
cell (1,3) $56,651.49
cell (1,5) PT
Fields:
Field Merchant has value 'Invoice For:' with a confidence score of 0.116
Field CompanyPhoneNumber has value '$56,651.49' with a confidence score of 0.249
Field VendorName has value 'Charges' with a confidence score of 0.145
Field CompanyAddress has value '1 Redmond way Suite 6000 Redmond, WA' with a confidence score of 0.258
Field CompanyName has value 'PT' with a confidence score of 0.245
Field Website has value '99243' with a confidence score of 0.114
Field DatedAs has value 'undefined' with a confidence score of undefined
Field Email has value 'undefined' with a confidence score of undefined
Field PhoneNumber has value 'undefined' with a confidence score of undefined
Field PurchaseOrderNumber has value 'undefined' with a confidence score of undefined
Field Quantity has value 'undefined' with a confidence score of undefined
Field Signature has value 'undefined' with a confidence score of undefined
Field Subtotal has value 'undefined' with a confidence score of undefined
Field Tax has value 'undefined' with a confidence score of undefined
Field Total has value 'undefined' with a confidence score of undefined
```

## <a name="manage-your-custom-models"></a>Gérer vos modèles personnalisés

Cette section explique comment gérer les modèles personnalisés stockés dans votre compte. Le code suivant effectue toutes les tâches de gestion de modèles dans une même fonction, à titre d’exemple. 

### <a name="get-list-of-models-in-account"></a>Obtenir la liste des modèles du compte

Le bloc de code suivant fournit la liste complète des modèles disponibles dans votre compte, notamment des informations sur la date de création du modèle et son état actuel.

```javascript
async function listModels() {
    const client = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

    // returns an async iteratable iterator that supports paging
    const result = client.listCustomModels();
    let i = 0;
    for await (const modelInfo of result) {
        console.log(`model ${i++}:`);
        console.log(modelInfo);
    }
}

listModels().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Output

```console
model 0:
{
  modelId: '453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e',
  status: 'invalid',
  trainingStartedOn: 2020-08-20T22:28:52.000Z,
  trainingCompletedOn: 2020-08-20T22:28:53.000Z
}
model 1:
{
  modelId: '628739de-779c-473d-8214-d35c72d3d4f7',
  status: 'ready',
  trainingStartedOn: 2020-08-20T23:16:51.000Z,
  trainingCompletedOn: 2020-08-20T23:16:59.000Z
}
model 2:
{
  modelId: '789b1b37-4cc3-4e36-8665-9dde68618072',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:30:37.000Z,
  trainingCompletedOn: 2020-08-21T03:30:43.000Z
}
model 3:
{
  modelId: '9d893595-1690-4cf2-a4b1-fbac0fb11909',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:27:26.000Z,
  trainingCompletedOn: 2020-08-21T03:27:37.000Z
}
```

### <a name="get-list-of-model-ids"></a>Obtenir la liste des ID de modèle

Ce bloc de code fournit la liste des modèles et des ID de modèle.

```javascript
async function listModelIds(){
    const client = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));
    // using `iter.next()`
    i = 1;
    let iter = client.listCustomModels();
    let modelItem = await iter.next();
    while (!modelItem.done) {
        console.log(`model ${i++}: ${modelItem.value.modelId}`);
        modelItem = await iter.next();
    }
}
```

### <a name="output"></a>Output

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-list-of-model-ids-by-page"></a>Obtenir la liste des ID de modèle par page

Ce bloc de code fournit une liste paginée des modèles et des ID de modèle.

```javascript
async function listModelsByPage(){
    const client = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));
    // using `byPage()`
    i = 1;
    for await (const response of client.listCustomModels().byPage()) {
        for (const modelInfo of response.modelList) {
            console.log(`model ${i++}: ${modelInfo.modelId}`);
        }
    }
}

listModelsByPage().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Output

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
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

### <a name="output"></a>Output

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>Exécution de l'application

Vous pouvez à tout moment exécuter l’application avec les fonctions de votre choix, qui vous ont été présentées dans ce démarrage rapide, avec cette commande :

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

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que Form Recognizer ?](../../overview.md)
