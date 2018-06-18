---
title: Traiter et extraire du texte d’images dans Recherche Azure | Microsoft Docs
description: Traitez et extrayez du texte et d’autres informations d’images dans des pipelines de recherche cognitive dans Recherche Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 566b1e731f137863e9d4bc284d8868d408c7a575
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640198"
---
#  <a name="how-to-process-and-extract-information-from-images-in-cognitive-search-scenarios"></a>Comment traiter et extraire des informations d’images dans des scénarios de recherche cognitive

La recherche cognitive intègre plusieurs fonctionnalités pour l’utilisation d’images et de fichiers image. Pendant le décodage d’un document, vous pouvez utiliser le paramètre *imageAction* pour extraire du texte de photos ou d’images contenant du texte alphanumérique, tel que le mot « STOP » dans le panneau de signalisation d’arrêt. D’autres scénarios incluent la génération d’une représentation textuelle d’une image, telle que « pissenlit » pour une photo de pissenlit ou la couleur « jaune ». Vous pouvez également extraire des métadonnées de l’image, telles que sa taille.

Cet article couvre plus en détail le traitement d’image et fournit des conseils pour l’utilisation d’images dans un pipeline de recherche cognitive.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Obtenir des images normalisées

Dans le cadre du décodage d’un document, il existe un nouvel ensemble de paramètres de configuration de l’indexeur pour gérer des fichiers image ou des images incorporées dans des fichiers. Ces paramètres permettent de normaliser des images en vue d’un traitement ultérieur en aval. La normalisation des images rend celles-ci plus uniformes. Les images de grande taille sont redimensionnées à une hauteur et une largeur maximales afin de les rendre utilisables. Pour les images fournissant des métadonnées d’orientation, la rotation est ajustée pour un chargement vertical. Les ajustements de métadonnées sont capturés dans un type complexe créé pour chaque image. 

Vous ne pouvez pas désactiver la normalisation d’image. Les compétences qui itèrent sur des images attendent des images normalisées.

| Paramètre de configuration | Description |
|--------------------|-------------|
| imageAction   | Défini sur « none » si aucune action ne doit être effectuée quand des images incorporées ou des fichiers image sont rencontrés. <br/>Défini sur « generateNormalizedImages » pour générer un tableau d’images normalisées dans le cadre du décodage de document. Ces images seront exposées dans le champ *normalized_images*. <br/>La valeur par défaut est « none ». Cette configuration est pertinente uniquement pour des sources de données d’objet blob quand le paramètre « dataToExtract » est défini sur « contentAndMetadata ». |
|  normalizedImageMaxWidth | Largeur maximale (en pixels) des images normalisées générées. La valeur par défaut est 2000.|
|  normalizedImageMaxHeight | Hauteur maximale (en pixels) des images normalisées générées. La valeur par défaut est 2000.|

> [!NOTE]
> Si vous ne définissez pas la propriété *imageAction* sur « none », vous ne pouvez définir la propriété *parsingMode* que sur « default ».  Vous ne pouvez définir qu’une seule de ces deux propriétés sur une valeur autre que la valeur par défaut dans la configuration de votre indexeur.

Définissez le paramètre **parsingMode** avec la valeur `json` (pour indexer chaque objet blob en tant que document unique) ou `jsonArray` (si vos objets blob contiennent des tableaux JSON et que vous souhaitez traiter chaque élément de tableau comme un document distinct).

La valeur par défaut de 2000 pixels pour la hauteur et la largeur maximales des images normalisées est basée sur les tailles maximales prises en charge par la [compétence de reconnaissance optique de caractères](cognitive-search-skill-ocr.md) et la [compétence d’analyse d’image](cognitive-search-skill-image-analysis.md). Si vous augmentez les limites maximales, il se peut que le traitement échoue sur des images de grande taille.


Vous spécifiez la propriété imageAction dans votre [définition d’indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer) comme suit :

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
    }
  }
}
```

Si la propriété *imageAction* est définie sur « generateNormalizedImages », le nouveau champ *normalized_images* contiendra un tableau d’images. Chaque image est un type complexe comprenant les membres suivants :

| Membre de l’image       | Description                             |
|--------------------|-----------------------------------------|
| données               | Chaîne codée en Base64 de l’image normalisée au format JPEG.   |
| width              | Largeur de l’image normalisée en pixels. |
| height             | Hauteur de l’image normalisée en pixels. |
| originalWidth      | Largeur d’origine de l’image avant la normalisation. |
| originalHeight      | Hauteur d’origine de l’image avant la normalisation. |
| rotationFromOriginal |  Rotation dans le sens inverse des aiguilles d’une montre exprimée en degrés pour créer l’image normalisée. Valeur comprise entre 0 et 360 degrés. Cette étape lit les métadonnées de l’image générée par un appareil photo ou un scanneur. La valeur est généralement un multiple de 90 degrés. |
| contentOffset |Offset de caractère à l’intérieur du champ de contenu dont l’image a été extraite. Ce champ est applicable uniquement aux fichiers contenant des images incorporées. |

 Exemple de valeur de *normalized_images* :
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500  
  }
]
```

## <a name="image-related-skills"></a>Compétences liées à l’image

Il existe deux compétences cognitives intégrées qui prennent des images en tant qu’entrée : la [reconnaissance optique des caractères](cognitive-search-skill-ocr.md) et l’[analyse d’image](cognitive-search-skill-image-analysis.md). 

Actuellement, ces compétences fonctionnent uniquement avec des images générées à partir de l’étape de décodage du document. Par conséquent, la seule entrée prise en charge est `"/document/normalized_images"`.

### <a name="image-analysis-skill"></a>Compétence d’analyse d’image

La [compétence d’analyse d’image](cognitive-search-skill-image-analysis.md) extrait un ensemble riche de caractéristiques visuelles basées sur le contenu de l’image. Par exemple, à partir d’une image, vous pouvez générer une légende, générer des balises ou identifier des célébrités et des points de repère.

### <a name="ocr-skill"></a>Compétence de reconnaissance optique des caractères

La [compétence de reconnaissance optique des caractères](cognitive-search-skill-ocr.md) extrait du texte de fichiers image, par exemple, aux formats JPG, PNG et bitmap. Elle peut extraire du texte ainsi que des informations de disposition. Les informations de disposition fournissent des rectangles englobants pour chacune des chaînes identifiées.

La compétence de reconnaissance optique des caractères vous permet de sélectionner l’algorithme à utiliser pour détecter le texte dans vos images. Elle prend actuellement en charge deux algorithmes, l’un pour le texte imprimé et l’autre pour le texte manuscrit.

## <a name="embedded-image-scenario"></a>Scénario d’image incorporée

Un scénario courant implique la création d’une chaîne comprenant tous les contenus d’un fichier, tant le texte que le texte d’origine de l’image, en procédant comme suit :  

1. [Extraction de normalized_images](#get-normalized-images).
1. Exécution de la compétence de reconnaissance optique des caractères en utilisant `"/document/normalized_images"` en entrée.
1. Fusion de la représentation textuelle de ces images avec le texte brut extrait du fichier. Vous pouvez utiliser la compétence de [fusion de texte](cognitive-search-skill-textmerger.md) pour combiner les deux blocs de texte dans une seule chaîne de grande taille.

L’exemple suivant de jeu de jeu de compétences crée un champ *merged_text* comprenant le contenu textuel de votre document. Il inclut également le texte obtenu par reconnaissance optique des caractères de chacune des images incorporées. 

#### <a name="request-body-syntax"></a>Syntaxe du corps de la demande
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "name": "OCR skill",
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
          "name": "mergedText", "targetname" : "merged_text"
        }
      ]
    }
  ]
}
```

À présent que vous avez un champ merged_text, vous pouvez le mapper en tant que champ pouvant faire l’objet d’une recherche dans la définition de votre indexeur. Tout le contenu de vos fichiers, y compris le texte des images, sera disponible pour une recherche.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Visualiser les rectangles englobants du texte extrait

Un autre scénario courant est la visualisation des informations de disposition des résultats de recherche. Par exemple, dans le cadre de vos résultats de recherche, vous pouvez vouloir mettre en évidence l’endroit où un élément de texte a été trouvé dans une image.

Étant donné que l’étape de reconnaissance optique des caractères est effectuée sur les images normalisées, les coordonnées de disposition figurent dans l’espace de l’image normalisée. Lorsque vous affichez l’image normalisée, la présence de coordonnées n’est généralement pas un problème mais, dans certains cas, il peut être utile d’afficher l’image d’origine. Dans ce cas, vous pouvez convertir chaque point de coordonnée dans la disposition vers le système de coordonnées de l’image d’origine. 

Pour vous aider, si vous avez besoin de convertir des coordonnées normalisées vers l’espace des coordonnées d’origine, vous pouvez utiliser l’algorithme suivant :

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```

## <a name="see-also"></a>Voir aussi
+ [Créer un indexeur (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ [Compétence d’analyse d’image](cognitive-search-skill-image-analysis.md)
+ [Compétence de reconnaissance optique des caractères](cognitive-search-skill-ocr.md)
+ [Compétence de fusion de texte](cognitive-search-skill-textmerger.md)
+ [Guide pratique pour définir un jeu de compétences](cognitive-search-defining-skillset.md)
+ [Guide pratique pour mapper des champs enrichis](cognitive-search-output-field-mapping.md)
