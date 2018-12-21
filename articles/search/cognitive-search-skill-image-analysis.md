---
title: Compétence de recherche cognitive Analyse d’image - Recherche Azure
description: Extrayez le texte sémantique par analyse d’image à l’aide de la compétence cognitive Analyse d’image dans un pipeline d’enrichissement Recherche Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: fc8780c5b99ce98a55a6cb08cfaa6585e5a4e89a
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313307"
---
#   <a name="image-analysis-cognitive-skill"></a>Compétence cognitive Analyse d’image

La compétence **Analyse d’image** extrait un ensemble complet de caractéristiques visuelles basées sur le contenu d’une image. Par exemple, vous pouvez générer une légende à partir d’une image, générer des balises ou identifier des célébrités et des paysages.

> [!NOTE]
> À compter du 21 décembre 2018, vous pourrez associer votre ressource Cognitive Services à un ensemble de compétences Recherche Azure. Cela nous permettra de commencer la facturation pour l’exécution des ensembles de compétences. Ce jour-là, nous commencerons également à facturer l’extraction d’images dans le cadre de notre étape de décodage de documents. L’extraction de texte à partir de documents continuera d’être offerte sans frais supplémentaires.
>
> L’exécution des compétences intégrées sera facturée au prix actuel du [paiement à l’utilisation de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Les tarifs de l’extraction d’images sont ceux de la préversion. Ils sont décrits à la page [Tarification Recherche Azure](https://go.microsoft.com/fwlink/?linkid=2042400). En savoir [plus](cognitive-search-attach-cognitive-services.md).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Paramètres de la compétence

Les paramètres respectent la casse.

| Nom du paramètre     | Description |
|--------------------|-------------|
| defaultLanguageCode   |  Chaîne indiquant la langue à retourner. Le service retourne les résultats de la reconnaissance dans une langue donnée. Si ce paramètre n’est pas spécifié, la valeur par défaut est « en ». <br/><br/>Les langues prises en charge sont les suivantes : <br/>*en* : anglais (par défaut) <br/> *zh* : chinois simplifié|
|visualFeatures |   Tableau de chaînes qui indique les types de caractéristiques visuelles à retourner. Les types de caractéristiques visuelles valides sont les suivants :  <ul><li> *categories* : classe le contenu de l’image en fonction d’une taxonomie définie dans la [documentation](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy) Cognitive Services.</li><li> *tags* : balise l’image avec une liste détaillée de mots liés au contenu de l’image.</li><li>*Description* : décrit le contenu de l’image avec une phrase en anglais complète.</li><li>*Faces* : détecte si des visages sont présents. Si tel est le cas, génère des coordonnées, ainsi que des paramètres d’âge et de sexe.</li><li> *ImageType* : détecte si l’image est de type clipart ou un dessin au trait.</li><li>   *Color* : détermine la couleur d’accentuation, la couleur dominante, et si une image est en noir et blanc.</li><li>*Adult* : détecte si l’image est de nature pornographique (nudité ou acte sexuel). Le contenu sexuellement suggestif est également détecté.</li></ul> Les noms des caractéristiques visuelles respectent la casse.|
| détails   | Tableau de chaînes indiquant les détails spécifiques à un domaine à retourner. Les types de caractéristiques visuelles valides sont les suivants : <ul><li>*Celebrities* : identifie les célébrités éventuellement détectées dans l’image.</li><li>*Landmarks* : identifie les paysages éventuellement détectés dans l’image.</li></ul>
 |

## <a name="skill-inputs"></a>Entrées de la compétence

| Nom d’entrée      | Description                                          |
|---------------|------------------------------------------------------|
| image         | Type complexe. Ne fonctionne qu’avec le champ « /documents/normalized_images », généré par l’indexeur d’objets Blob Azure quand ```imageAction``` a la valeur ```generateNormalizedImages```. Pour plus d’informations, consultez [l’exemple](#sample-output).|



##  <a name="sample-definition"></a>Exemple de définition
```json
{
    "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
    "context": "/document/normalized_images/*",
    "visualFeatures": [
        "Tags",
        "Faces",
        "Categories",
        "Adult",
        "Description",
        "ImageType",
        "Color"
    ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "image",
            "source": "/document/normalized_images/*"
        }
    ],
    "outputs": [
        {
            "name": "categories",
            "targetName": "myCategories"
        },
        {
            "name": "tags",
            "targetName": "myTags"
        },
        {
            "name": "description",
            "targetName": "myDescription"
        },
        {
            "name": "faces",
            "targetName": "myFaces"
        },
        {
            "name": "imageType",
            "targetName": "myImageType"
        },
        {
            "name": "color",
            "targetName": "myColor"
        },
        {
            "name": "adult",
            "targetName": "myAdultCategory"
        }
    ]
}
```

##  <a name="sample-input"></a>Exemple d’entrée

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {                
                "image":  {
                               "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                               "width": 500,
                               "height": 300,
                               "originalWidth": 5000,  
                               "originalHeight": 3000,
                               "rotationFromOriginal": 90,
                               "contentOffset": 500  
                           }
            }
        }
    ]
}
```


##  <a name="sample-output"></a>Exemple de sortie

```json
{
    "values": [
      {
        "recordId": "1",
            "data": {
                "categories": [
           {
                        "name": "abstract_",
                        "score": 0.00390625
                    },
                    {
                "name": "people_",
                        "score": 0.83984375,
                "detail": {
                            "celebrities": [
                                {
                                    "name": "Satya Nadella",
                                    "faceBoundingBox": {
                                        "left": 597,
                                        "top": 162,
                                        "width": 248,
                                        "height": 248
                                    },
                                    "confidence": 0.999028444
                                }
                            ],
                            "landmarks": [
                                {
                                    "name": "Forbidden City",
                                    "confidence": 0.9978346
                                }
                            ]
                        }
                    }
                ],
                "adult": {
                    "isAdultContent": false,
                    "isRacyContent": false,
                    "adultScore": 0.0934349000453949,
                    "racyScore": 0.068613491952419281
                },
                "tags": [
                    {
                        "name": "person",
                        "confidence": 0.98979085683822632
                    },
                    {
                        "name": "man",
                        "confidence": 0.94493889808654785
                    },
                    {
                        "name": "outdoor",
                        "confidence": 0.938492476940155
                    },
                    {
                        "name": "window",
                        "confidence": 0.89513939619064331
                    }
                ],
                "description": {
                    "tags": [
                        "person",
                        "man",
                        "outdoor",
                        "window",
                        "glasses"
                    ],
                    "captions": [
                        {
                            "text": "Satya Nadella sitting on a bench",
                            "confidence": 0.48293603002174407
                        }
                    ]
                },
                "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
                "metadata": {
                    "width": 1500,
                    "height": 1000,
                    "format": "Jpeg"
                },
                "faces": [
                    {
                        "age": 44,
                        "gender": "Male",
                    "faceBoundingBox": {
                            "left": 593,
                            "top": 160,
                            "width": 250,
                            "height": 250
                        }
                    }
                ],
                "color": {
                    "dominantColorForeground": "Brown",
                    "dominantColorBackground": "Brown",
                    "dominantColors": [
                        "Brown",
                        "Black"
                    ],
                    "accentColor": "873B59",
                    "isBwImg": false
                    },
                "imageType": {
                    "clipArtType": 0,
                    "lineDrawingType": 0
                }
           }
      }
    ]
}
```


## <a name="error-cases"></a>Cas d’erreur
Dans les cas d’erreur suivants, aucun élément n’est extrait.

| Code d'erreur | Description |
|------------|-------------|
| NotSupportedLanguage | La langue fournie n’est pas prise en charge. |
| InvalidImageUrl | L’URL de l’image est incorrecte ou inaccessible.|
| InvalidImageFormat | Les données d’entrée ne sont pas une image valide. |
| InvalidImageSize | L’image d’entrée est trop grande. |
| NotSupportedVisualFeature  | Le type de caractéristique spécifié n’est pas valide. |
| NotSupportedImage | Image non prise en charge, par exemple, pornographie enfantine. |
| InvalidDetails | Modèle spécifique à un domaine non pris en charge. |

## <a name="see-also"></a>Voir aussi

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Créer un indexeur (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
