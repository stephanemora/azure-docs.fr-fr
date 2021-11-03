---
title: Formats de données de la reconnaissance d’entités nommées personnalisées
titleSuffix: Azure Cognitive Services
description: Découvrez les formats de données acceptés par la reconnaissance d’entités nommées personnalisées.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: de37751b3d814110b5d37f1b7c84d41278b2bea9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098061"
---
# <a name="data-formats-accepted-by-custom-ner"></a>Formats de données acceptés par la reconnaissance d’entités nommées personnalisées

Les données utilisées par le modèle pour l’apprentissage doivent être dans un format spécifique. Quand vous étiquetez vos données dans Language Studio, elles sont converties au format JSON décrit dans cet article. Vous pouvez aussi étiqueter manuellement vos fichiers.


## <a name="json-file-format"></a>Format de fichier JSON

Lorsque vous étiquetez des entités, les balises sont enregistrées au format JSON suivant. Si vous chargez un fichier de balises, celui-ci doit respecter le même format.

```json
{
    //List of entity names. Their index within this array is used as an ID. 
    "entityNames": [
        "entity_name1",
        "entity_name2"
    ],
    "documents": "path_to_document", //Relative file path to get the text.
    "culture": "en-US", //Standard culture strings supported by CultureInfo.
    "entities": [
        {
            "regionStart": 0,
            "regionLength": 69,
            "labels": [
                {
                    "entity": 0, // Index of the entity in the "entityNames" array. Positions are relative to the original text (not bounding box)
                    "start": 4,
                    "length": 10
                },
                {
                    "entity": 1,
                    "start": 18,
                    "length": 11
                }
            ]
        }
    ]    
}
```

La liste suivante décrit les différentes propriétés JSON de l’exemple ci-dessus.

* `entityNames` : tableau de noms d’entités. C’est l’index de l’entité dans le tableau qui sert d’ID.
* `documents` : tableau de documents étiquetés.
  * `location` : chemin du document par rapport au fichier JSON, par exemple `file.txt` pour les documents situés au même niveau que le fichier de balises et `dir1/file.txt` pour ceux qui se trouvent au sein d’un niveau de répertoire.
  * `culture` : culture/langue du document. <!-- See [language support](../language-support.md) for more information. -->
  * `entities` : balises de reconnaissance d’entités.
    * `regionStart` : position de caractère inclusive du début du texte.
    * `regionLength` : longueur du rectangle englobant en caractères UTF-16. L’apprentissage ne prend en compte que les données de cette région. Par conséquent, s’il s’agit d’un fichier balisé, affectez la valeur `regionStart` à 0 et la valeur `regionLength` au dernier index du dernier caractère du fichier. Vous pouvez également définir cette région comme une partie du fichier sans balises si vous souhaitez introduire un échantillon négatif pour l’apprentissage.

    * `labels` : toutes les balises qui se trouvent dans le rectangle englobant.
      * `entity` : index de l’entité dans le tableau `entityNames`.
      * `start` : position de caractère inclusive du début de la balise dans le texte du document. Il ne s’agit pas d’une valeur relative exprimée par rapport au rectangle englobant.
      * `length` : longueur de la balise en caractères UTF-16.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’étiquetage de vos données, consultez le [guide pratique](../how-to/tag-data.md). Une fois vos données étiquetées, vous pouvez [entraîner votre modèle](../how-to/train-model.md).  
