---
title: Extraction des relations dans l’Analyse de texte pour la santé
titleSuffix: Azure Cognitive Services
description: Découvrir l’extraction des relations
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 6fd838471387d7ef1b54beb9ead7b802f6e041e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028992"
---
# <a name="relation-extraction"></a>Extraction de relations

L’extraction de relations identifie les connexions pertinentes entre les concepts mentionnés dans le texte. Par exemple, une relation « temps de la condition » est trouvée en associant un nom de condition à un temps ou entre une abréviation et la description complète.  


## <a name="relation-extraction-output"></a>Sortie d’extraction de relations

L’Analyse de texte pour la santé reconnaît les relations entre les différents concepts, notamment les relations entre l’attribut et l’entité (par exemple, la direction de la structure du corps, la posologie du médicament) et entre les entités (par exemple, la détection des abréviations).

> [!NOTE]
> * Les relations faisant référence à la CONDITION peuvent faire référence au type d’entité DIAGNOSIS ou SYMPTOM_OR_SIGN.
> * Les relations faisant référence à un médicament peuvent faire référence au type d’entité MEDICATION_NAME ou au MEDICATION_CLASS.
> * Les relations qui font référence au temps peuvent faire référence au type d’entité TIME ou DATE.

La sortie de l’extraction des relations contient les références URI et les rôles attribués des entités du type de relation. Par exemple, dans le code JSON suivant :

```json
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
...
]
```

## <a name="recognized-relations"></a>Relations reconnues

Les relations suivantes peuvent être retournées par l’API. 

**ABBREVIATION**

**BODY_SITE_OF_CONDITION**

**BODY_SITE_OF_TREATMENT**

**COURSE_OF_CONDITION**

**COURSE_OF_EXAMINATION**

**COURSE_OF_MEDICATION**

**COURSE_OF_TREATMENT**

**DIRECTION_OF_BODY_STRUCTURE**

**DIRECTION_OF_CONDITION**

**DIRECTION_OF_EXAMINATION**

**DIRECTION_OF_TREATMENT**

**DOSAGE_OF_MEDICATION**

**EXAMINATION_FINDS_CONDITION**

**EXPRESSION_OF_GENE**

**EXPRESSION_OF_VARIANT**

**FORM_OF_MEDICATION**

**FREQUENCY_OF_CONDITION**

**FREQUENCY_OF_MEDICATION**

**FREQUENCY_OF_TREATMENT**

**MUTATION_TYPE_OF_GENE**

**MUTATION_TYPE_OF_VARIANT**

**QUALIFIER_OF_CONDITION**

**RELATION_OF_EXAMINATION**

**ROUTE_OF_MEDICATION** 

**SCALE_OF_CONDITION**

**TIME_OF_CONDITION**

**TIME_OF_EVENT**

**TIME_OF_EXAMINATION**

**TIME_OF_MEDICATION**

**TIME_OF_TREATMENT**

**UNIT_OF_CONDITION**

**UNIT_OF_EXAMINATION**

**VALUE_OF_CONDITION**  

**VALUE_OF_EXAMINATION**

**VARIANT_OF_GENE**
