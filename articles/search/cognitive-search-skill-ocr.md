---
title: Compétence de recherche cognitive OCR - Recherche Azure
description: Extrayez le texte de fichiers image à l’aide de la reconnaissance optique de caractères (OCR) dans un pipeline d’enrichissement Recherche Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: fb7fe0689ce54031880565c0c6409afeab2ff523
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777889"
---
# <a name="ocr-cognitive-skill"></a>Compétence cognitive OCR

La compétence de reconnaissance optique de caractères (OCR) reconnaît le texte imprimé et manuscrit dans des fichiers image. Cette compétence utilise les modèles d’apprentissage automatique fournis par [Vision par ordinateur](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) dans Cognitive Services. La compétence de **reconnaissance optique de caractères (OCR)** est mappée à la fonctionnalité suivante :

+ Lorsque textExtractionAlgorithm est définie sur « handwritten », la fonctionnalité [« RecognizeText »](../cognitive-services/computer-vision/quickstarts-sdk/csharp-hand-text-sdk.md) est utilisée.
+ Lorsque textExtractionAlgorithm est définie sur « printed », la fonctionnalité [« OCR »](../cognitive-services/computer-vision/concept-extracting-text-ocr.md) est utilisée pour les langues autres que l’anglais. Pour l’anglais, la nouvelle fonctionnalité [« Reconnaître le texte »](../cognitive-services/computer-vision/concept-recognizing-text.md) est utilisée pour le texte imprimé.

La compétence **OCR** extrait le texte de fichiers d’image. Les formats de fichiers pris en charge sont les suivants :

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF
+ .TIFF

> [!NOTE]
> Depuis le 21 décembre 2018, vous pouvez [attacher une ressource Cognitive Services](cognitive-search-attach-cognitive-services.md) à un ensemble de compétences Recherche Azure. Cela nous permet de commencer à facturer l’exécution de l’ensemble de compétences. Ce jour-là, nous avons également commencé à facturer l’extraction d’images dans le cadre de notre étape de décodage de documents. L’extraction de texte à partir de documents est toujours offerte sans frais supplémentaires.
>
> L’exécution de [compétences cognitives intégrées](cognitive-search-predefined-skills.md) est facturée au [tarif de paiement à l’utilisation de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services), qui est le même que si vous aviez exécuté la tâche directement. L’extraction d’image est une opération facturable de Recherche Azure, actuellement proposée au tarif de la préversion. Pour plus d’informations, consultez la [page des tarifs de Recherche Azure](https://go.microsoft.com/fwlink/?linkid=2042400) ou [Comment la facturation fonctionne](search-sku-tier.md#how-billing-works).

## <a name="skill-parameters"></a>Paramètres de la compétence

Les paramètres respectent la casse.

| Nom du paramètre     | Description |
|--------------------|-------------|
| detectOrientation | Active la détection automatique de l’orientation de l’image. <br/> Valeurs valides : true, false.|
|defaultLanguageCode | <p>  Code de langue du texte d’entrée. Les langages pris en charge incluent : <br/> zh-Hans (chinois simplifié) <br/> zh-Hant (chinois traditionnel) <br/>cs (tchèque) <br/>da (danois) <br/>nl (néerlandais) <br/>en (anglais) <br/>fi (finnois)  <br/>fr (français) <br/>  de (allemand) <br/>el (grec) <br/> hu (hongrois) <br/> it (italien) <br/>  ja (japonais) <br/> ko (coréen) <br/> nb (norvégien) <br/>   pl (polonais) <br/> pt (portugais) <br/>  ru (russe) <br/>  es (espagnol) <br/>  sv (suédois) <br/>  tr (turc) <br/> ar (arabe) <br/> ro (roumain) <br/> sr-Cyrl (serbe cyrillique) <br/> sr-Latn (serbe latin) <br/>  sk (slovaque) <br/>  unk (inconnu) <br/><br/> Si le code langue n’est pas spécifié ou est Null, la langue est définie automatiquement sur l’anglais. Si la langue est explicitement définie sur « unk », la langue sera détectée automatiquement. </p> |
| textExtractionAlgorithm | « printed » (imprimé) ou « handwritten » (manuscrit). L’algorithme OCR de reconnaissance de texte « handwritten » est actuellement en préversion et n’est pris en charge qu’en anglais. |

## <a name="skill-inputs"></a>Entrées de la compétence

| Nom d’entrée      | Description                                          |
|---------------|------------------------------------------------------|
| image         | Type complexe. Ne fonctionne actuellement qu'avec le champ « /documents/normalized_images », généré par l'indexeur d'objets Blob Azure lorsque ```imageAction``` est défini sur une valeur supérieure à ```none```. Pour plus d’informations, consultez [l’exemple](#sample-output).|


## <a name="skill-outputs"></a>Sorties de la compétence
| Nom de sortie     | Description                   |
|---------------|-------------------------------|
| texte          | Texte brut extrait de l’image.   |
| layoutText    | Type complexe qui décrit le texte extrait ainsi que l’emplacement où le texte a été trouvé.|


## <a name="sample-definition"></a>Exemple de définition

```json
{
  "skills": [
    {
      "description": "Extracts text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": null,
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text",
          "targetName": "myText"
        },
        {
          "name": "layoutText",
          "targetName": "myLayoutText"
        }
      ]
    }
  ]
}
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Exemple de sortie text et layoutText

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Exemple : Fusion du texte extrait d’images incorporées avec le contenu du document.

La fusion de texte permet notamment de fusionner la représentation textuelle d’images (texte issu d’une compétence OCR ou légende d’une image) dans le champ de contenu d’un document.

L’exemple d’ensemble de compétences suivant crée un champ *merged_text*. Ce champ comprend le contenu textuel de votre document et le texte obtenu par reconnaissance optique de caractères de chacune des images incorporées dans ce document.

#### <a name="request-body-syntax"></a>Syntaxe du corps de la demande
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset"
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
L’exemple d’ensemble de compétences ci-dessus suppose l’existence d’un champ d’images normalisées. Pour générer ce champ, définissez la configuration *imageAction* dans la définition de l’indexeur sur *generateNormalizedImages* comme indiqué ci-dessous :

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Voir aussi
+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Compétence Fusion de texte](cognitive-search-skill-textmerger.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Créer un indexeur (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
