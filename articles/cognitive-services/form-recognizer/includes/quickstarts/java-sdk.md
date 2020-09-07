---
title: 'Démarrage rapide : Bibliothèque de client Form Recognizer pour Java'
description: Dans ce guide de démarrage rapide, démarrez avec la bibliothèque de client Form Recognizer pour Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 30c486ccb7bf3b7d537cd1ed3475a8dadc5b4f6d
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89324673"
---
> [!IMPORTANT]
> * Le kit SDK Form Recognizer cible actuellement la version 2.0 du service Form Recognizer.
> * Le code indiqué dans cet article utilise des méthodes synchrones et un stockage d’informations d’identification non sécurisé pour des raisons de simplicité. Consultez la documentation de référence ci-dessous. 

[Documentation de référence](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-java-preview) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [Package (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [Exemples](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* Un blob Stockage Azure qui contient un jeu de données d’apprentissage. Consultez [Créer un jeu de données d’entraînement pour un modèle personnalisé](../../build-training-data-set.md) pour obtenir des conseils et des options pour constituer votre jeu de données d’entraînement. Dans le cadre de ce guide de démarrage rapide, vous pouvez utiliser les fichiers disponibles dans le dossier **Train** de l’[exemple de jeu de données](https://go.microsoft.com/fwlink/?linkid=2090451).
* La version actuelle du [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* L’[outil de génération Gradle](https://gradle.org/install/) ou un autre gestionnaire de dépendances.
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="créez une ressource Form Recognizer"  target="_blank">créer une ressource Form Recognizer<span class="docon docon-navigate-external x-hidden-focus"></span></a> sur le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Form Recognizer. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-gradle-project"></a>Créer un projet Gradle

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. 

```console
mkdir myapp && cd myapp
```

Exécutez la commande `gradle init` à partir de votre répertoire de travail. Cette commande crée des fichiers de build essentiels pour Gradle, notamment *build.gradle.kts*, qui est utilisé au moment de l’exécution pour créer et configurer votre application.

```console
gradle init --type basic
```

Quand vous êtes invité à choisir un **DSL**, sélectionnez **Kotlin**.

Entrez la commande suivante à partir de votre répertoire de travail :

```console
mkdir -p src/main/java
```

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Ce guide de démarrage rapide utilise le gestionnaire de dépendances Gradle. Vous trouverez la bibliothèque de client et des informations concernant d’autres gestionnaires de dépendances sur le [référentiel central Maven](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

Dans le fichier *build.gradle.kts* de votre projet, incluez la bibliothèque de client sous la forme d’une instruction `implementation`, avec les plug-ins et les paramètres obligatoires.

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.0.0")
}
```

Accédez au nouveau dossier **src/main/java**, puis créez un fichier appelé *Management.java*. Ouvrez-le dans votre éditeur ou IDE habituel et ajoutez les instructions `import` suivantes :

```java
import com.azure.ai.formrecognizer.*;
import com.azure.ai.formrecognizer.training.*;
import com.azure.ai.formrecognizer.models.*;
import com.azure.ai.formrecognizer.training.models.*;

import java.util.concurrent.atomic.AtomicReference;
import java.util.List;
import java.util.Map;
import java.time.LocalDate;

import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.util.Context;
import com.azure.core.util.polling.SyncPoller;
```

Ajoutez une classe et une méthode `main`, puis créez des variables pour le point de terminaison et la clé Azure de votre ressource. Si vous avez créé la variable d’environnement après avoir lancé l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou l’interpréteur de commandes pour accéder à la variable. Vous définirez les méthodes plus tard.


```java
public class FormRecognizer {
    public static void main(String[] args)
    {
        String key = "<replace-with-your-form-recognizer-key>";
        String endpoint = "<replace-with-your-form-recognizer-endpoint>";
    }
}
```

## <a name="object-model"></a>Modèle objet 

Avec Form Recognizer, vous pouvez créer deux types de client différents. Le premier, `FormRecognizerClient`, sert à interroger le service pour reconnaître les champs et le contenu de formulaires. Le deuxième, `FormTrainingClient`, sert à créer et gérer des modèles personnalisés dont vous pouvez vous servir pour améliorer la reconnaissance. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` propose des opérations pour :

- Reconnaître les champs et le contenu de formulaires à l’aide de modèles personnalisés entraînés pour reconnaître vos formulaires personnalisés.  Ces valeurs sont retournées dans une collection d’objets `RecognizedForm`. Consultez l’exemple [Analyser les formulaires personnalisés](#analyze-forms-with-a-custom-model).
- Reconnaître le contenu des formulaires, notamment les tableaux, les lignes et les mots, sans avoir à entraîner un modèle.  Le contenu des formulaires est retourné dans une collection d’objets `FormPage`. Consultez l’exemple [Reconnaître le contenu des formulaires](#recognize-form-content).
- Reconnaître les champs communs des tickets de caisse émis aux États-Unis, à l’aide d’un modèle de ticket de caisse préentraîné dans le service Form Recognizer.  Ces champs et métadonnées sont retournés dans une collection d’objets `RecognizedForm`. Consultez l’exemple [Reconnaître les tickets de caisse](#recognize-receipts).

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` propose des opérations pour :

- Entraîner les modèles personnalisés à reconnaître tous les champs et les valeurs rencontrés dans vos formulaires personnalisés.  Un `CustomFormModel` est retourné indiquant les types de formulaire que le modèle va reconnaître et les champs qu’il va extraire pour chaque type de formulaire.
- Entraîner les modèles personnalisés à reconnaître des champs et des valeurs spécifiques que vous spécifiez en étiquetant vos formulaires personnalisés.  Un `CustomFormModel` est retourné indiquant les champs que le modèle va extraire ainsi que la précision estimée pour chaque champ.
- Gérer les modèles créés dans votre compte.
- Copier un modèle personnalisé d’une ressource Form Recognizer vers une autre.

Notez que les modèles peuvent aussi être entraînés à partir d’une interface graphique utilisateur comme l’[outil d’étiquetage Form Recognizer](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool).

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client Form Recognizer pour Java :

* [Authentifier le client](#authenticate-the-client)
* [Reconnaître le contenu d’un formulaire](#recognize-form-content)
* [Reconnaître les tickets de caisse](#recognize-receipts)
* [Entraîner un modèle personnalisé](#train-a-custom-model)
* [Analyser les formulaires avec un modèle personnalisé](#analyze-forms-with-a-custom-model)
* [Gérer vos modèles personnalisés](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Authentifier le client

À l’intérieur de la méthode `Main`, ajoutez le code suivant. Ici, vous allez authentifier deux objets clients à l’aide des variables d’abonnement que vous avez définies ci-dessus. Vous allez utiliser un objet **AzureKeyCredential** pour que, le cas échéant, vous puissiez mettre à jour la clé d’API sans créer de nouveaux objets clients.

> [!IMPORTANT]
> Obtenez votre clé et votre point de terminaison à partir du portail Azure. Si la ressource Form Recognizer que vous avez créée dans la section **Prérequis** a été déployée, cliquez sur le bouton **Accéder à la ressource** sous **Étapes suivantes**. La clé et le point de terminaison se trouvent dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. 
>
> N’oubliez pas de supprimer la clé de votre code une fois que vous avez terminé, et ne la postez jamais publiquement. Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Par exemple, [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

```java
    FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder()
    .credential(new AzureKeyCredential(key))
    .endpoint(endpoint)
    .buildClient();
    
    FormTrainingClient trainingClient = new FormTrainingClientBuilder()
    .credential(new AzureKeyCredential(key))
    .endpoint(endpoint)
    .buildClient();
```

### <a name="call-client-specific-methods"></a>Appeler des méthodes propres au client

Le bloc de code suivant utilise les objets clients pour appeler des méthodes pour chacune des principales tâches dans le kit SDK Form Recognizer. Vous définirez ces méthodes plus tard.

Vous devrez aussi ajouter des références aux URL pour vos données d’entraînement et de test.

* Pour récupérer l’URL SAS des données d’entraînement de votre modèle personnalisé, ouvrez l’Explorateur Stockage Microsoft Azure, cliquez avec le bouton droit sur votre conteneur, puis sélectionnez **Obtenir une signature d’accès partagé**. Assurez-vous que les autorisations de **Lecture** et **Écriture** sont cochées, puis cliquez sur **Créer**. Copiez alors la valeur dans la section **URL**. Il doit avoir le format : `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Pour obtenir l’URL d’un formulaire à tester, vous pouvez utiliser les étapes ci-dessus pour obtenir l’URL SAS d’un document individuel dans le stockage d’objets blob. Vous pouvez aussi prendre l’URL d’un document situé ailleurs.
* Employez également la méthode ci-dessus pour obtenir l’URL d’une image de ticket de caisse.

> [!NOTE]
> Les extraits de code présentés dans ce guide utilisent des formulaires distants accessibles par URL. Si vous voulez traiter des documents de formulaire locaux, consultez les méthodes correspondantes dans la [documentation de référence](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-java-preview).

```java
    String trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    String formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
    String receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media"
    + "/contoso-allinone.jpg";

    // Call Form Recognizer scenarios:
    System.out.println("Get form content...");
    GetContent(recognizerClient, formUrl);

    System.out.println("Analyze receipt...");
    AnalyzeReceipt(recognizerClient, receiptUrl);

    System.out.println("Train Model with training data...");
    String modelId = TrainModel(trainingClient, trainingDataUrl);

    System.out.println("Analyze PDF form...");
    AnalyzePdfForm(recognizerClient, modelId, formUrl);

    System.out.println("Manage models...");
    ManageModels(trainingClient, trainingDataUrl);
```


## <a name="recognize-form-content"></a>Reconnaître le contenu d’un formulaire

Vous pouvez utiliser Form Recognizer pour reconnaître les tables, les lignes et les mots dans les documents, sans avoir besoin d’entraîner un modèle.

Pour reconnaître le contenu d’un fichier à un URI donné, utilisez la méthode **beginRecognizeContentFromUrl**.

```java
private static void GetContent(
    FormRecognizerClient recognizerClient, String invoiceUri)
{
    String analyzeFilePath = invoiceUri;
    SyncPoller<FormRecognizerOperationResult, List<FormPage>> recognizeContentPoller =
        recognizerClient.beginRecognizeContentFromUrl(analyzeFilePath);
    
    List<FormPage> contentResult = recognizeContentPoller.getFinalResult();
```

La valeur retournée est une collection d’objets **FormPage** : un pour chaque page du document envoyé. Le code suivant itère au sein de ces objets et imprime les paires clé/valeur extraites et les données de table.

```java
    contentResult.forEach(formPage -> {
        // Table information
        System.out.println("----Recognizing content ----");
        System.out.printf("Has width: %f and height: %f, measured with unit: %s.%n", formPage.getWidth(),
            formPage.getHeight(),
            formPage.getUnit());
        formPage.getTables().forEach(formTable -> {
            System.out.printf("Table has %d rows and %d columns.%n", formTable.getRowCount(),
                formTable.getColumnCount());
            formTable.getCells().forEach(formTableCell -> {
                System.out.printf("Cell has text %s.%n", formTableCell.getText());
            });
            System.out.println();
        });
    });
}
```

### <a name="output"></a>Output

```console
Get form content...
----Recognizing content ----
Has width: 8.500000 and height: 11.000000, measured with unit: inch.
Table has 2 rows and 6 columns.
Cell has text Invoice Number.
Cell has text Invoice Date.
Cell has text Invoice Due Date.
Cell has text Charges.
Cell has text VAT ID.
Cell has text 458176.
Cell has text 3/28/2018.
Cell has text 4/16/2018.
Cell has text $89,024.34.
Cell has text ET.
```

## <a name="recognize-receipts"></a>Reconnaître les tickets de caisse

Cette section montre comment reconnaître et extraire les champs communs de tickets de caisse émis aux États-Unis à l’aide d’un modèle de ticket préentraîné.

Pour reconnaître les tickets à partir d’un URI, utilisez la méthode **beginRecognizeReceiptsFromUrl**. La valeur retournée est une collection d’objets **RecognizedReceipt** : un pour chaque page du document envoyé.

```java
private static void AnalyzeReceipt(
    FormRecognizerClient recognizerClient, String receiptUri)
{
    SyncPoller<FormRecognizerOperationResult, List<RecognizedForm>> syncPoller =
    recognizerClient.beginRecognizeReceiptsFromUrl(receiptUri);
    List<RecognizedForm> receiptPageResults = syncPoller.getFinalResult();
```

Le bloc de code suivant itère au sein des tickets de caisse et en imprime les détails dans la console.

```java
    for (int i = 0; i < receiptPageResults.size(); i++) {
        RecognizedForm recognizedForm = receiptPageResults.get(i);
        Map<String, FormField> recognizedFields = recognizedForm.getFields();
        System.out.printf("----------- Recognized Receipt page %d -----------%n", i);
        FormField merchantNameField = recognizedFields.get("MerchantName");
        if (merchantNameField != null) {
            if (FieldValueType.STRING == merchantNameField.getValue().getValueType()) {
                String merchantName = merchantNameField.getValue().asString();
                System.out.printf("Merchant Name: %s, confidence: %.2f%n",
                    merchantName, merchantNameField.getConfidence());
            }
        }
        FormField merchantAddressField = recognizedFields.get("MerchantAddress");
        if (merchantAddressField != null) {
            if (FieldValueType.STRING == merchantAddressField.getValue().getValueType()) {
                String merchantAddress = merchantAddressField.getValue().asString();
                System.out.printf("Merchant Address: %s, confidence: %.2f%n",
                    merchantAddress, merchantAddressField.getConfidence());
            }
        }
        FormField transactionDateField = recognizedFields.get("TransactionDate");
        if (transactionDateField != null) {
            if (FieldValueType.DATE == transactionDateField.getValue().getValueType()) {
                LocalDate transactionDate = transactionDateField.getValue().asDate();
                System.out.printf("Transaction Date: %s, confidence: %.2f%n",
                    transactionDate, transactionDateField.getConfidence());
            }
        }
```
Le bloc de code suivant itère au sein des éléments individuels détectés sur le ticket et en imprime les détails dans la console.

```java
        FormField receiptItemsField = recognizedFields.get("Items");
        if (receiptItemsField != null) {
            System.out.printf("Receipt Items: %n");
            if (FieldValueType.LIST == receiptItemsField.getValue().getValueType()) {
                List<FormField> receiptItems = receiptItemsField.getValue().asList();
                receiptItems.stream()
                    .filter(receiptItem -> FieldValueType.MAP == receiptItem.getValue().getValueType())
                    .map(formField -> formField.getValue().asMap())
                    .forEach(formFieldMap -> formFieldMap.forEach((key, formField) -> {
                        if ("Name".equals(key)) {
                            if (FieldValueType.STRING == formField.getValue().getValueType()) {
                                String name = formField.getValue().asString();
                                System.out.printf("Name: %s, confidence: %.2fs%n",
                                    name, formField.getConfidence());
                            }
                        }
                        if ("Quantity".equals(key)) {
                            if (FieldValueType.FLOAT == formField.getValue().getValueType()) {
                                Float quantity = formField.getValue().asFloat();
                                System.out.printf("Quantity: %f, confidence: %.2f%n",
                                    quantity, formField.getConfidence());
                            }
                        }
                        if ("Price".equals(key)) {
                            if (FieldValueType.FLOAT == formField.getValue().getValueType()) {
                                Float price = formField.getValue().asFloat();
                                System.out.printf("Price: %f, confidence: %.2f%n",
                                    price, formField.getConfidence());
                            }
                        }
                        if ("TotalPrice".equals(key)) {
                            if (FieldValueType.FLOAT == formField.getValue().getValueType()) {
                                Float totalPrice = formField.getValue().asFloat();
                                System.out.printf("Total Price: %f, confidence: %.2f%n",
                                    totalPrice, formField.getConfidence());
                            }
                        }
                }));
            }
        }
    }
}
```

### <a name="output"></a>Output 

```console
Analyze receipt...
----------- Recognized Receipt page 0 -----------
Merchant Name: Contoso Contoso, confidence: 0.62
Merchant Address: 123 Main Street Redmond, WA 98052, confidence: 0.99
Transaction Date: 2020-06-10, confidence: 0.90
Receipt Items:
Name: Cappuccino, confidence: 0.96s
Quantity: null, confidence: 0.957s]
Total Price: 2.200000, confidence: 0.95
Name: BACON & EGGS, confidence: 0.94s
Quantity: null, confidence: 0.927s]
Total Price: null, confidence: 0.93
```

## <a name="train-a-custom-model"></a>Entraîner un modèle personnalisé

Cette section montre comment entraîner un modèle avec vos propres données. Un modèle entraîné peut restituer des données structurées qui incluent les relations clé/valeur du document de formulaire d’origine. Une fois que le modèle est entraîné, vous pouvez le tester, le réentraîner et l’utiliser ensuite pour extraire de manière fiable des données d’autres formulaires, en fonction de vos besoins.

> [!NOTE]
> Vous pouvez aussi entraîner des modèles à l’aide d’une interface graphique utilisateur telle que l’[outil d’étiquetage des exemples Form Recognizer](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Entraîner un modèle sans étiquettes

Entraînez les modèles personnalisés à reconnaître tous les champs et valeurs rencontrés dans vos formulaires personnalisés sans étiqueter manuellement les documents d’entraînement.

La méthode suivante entraîne un modèle sur un ensemble donné de documents et imprime l’état du modèle dans la console. 

```java
private static String TrainModel(
    FormTrainingClient trainingClient, String trainingDataUrl)
{
    SyncPoller<FormRecognizerOperationResult, CustomFormModel> trainingPoller =
        trainingClient.beginTraining(trainingDataUrl, false);
    
    CustomFormModel customFormModel = trainingPoller.getFinalResult();
    
    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Training started on: %s%n", customFormModel.getTrainingStartedOn());
    System.out.printf("Training completed on: %s%n%n", customFormModel.getTrainingCompletedOn());
```
L’objet **CustomFormModel** retourné contient des informations sur les types de formulaires que le modèle peut reconnaître et les champs qu’il peut extraire de chaque type de formulaire. Le bloc de code suivant imprime ces informations dans la console.

```java 
    System.out.println("Recognized Fields:");
    // looping through the subModels, which contains the fields they were trained on
    // Since the given training documents are unlabeled, we still group them but they do not have a label.
    customFormModel.getSubmodels().forEach(customFormSubmodel -> {
        // Since the training data is unlabeled, we are unable to return the accuracy of this model
        System.out.printf("The subModel has form type %s%n", customFormSubmodel.getFormType());
        customFormSubmodel.getFields().forEach((field, customFormModelField) ->
            System.out.printf("The model found field '%s' with label: %s%n",
                field, customFormModelField.getLabel()));
    });
```

Enfin, cette méthode retourne l’ID unique du modèle.

```java
    return customFormModel.getModelId();
}
```

### <a name="output"></a>Output

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

### <a name="train-a-model-with-labels"></a>Entraîner un modèle avec des étiquettes

Vous pouvez aussi entraîner les modèles personnalisés en étiquetant manuellement les documents d’entraînement. L’entraînement avec étiquettes offre de meilleures performances dans certains scénarios. Pour entraîner avec des étiquettes, vous devez disposer de fichiers d’informations spéciaux sur les étiquettes ( *\<filename\>.pdf.labels.json*) dans votre conteneur de stockage d’objets blob, en même temps que les documents d’entraînement. L’[outil d’étiquetage des exemples Form Recognizer](../../quickstarts/label-tool.md) propose une interface utilisateur qui facilite la création de ces fichiers d’étiquettes. Une fois ceux-ci à disposition, vous pouvez appeler la méthode **beginTraining** avec le paramètre *useTrainingLabels* défini sur `true`.

```java
private static String TrainModelWithLabels(
    FormTrainingClient trainingClient, String trainingDataUrl)
{
    // Train custom model
    String trainingSetSource = trainingDataUrl;
    SyncPoller<FormRecognizerOperationResult, CustomFormModel> trainingPoller = trainingClient.beginTraining(trainingSetSource, true);

    CustomFormModel customFormModel = trainingPoller.getFinalResult();

    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Training started on: %s%n", customFormModel.getTrainingStartedOn());
    System.out.printf("Training completed on: %s%n%n", customFormModel.getTrainingCompletedOn());
```

Le **CustomFormModel** retourné indique les champs que le modèle peut extraire ainsi qu’une estimation de sa justesse dans chaque champ. Le bloc de code suivant imprime ces informations dans la console.

```java
    // looping through the subModels, which contains the fields they were trained on
    // The labels are based on the ones you gave the training document.
    System.out.println("Recognized Fields:");
    // Since the data is labeled, we are able to return the accuracy of the model
    customFormModel.getSubmodels().forEach(customFormSubmodel -> {
        System.out.printf("The subModel with form type %s has accuracy: %.2f%n",
            customFormSubmodel.getFormType(), customFormSubmodel.getAccuracy());
        customFormSubmodel.getFields().forEach((label, customFormModelField) ->
            System.out.printf("The model found field '%s' to have name: %s with an accuracy: %.2f%n",
                label, customFormModelField.getName(), customFormModelField.getAccuracy()));
    });
    return customFormModel.getModelId();
}
```

### <a name="output"></a>Output

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

## <a name="analyze-forms-with-a-custom-model"></a>Analyser les formulaires avec un modèle personnalisé

Cette section montre comment extraire des informations clé/valeur et d’autres contenus de vos types de formulaires personnalisés à l’aide de modèles que vous avez entraînés avec vos propres formulaires.

> [!IMPORTANT]
> Pour implémenter ce scénario, vous devez avoir déjà entraîné un modèle pour pouvoir passer son ID dans la méthode ci-dessous. Consultez la section [Entraîner un modèle](#train-a-model-without-labels).

Vous allez utiliser la méthode **beginRecognizeCustomFormsFromUrl**. La valeur retournée est une collection d’objets **RecognizedForm** : un pour chaque page du document envoyé.

```java
// Analyze PDF form data
private static void AnalyzePdfForm(
    FormRecognizerClient formClient, String modelId, String pdfFormUrl)
{    
    SyncPoller<FormRecognizerOperationResult, List<RecognizedForm>> recognizeFormPoller =
    formClient.beginRecognizeCustomFormsFromUrl(modelId, pdfFormUrl);

    List<RecognizedForm> recognizedForms = recognizeFormPoller.getFinalResult();
```

Le code suivant imprime les résultats de l’analyse dans la console. Il imprime chaque champ reconnu et la valeur correspondante ainsi qu’un score de confiance.

```java
    for (int i = 0; i < recognizedForms.size(); i++) {
        final RecognizedForm form = recognizedForms.get(i);
        System.out.printf("----------- Recognized custom form info for page %d -----------%n", i);
        System.out.printf("Form type: %s%n", form.getFormType());
        form.getFields().forEach((label, formField) ->
            // label data is populated if you are using a model trained with unlabeled data,
            // since the service needs to make predictions for labels if not explicitly given to it.
            System.out.printf("Field '%s' has label '%s' with a confidence "
                + "score of %.2f.%n", label, formField.getLabelData().getText(), formField.getConfidence()));
    }
}
```

### <a name="output"></a>Output

```console
Analyze PDF form...
----------- Recognized custom form info for page 0 -----------
Form type: form-0
Field 'field-0' has label 'Address:' with a confidence score of 0.91.
Field 'field-1' has label 'Invoice For:' with a confidence score of 1.00.
Field 'field-2' has label 'Invoice Number' with a confidence score of 1.00.
Field 'field-3' has label 'Invoice Date' with a confidence score of 1.00.
Field 'field-4' has label 'Invoice Due Date' with a confidence score of 1.00.
Field 'field-5' has label 'Charges' with a confidence score of 1.00.
Field 'field-6' has label 'VAT ID' with a confidence score of 1.00.
```


## <a name="manage-custom-models"></a>Gérer des modèles personnalisés

Cette section explique comment gérer les modèles personnalisés stockés dans votre compte. Le code suivant effectue toutes les tâches de gestion de modèles dans une même méthode, à titre d’exemple. Commencez par copier la signature de méthode ci-dessous :

```java
private static void ManageModels(
    FormTrainingClient trainingClient, String trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Vérifier le nombre de modèles dans le compte de ressource FormRecognizer

Le bloc de code suivant vérifie le nombre de modèles que vous avez enregistrés dans votre compte Form Recognizer et le compare à la limite du compte.

```java
    AtomicReference<String> modelId = new AtomicReference<>();

    // First, we see how many custom models we have, and what our limit is
    AccountProperties accountProperties = trainingClient.getAccountProperties();
    System.out.printf("The account has %s custom models, and we can have at most %s custom models",
        accountProperties.getCustomModelCount(), accountProperties.getCustomModelLimit());
```

#### <a name="output"></a>Output 

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Lister les modèles actuellement stockés dans le compte de ressource

Le bloc de code suivant liste les modèles actifs dans votre compte et imprime leurs détails dans la console.

```java    
    // Next, we get a paged list of all of our custom models
    PagedIterable<CustomFormModelInfo> customModels = trainingClient.listCustomModels();
    System.out.println("We have following models in the account:");
    customModels.forEach(customFormModelInfo -> {
        System.out.printf("Model Id: %s%n", customFormModelInfo.getModelId());
        // get custom model info
        modelId.set(customFormModelInfo.getModelId());
        CustomFormModel customModel = trainingClient.getCustomModel(customFormModelInfo.getModelId());
        System.out.printf("Model Id: %s%n", customModel.getModelId());
        System.out.printf("Model Status: %s%n", customModel.getModelStatus());
        System.out.printf("Training started on: %s%n", customModel.getTrainingStartedOn());
        System.out.printf("Training completed on: %s%n", customModel.getTrainingCompletedOn());
        customModel.getSubmodels().forEach(customFormSubmodel -> {
            System.out.printf("Custom Model Form type: %s%n", customFormSubmodel.getFormType());
            System.out.printf("Custom Model Accuracy: %.2f%n", customFormSubmodel.getAccuracy());
            if (customFormSubmodel.getFields() != null) {
                customFormSubmodel.getFields().forEach((fieldText, customFormModelField) -> {
                    System.out.printf("Field Text: %s%n", fieldText);
                    System.out.printf("Field Accuracy: %.2f%n", customFormModelField.getAccuracy());
                });
            }
        });
    });
```

#### <a name="output"></a>Output 

Cette réponse a été tronquée dans un souci de lisibilité.

```console
We have following models in the account:
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Status: ready
Training started on: 2020-06-04T18:33:08Z
Training completed on: 2020-06-04T18:33:10Z
Custom Model Form type: form-0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Custom Model Accuracy: 1.00
Field Text: invoice date
Field Accuracy: 1.00
Field Text: invoice number
Field Accuracy: 1.00
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Supprimer un modèle du compte de ressource

Vous pouvez aussi supprimer un modèle de votre compte en référençant son ID.

```java
    // Delete Custom Model
    System.out.printf("Deleted model with model Id: %s, operation completed with status: %s%n", modelId.get(),
    trainingClient.deleteModelWithResponse(modelId.get(), Context.NONE).getStatusCode());
}
```


## <a name="run-the-application"></a>Exécution de l'application

Revenez à votre répertoire de projet principal. Générez ensuite l’application avec la commande suivante :

```console
gradle build
```

Exécutez l’application avec l’objectif `run` :

```console
gradle run
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Dépannage

Les clients Form Recognizer lèvent des exceptions `ErrorResponseException`. Par exemple, si vous essayez de fournir une URL de source de fichier non valide, une exception `ErrorResponseException` est levée avec une erreur indiquant la cause de l’échec. Dans l’extrait de code suivant, l’erreur est gérée correctement via l’interception de l’exception et en affichant des informations supplémentaires sur l’erreur en question.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>Activer la journalisation du client

Les kits SDK Azure pour Java proposent un scénario de journalisation cohérent pour faciliter le dépannage des erreurs d’application et accélérer leur résolution. Les journaux produits capturent le flux d’une application avant d’atteindre l’état terminal pour faciliter la localisation du problème racine. Consultez le [wiki sur la journalisation](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK) pour obtenir des indications sur l’activation de la journalisation.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez utilisé la bibliothèque de client Java Form Recognizer pour entraîner des modèles et analyser des formulaires de différentes manières. Découvrez ensuite les astuces pour créer un jeu de données d’apprentissage plus performant et produire des modèles plus précis.

> [!div class="nextstepaction"]
> [Créer un jeu de données d’apprentissage](../../build-training-data-set.md)

* [Qu’est-ce que Form Recognizer ?](../../overview.md)
* Vous trouverez l’exemple de code de ce guide (et bien plus encore) sur [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples).
