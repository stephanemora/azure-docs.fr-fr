---
title: Formats de données de la classification de texte personnalisée
titleSuffix: Azure Cognitive Services
description: En savoir plus sur les formats de données acceptés par l’extraction d’entité personnalisée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: f99dd2b0d540cad6fad605e264df4b6054240e35
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097780"
---
# <a name="accepted-data-formats"></a>Formats de données acceptés

Quand les données sont utilisées par votre modèle pour l’entraînement, elles doivent être dans un format spécifique. Quand vous étiquetez vos données dans Language Studio, elles sont converties au format JSON décrit dans cet article. Vous pouvez aussi étiqueter manuellement vos fichiers.


## <a name="json-file-format"></a>Format de fichier JSON

Vos étiquettes doivent être au format `json` ci-dessous.

```json
{
    "classifiers": [
        {
            "name": "Class1"
        },
        {
            "name": "Class2"
        }
    ],
    "documents": [
        {
            "location": "doc1.txt",
            "language": "en-us",
            "classifiers": [
                {
                    "classifierName": "Class2"
                },
                {
                    "classifierName": "Class1"
                }
            ]
        }
    ]
}
```

### <a name="data-description"></a>Description des données

* `classifiers` : tableau de classifieurs pour vos données. Chaque classifieur représente une des classes utilisées pour étiqueter vos données.
* `documents` : tableau des documents étiquetés. Par exemple :
  * `location` : chemin du fichier JSON contenant les étiquettes. Le fichier d’étiquettes doit être à la racine du conteneur de stockage.
  * `language` : langue du document. Utilisez un des [paramètres régionaux de culture pris en charge](../language-support.md).
  * `classifiers` : tableau d’objets classifieur attribués au document. Si votre projet utilise une classification avec une seule étiquette, vous devez avoir un seul classifieur.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’étiquetage de vos données, consultez le [guide pratique](../how-to/tag-data.md). Une fois vos données étiquetées, vous pouvez [entraîner votre modèle](../how-to/train-model.md).  
