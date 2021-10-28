---
title: 'Guide pratique : Migrer votre application de la version 2.1 à la version 3.0 de Form Recognizer.'
titleSuffix: Azure Applied AI Services
description: Dans ce guide pratique, vous apprendrez les différences entre la version 2.1 et la version 3.0 de l’API Form Recognizer. Vous découvrirez également les modifications à apporter pour passer à la nouvelle version de l’API.
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 10/07/2021
ms.author: vikurpad
recommendations: false
ms.openlocfilehash: 11010ebe6a4afa8698491dacfb495625a8445779
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240411"
---
# <a name="form-recognizer-v30-migration--preview"></a>Migration Form Recognizer v3.0 | Préversion

> [!IMPORTANT]
>
> La version 3.0 de l’API REST de Form Recognizer introduit des changements cassants dans la requête de l’API REST et l’analyse de la réponse JSON.

Form Recognizer v3.0 (préversion) introduit plusieurs nouvelles fonctionnalités et capacités :

* L’[API REST de Form Recognizer](quickstarts/try-v3-rest-api.md) a été remaniée pour une meilleure convivialité.
* Le modèle [**Document général (v3.0)**](concept-general-document.md) est une nouvelle API qui extrait le texte, les tableaux, la structure, les paires clé-valeur et les entités nommées des formulaires et des documents.
* Le modèle [**Reçu (v3.0)**](concept-receipt.md) prend en charge le traitement des reçus d’hôtel d’une seule page.
* Le modèle [**Document d’identité (v3.0)**](concept-id-document.md) prend en charge l’extraction des approbations, des restrictions et des classifications de véhicules à partir de permis de conduire américains.
* L’[**API Modèle personnalisé (v3.0)**](concept-custom.md) prend en charge la détection de signatures pour les formulaires personnalisés.

Dans cet article, vous allez découvrir les différences entre les versions 2.1 et 3.0 de Form Recognizer et comment passer à la version la plus récente de l’API.

## <a name="changes-to-the-rest-api-endpoints"></a>Modifications apportées aux points de terminaison de l’API REST

 L’API REST v3.0 combine les opérations d’analyse pour l’analyse de la disposition, les modèles prédéfinis et les modèles personnalisés en une seule paire d’opérations en assignant **`documentModels`** et **`modelId`** à l’analyse de la disposition (disposition prédéfinie) et aux modèles prédéfinis.

### <a name="post-request"></a>requête POST

```http
https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-07-30-preview

```

### <a name="get-request"></a>Requête GET

```http
https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}/AnalyzeResult/{resultId}?api-version=2021-07-30-preview
```

### <a name="analyze-operation"></a>Opération d’analyse

* La charge utile de la demande et le modèle d’appel restent inchangés.
* L’opération d’analyse spécifie le document d’entrée et les configurations spécifiques au contenu, et elle renvoie l’URL du résultat de l’analyse via l’en-tête Operation-Location de la réponse.
* Interrogez cette URL de résultat d’analyse, via une requête GET, pour vérifier l’état de l’opération d’analyse (l’intervalle minimum recommandé entre les requêtes est d’une seconde).
* En cas de réussite, l’état est défini sur « succeeded » et [analyzeResult](#changes-to-analyze-result) est renvoyé dans le corps de la réponse. Si des erreurs se produisent, l’état est défini sur « failed » et une erreur est renvoyée.

| Modèle | v2.1 | v3.0 |
|:--| :--| :--|
| **Préfixe de l’URL de la demande**| **https://{your-form-recognizer-endpoint}/formrecognizer/v2.1**  | **https://{your-form-recognizer-endpoint}/formrecognizer** |
|🆕 **Document général**|N/A|/documentModels/prebuilt-document:analyze |
| **Disposition**| /layout/analyze |/documentModels/prebuilt-layout:analyze|
|**Personnalisée**| /custom/{modelId}/analyze    |/documentModels/{modelId}:analyze |
| **Facture** | /prebuilt/invoice/analyze    | /documentModels/prebuilt-invoice:analyze |
| **Réception** | /prebuilt/receipt/analyze    | /documentModels/prebuilt-receipt:analyze |
| **Document d’identité** | /prebuilt/idDocument/analyze |  /documentModels/prebuilt-idDocument:analyze |
|**Carte de visite**| /prebuilt/businessCard/analyze| /documentModels/prebuilt-businessCard:analyze|

### <a name="analyze-request-body"></a>Corps de la demande d’analyse

Le contenu à analyser est fourni par le corps de la demande. L’URL ou les données codées en base64 peuvent être utilisées pour construire la requête.

  Pour spécifier une URL web accessible publiquement, définissez Content-Type sur **application/json** et envoyez le corps JSON suivant :

  ```json
  {
    "urlSource": "{urlPath}"
  }
  ```

L’encodage Base64 est également pris en charge par Form Recognizer v3.0 :

```json
{
  "base64Source": "{base64EncodedContent}"
}
```

### <a name="additional-parameters"></a>Paramètres supplémentaires

Paramètres qui continuent d’être pris en charge :

* pages
* locale

Paramètres qui ne sont plus pris en charge : 

* includeTextDetails

Le nouveau format de réponse est plus compact et la sortie complète est toujours renvoyée.

## <a name="changes-to-analyze-result"></a>Modifications du résultat d’analyse

L’analyse de la réponse a été refactorisée aux résultats de haut niveau suivants pour prendre en charge les éléments multipages.
* pages
* dans des tables
* keyValuePairs
* entities
* styles
* dans des documents

> [!NOTE]
>
> Les modifications apportées à la réponse analyzeResult incluent un certain nombre de modifications, comme le fait de passer d’une propriété de pages à une propriété de levier supérieur dans analyzeResult.

```json

{
// Basic analyze result metadata
"apiVersion": "2021-07-30-preview", // REST API version used
"modelId": "prebuilt-invoice", // ModelId used
"stringIndexType": "textElements", // Character unit used for string offsets and lengths:
// textElements, unicodeCodePoint, utf16CodeUnit // Concatenated content in global reading order across pages.
// Words are generally delimited by space, except CJK (Chinese, Japanese, Korean) characters.
// Lines and selection marks are generally delimited by newline character.
// Selection marks are represented in Markdown emoji syntax (:selected:, :unselected:).
"content": "CONTOSO LTD.\nINVOICE\nContoso Headquarters...", "pages": [ // List of pages analyzed
{
// Basic page metadata
"pageNumber": 1, // 1-indexed page number
"angle": 0, // Orientation of content in clockwise direction (degree)
"width": 0, // Page width
"height": 0, // Page height
"unit": "pixel", // Unit for width, height, and bounding box coordinates
"spans": [ // Parts of top-level content covered by page
{
"offset": 0, // Offset in content
"length": 7 // Length in content
}
], // List of words in page
"words": [
{
"text": "CONTOSO", // Equivalent to $.content.Substring(span.offset, span.length)
"boundingBox": [ ... ], // Position in page
"confidence": 0.99, // Extraction confidence
"span": { ... } // Part of top-level content covered by word
}, ...
], // List of selectionMarks in page
"selectionMarks": [
{
"state": "selected", // Selection state: selected, unselected
"boundingBox": [ ... ], // Position in page
"confidence": 0.95, // Extraction confidence
"span": { ... } // Part of top-level content covered by selection mark
}, ...
], // List of lines in page
"lines": [
{
"content": "CONTOSO LTD.", // Concatenated content of line (may contain both words and selectionMarks)
"boundingBox": [ ... ], // Position in page
"spans": [ ... ], // Parts of top-level content covered by line
}, ...
]
}, ...
], // List of extracted tables
"tables": [
{
"rowCount": 1, // Number of rows in table
"columnCount": 1, // Number of columns in table
"boundingRegions": [ // Bounding boxes potentially across pages covered by table
{
"pageNumber": 1, // 1-indexed page number
"boundingBox": [ ... ], // Bounding box
}
],
"spans": [ ... ], // Parts of top-level content covered by table // List of cells in table
"cells": [
{
"kind": "stub", // Cell kind: content (default), rowHeader, columnHeader, stub, description
"rowIndex": 0, // 0-indexed row position of cell
"columnIndex": 0, // 0-indexed column position of cell
"rowSpan": 1, // Number of rows spanned by cell (default=1)
"columnSpan": 1, // Number of columns spanned by cell (default=1)
"content": "SALESPERSON", // Concatenated content of cell
"boundingRegions": [ ... ], // Bounding regions covered by cell
"spans": [ ... ] // Parts of top-level content covered by cell
}, ...
]
}, ...
], // List of extracted key-value pairs
"keyValuePairs": [
{
"key": { // Extracted key
"content": "INVOICE:", // Key content
"boundingRegions": [ ... ], // Key bounding regions
"spans": [ ... ] // Key spans
},
"value": { // Extracted value corresponding to key, if any
"content": "INV-100", // Value content
"boundingRegions": [ ... ], // Value bounding regions
"spans": [ ... ] // Value spans
},
"confidence": 0.95 // Extraction confidence
}, ...
], // List of extracted entities
"entities": [
{
"category": "DateTime", // Primary entity category
"subCategory": "Date", // Secondary entity category
"content": "11/15/2019", // Entity content
"boundingRegions": [ ... ], // Entity bounding regions
"spans": [ ... ], // Entity spans
"confidence": 0.99 // Extraction confidence
}, ...
], // List of extracted styles
"styles": [
{
"isHandwritten": true, // Is content in this style handwritten?
"spans": [ ... ], // Spans covered by this style
"confidence": 0.95 // Detection confidence
}, ...
], // List of extracted documents
"documents": [
{
"docType": "prebuilt-invoice", // Classified document type (model dependent)
"boundingRegions": [ ... ], // Document bounding regions
"spans": [ ... ], // Document spans
"confidence": 0.99, // Document splitting/classification confidence // List of extracted fields
"fields": {
"VendorName": { // Field name (docType dependent)
"type": "string", // Field value type: string, number, array, object, ...
"valueString": "CONTOSO LTD.",// Normalized field value
"content": "CONTOSO LTD.", // Raw extracted field content
"boundingRegions": [ ... ], // Field bounding regions
"spans": [ ... ], // Field spans
"confidence": 0.99 // Extraction confidence
}, ...
}
}, ...
]
}



```

## <a name="build-or-train-model"></a>Créer un modèle ou effectuer l’apprentissage d’un modèle

L’objet de modèle fait l’objet de deux mises à jour dans la nouvelle API :
* ```modelId``` est désormais une propriété qui peut être définie sur un modèle pour un nom lisible par l’utilisateur.
* ```modelName``` a été renommé en ```description```

L’opération ```build``` est appelée pour effectuer l’apprentissage d’un modèle. La charge utile de la demande et le modèle d’appel restent inchangés. L’opération de génération spécifie le modèle et le jeu de données de formation, et elle renvoie le résultat via l’en-tête Operation-Location dans la réponse. Interrogez cette URL d’opération de modèle, via une requête GET, pour vérifier l’état de l’opération de génération (l’intervalle minimum recommandé entre les requêtes est d’une seconde). Contrairement à la version 2.1, cette URL n’est pas l’emplacement de la ressource du modèle. Au lieu de cela, l’URL du modèle peut être construite à partir du modelId donné, également récupéré dans la propriété resourceLocation de la réponse. En cas de réussite, l’état est défini sur ```succeeded``` et le résultat contient les informations sur le modèle personnalisé. Si des erreurs se produisent, l’état est défini sur ```failed``` et l’erreur est renvoyée.

Le code suivant est un exemple de demande de génération utilisant un jeton SAP. Notez la barre oblique de fin lorsque vous définissez le préfixe ou le chemin du dossier.

```json
POST https://{your-form-recognizer-endpoint}/formrecognizer/documentModels:build?api-version=2021-09-30-preview

{
  "modelId": {modelId},
  "description": "Sample model",
  "azureBlobSource": {
    "containerUrl": "https://{storageAccount}.blob.core.windows.net/{containerName}?{sasToken}",
    "prefix": "{folderName/}"
  }
}
```
## <a name="changes-to-compose-model"></a>Modifications apportées au modèle de composition

Le modèle de composition est maintenant limité à un seul niveau d’imbrication. Les modèles composés sont désormais cohérents avec les modèles personnalisés grâce à l’ajout des propriétés ```modelId``` et ```description```.

```json
POST https://{your-form-recognizer-endpoint}/formrecognizer/documentModels:compose?api-version=2021-09-30-preview
{
  "modelId": "{composedModelId}",
  "description": "{composedModelDescription}",
  "componentModels": [
    { "modelId": "{modelId1}" },
    { "modelId": "{modelId2}" },
  ]
}

```

## <a name="changes-to-copy-model"></a>Modifications apportées au modèle de copie

Le modèle d’appel pour le modèle de copie reste inchangé :

* Autorisez l’opération de copie avec la ressource cible qui appelle ```authorizeCopy```. Désormais une requête POST.
* Envoyez l’autorisation à la ressource source pour copier le modèle appelant ```copy-to```.
* Interrogez l’opération renvoyée pour valider l’opération terminée avec succès.

Les seules modifications apportées à la fonction du modèle de copie sont les suivantes :

* L’action HTTP sur ```authorizeCopy``` est désormais une requête POST.
* La charge utile d’autorisation contient toutes les informations nécessaires pour envoyer la demande de copie.

Autorisez la copie.
```json
POST https://{targetHost}/formrecognizer/documentModels:authorizeCopy?api-version=2021-09-30-preview
{
  "modelId": "{targetModelId}",
  "description": "{targetModelDescription}",
}
```
Utilisez le corps de réponse de l’action d’autorisation pour construire la demande de copie.

```json
POST https://{sourceHost}/formrecognizer/documentModels/{sourceModelId}:copy-to?api-version=2021-09-30-preview
{
  "targetResourceId": "{targetResourceId}",
  "targetResourceRegion": "{targetResourceRegion}",
  "targetModelId": "{targetModelId}",
  "targetModelLocation": "https://{targetHost}/formrecognizer/documentModels/{targetModelId}",
  "accessToken": "{accessToken}",
  "expirationDateTime": "2021-08-02T03:56:11Z"
}
```

## <a name="changes-to-list-models"></a>Modifications apportées aux modèles de liste

Les modèles de liste ont été étendus et renvoient désormais des modèles prédéfinis et personnalisés. Tous les noms de modèles prédéfinis commencent par ```prebuilt-```. Seuls les modèles dont l’état est réussi sont renvoyés. Pour répertorier les modèles qui ont échoué ou qui sont en cours d’utilisation, consultez la section [Opérations de liste](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetModels).

Exemple de demande de modèles de liste

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/documentModels?api-version=2021-09-30-preview
```

## <a name="change-to-get-model"></a>Modifications apportées au modèle Get

Étant donné que le modèle Get comprend désormais des modèles prédéfinis, l’opération Get renvoie un dictionnaire ```docTypes```. Chaque type de document est décrit par son nom, une description facultative, un schéma de champ et une confiance facultative dans le champ. Le schéma de champ décrit la liste des champs potentiellement renvoyés avec le type de document.

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-09-30-preview
```

## <a name="new-get-info-operation"></a>Nouvelle opération Get info
L’opération ```info``` sur le service renvoie le nombre de modèles personnalisés et la limite de modèles personnalisés.

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/info? api-version=2021-09-30-preview
```
Exemple de réponse
```json
{
  "customDocumentModels": {
    "count": 5,
    "limit": 100
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de migration, vous avez appris à mettre à niveau votre application Form Recognizer existante pour utiliser les API v3.0. Continuez à utiliser l’API 2.1 pour toutes les fonctionnalités de disponibilité générale, et utilisez l’API 3.0 pour toutes les fonctionnalités d’évaluation.

* [Passer en revue la nouvelle API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)
* [Qu’est-ce que Form Recognizer ?](overview.md)
* [Démarrage rapide Form Recognizer](./quickstarts/try-sdk-rest-api.md)