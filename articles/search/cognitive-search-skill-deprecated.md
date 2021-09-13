---
title: Compétences cognitives dépréciées
titleSuffix: Azure Cognitive Search
description: Cette page contient la liste des compétences cognitives qui sont considérées comme déconseillées et qui ne seront pas prises en charge dans un avenir proche dans les ensembles de compétences de Recherche cognitive Azure.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b44ba49fea555652bdf513e40c3e030ec5862cf5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563308"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Compétences cognitives dépréciées dans la Recherche cognitive Azure

Ce document décrit les compétences cognitives qui sont considérées comme déconseillées. Utilisez le guide suivant pour le contenu :

* Nom de compétence : nom de la compétence dépréciée. Il est mappé à l’attribut @odata.type.
* Dernière version de l’API disponible : dernière version de l’API publique de la Recherche cognitive Azure via laquelle l’ensemble de compétences contenant la compétence déconseillée correspondante peut être créée/mise à jour. Les indexeurs auxquels sont attachés des ensembles de compétences ayant ces compétences continueront de s’exécuter même dans les futures versions d’API jusqu’à la date de « fin de support », date après laquelle ils cesseront de s’exécuter correctement.
* Fin de support : jour au-delà duquel la compétence correspondante est considérée comme non prise en charge et cesse de fonctionner. Les compétences créées précédemment doivent continuer à fonctionner, mais il est recommandé aux utilisateurs de migrer hors d’une compétence déconseillée.
* Recommandations : chemin de migration vers l’avant pour utiliser une compétence prise en charge. Il est conseillé aux utilisateurs de suivre ces suggestions pour continuer à bénéficier du support technique.

Si vous utilisez [Microsoft.Skills.Text.EntityRecognitionSkill](#microsoftskillstextentityrecognitionskill), cet article va vous aider à mettre à niveau votre ensemble de compétences vers [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md), qui est en disponibilité générale et introduit de nouvelles fonctionnalités. 

Si vous utilisez [Microsoft.Skills.Text.SentimentSkill](#microsoftskillstextsentimentskill), cet article va vous aider à mettre à niveau votre ensemble de compétences vers [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md), qui est en disponibilité générale et introduit de nouvelles fonctionnalités. 

Si vous utilisez [Microsoft.Skills.Text.NamedEntityRecognitionSkill](#microsoftskillstextnamedentityrecognitionskill), cet article va vous aider à mettre à niveau votre ensemble de compétences vers [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md), qui est en disponibilité générale et introduit de nouvelles fonctionnalités.

## <a name="microsoftskillstextentityrecognitionskill"></a>Microsoft.Skills.Text.EntityRecognitionSkill

### <a name="last-available-api-version"></a>Dernière version d’API disponible

Préversion du 30/04/2021

### <a name="end-of-support"></a>Fin de la prise en charge

31 août 2024

### <a name="recommendations"></a>Recommandations 

Utilisez [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) à la place. Il fournit la plupart des fonctionnalités de EntityRecognitionSkill avec une qualité supérieure. Il détient également des informations plus riches dans ses champs de sortie complexes.

Pour migrer vers [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md), vous devez apporter un ou plusieurs des changements suivants à votre définition de compétence. Vous pouvez mettre à jour la définition de compétence à l’aide de l’[API de mise à jour de compétences](/rest/api/searchservice/update-skillset).

1. *(Obligatoire)* Modifiez `@odata.type` en remplaçant `"#Microsoft.Skills.Text.EntityRecognitionSkill"` par `"#Microsoft.Skills.Text.V3.EntityRecognitionSkill"`.

2. *(Facultatif)* Le paramètre `includeTypelessEntities` n’est plus pris en charge, car la nouvelle compétence retourne uniquement les entités ayant des types connus. Ainsi, si votre précédente définition de compétence le référence, vous devez désormais le supprimer.

3. *(Facultatif)* Si vous utilisez la sortie `namedEntities`, cela signifie que quelques changements mineurs ont été apportés aux noms de propriétés.
    1. `value` est renommé `text`
    2. `confidence` est renommé `confidenceScore`

    Si vous devez générer exactement les mêmes noms de propriétés, ajoutez [ShaperSkill](cognitive-search-skill-shaper.md) pour remodéliser la sortie avec les noms nécessaires. Par exemple, ce ShaperSkill renomme les propriétés en utilisant leurs anciennes valeurs.

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "NamedEntitiesShaper",
        "description": "NamedEntitiesShaper",
        "context": "/document/namedEntitiesV3",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/namedEntitiesV3/*",
                "inputs": [
                    {
                        "name": "value",
                        "source": "/document/namedEntitiesV3/*/text"
                    },
                    {
                        "name": "offset",
                        "source": "/document/namedEntitiesV3/*/offset"
                    },
                    {
                        "name": "category",
                        "source": "/document/namedEntitiesV3/*/category"
                    },
                    {
                        "name": "confidence",
                        "source": "/document/namedEntitiesV3/*/confidenceScore"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "namedEntities"
            }
        ]
    }
    ```

4. *(Facultatif)* Si vous utilisez la sortie `entities` pour lier des entités à des entités bien connues, cette fonctionnalité est désormais une nouvelle compétence, [Microsoft.Skills.Text.V3.EntityLinkingSkill](cognitive-search-skill-entity-linking-v3.md). Ajoutez la compétence de liaison d’entités à votre ensemble de compétences pour générer les entités liées. Quelques changements mineurs ont également été apportés aux noms de propriétés de la sortie `entities` entre `EntityRecognitionSkill` et le nouveau `EntityLinkingSkill`.
    1. `wikipediaId` est renommé `id`
    2. `wikipediaLanguage` est renommé `language`
    3. `wikipediaUrl` est renommé `url`
    4. Les propriétés `type` et `subtype` ne sont plus retournées.

    Si vous devez générer exactement les mêmes noms de propriétés, ajoutez [ShaperSkill](cognitive-search-skill-shaper.md) pour remodéliser la sortie avec les noms nécessaires. Par exemple, ce ShaperSkill renomme les propriétés en utilisant leurs anciennes valeurs.

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "LinkedEntitiesShaper",
        "description": "LinkedEntitiesShaper",
        "context": "/document/linkedEntitiesV3",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/linkedEntitiesV3/*",
                "inputs": [
                    {
                        "name": "name",
                        "source": "/document/linkedEntitiesV3/*/name"
                    },
                    {
                        "name": "wikipediaId",
                        "source": "/document/linkedEntitiesV3/*/id"
                    },
                    {
                        "name": "wikipediaLanguage",
                        "source": "/document/linkedEntitiesV3/*/language"
                    },
                    {
                        "name": "wikipediaUrl",
                        "source": "/document/linkedEntitiesV3/*/url"
                    },
                    {
                        "name": "bingId",
                        "source": "/document/linkedEntitiesV3/*/bingId"
                    },
                    {
                        "name": "matches",
                        "source": "/document/linkedEntitiesV3/*/matches"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "entities"
            }
        ]
    }
    ```

5. *(Facultatif)* Si vous ne spécifiez pas explicitement `categories`, `EntityRecognitionSkill V3` peut retourner un type différent de catégories en plus de celles qui étaient prises en charge par `EntityRecognitionSkill`. Si ce comportement est indésirable, veillez à définir explicitement le paramètre `categories` sur `["Person", "Location", "Organization", "Quantity", "Datetime", "URL", "Email"]`.

    _Exemples de définitions de migration_

    * Migration simple

        _(Avant) Définition de compétence EntityRecognition_

        ```json
        {   
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```

        _(Après) Définition de compétence EntityRecognition V3_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```
    
    * Migration complexe

        _(Avant) Définition de compétence EntityRecognition_
    
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "includeTypelessEntities": true,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities",
                    "targetName": "namedEntities"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        }
        ```
    
        _(Après) Définition de compétence EntityRecognition V3_
    
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities",
                    "targetName": "namedEntitiesV3"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "NamedEntitiesShaper",
            "description": "NamedEntitiesShaper",
            "context": "/document/namedEntitiesV3",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/namedEntitiesV3/*",
                    "inputs": [
                        {
                            "name": "value",
                            "source": "/document/namedEntitiesV3/*/text"
                        },
                        {
                            "name": "offset",
                            "source": "/document/namedEntitiesV3/*/offset"
                        },
                        {
                            "name": "category",
                            "source": "/document/namedEntitiesV3/*/category"
                        },
                        {
                            "name": "confidence",
                            "source": "/document/namedEntitiesV3/*/confidenceScore"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "namedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityLinkingSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "entities",
                    "targetName": "linkedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "LinkedEntitiesShaper",
            "description": "LinkedEntitiesShaper",
            "context": "/document/linkedEntitiesV3",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/linkedEntitiesV3/*",
                    "inputs": [
                        {
                            "name": "name",
                            "source": "/document/linkedEntitiesV3/*/name"
                        },
                        {
                            "name": "wikipediaId",
                            "source": "/document/linkedEntitiesV3/*/id"
                        },
                        {
                            "name": "wikipediaLanguage",
                            "source": "/document/linkedEntitiesV3/*/language"
                        },
                        {
                            "name": "wikipediaUrl",
                            "source": "/document/linkedEntitiesV3/*/url"
                        },
                        {
                            "name": "bingId",
                            "source": "/document/linkedEntitiesV3/*/bingId"
                        },
                        {
                            "name": "matches",
                            "source": "/document/linkedEntitiesV3/*/matches"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "entities"
                }
            ]
        }
        ```

## <a name="microsoftskillstextsentimentskill"></a>Microsoft.Skills.Text.SentimentSkill

### <a name="last-available-api-version"></a>Dernière version d’API disponible

Préversion du 30/04/2021

### <a name="end-of-support"></a>Fin de la prise en charge

31 août 2024

### <a name="recommendations"></a>Recommandations 

Utilisez [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md) à la place. Il fournit un modèle amélioré et permet d’ajouter l’exploration des opinions, ou l’analyse des sentiments basés sur l’aspect. Dans la mesure où la compétence est nettement plus complexe, les sorties sont également très différentes.

Pour migrer vers [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md), vous devez apporter un ou plusieurs des changements suivants à votre définition de compétence. Vous pouvez mettre à jour la définition de compétence à l’aide de l’[API de mise à jour de compétences](/rest/api/searchservice/update-skillset).

> [!NOTE]
> Les sorties de compétence pour la compétence Sentiment V3 ne sont pas compatibles avec la définition d’index basée sur SentimentSkill. Vous devez apporter des changements à la définition d’index, à l’ensemble de compétences (entrées de compétence et/ou projections de base de connaissances) ainsi qu’aux mappages de champs de sortie de l’indexeur pour remplacer la compétence de sentiment par la nouvelle version.

1. *(Obligatoire)* Modifiez `@odata.type` en remplaçant `"#Microsoft.Skills.Text.SentimentSkill"` par `"#Microsoft.Skills.Text.V3.SentimentSkill"`.

2. *(Obligatoire)* La compétence Sentiment V3 fournit un score `positive`, `neutral` et `negative` pour l’ensemble du texte ainsi que pour chaque phrase de l’ensemble du texte, alors que la précédente compétence SentimentSkill fournissait uniquement une seule valeur double comprise entre 0,0 (négatif) et 1,0 (positif) pour l’ensemble du texte. Vous devez mettre à jour votre définition d’index pour accepter les trois valeurs doubles à la place d’un seul score. Vous devez également vérifier que les entrées de compétence en aval, les projections de base de connaissances et les mappages de champs de sortie sont cohérents par rapport aux changements de noms.

Il est recommandé de remplacer complètement l’ancien SentimentSkill par SentimentSkill V3, de mettre à jour les entrées de compétence en aval, les projections de base de connaissances, les mappages de champs de sortie de l’indexeur ainsi que la définition d’index pour correspondre au nouveau format de sortie, puis de réinitialiser l’indexeur pour que tous vos documents aient des résultats d’analyse de sentiments cohérents à l’avenir.

> [!NOTE]
> Si vous avez besoin d’aide supplémentaire pour mettre à jour votre pipeline d’enrichissement afin d’utiliser la dernière version de la compétence Sentiment, ou si la réinitialisation de l’indexeur n’est pas une option pour vous, ouvrez une [nouvelle demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) pour que nous puissions travailler directement avec vous.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Dernière version d’API disponible

2017-11-11-Preview

### <a name="end-of-support"></a>Fin de la prise en charge

31 août 2024

### <a name="recommendations"></a>Recommandations 

Utilisez [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) à la place. Il fournit la plupart des fonctionnalités de NamedEntityRecognitionSkill à un meilleur niveau de qualité. Il détient également des informations plus riches dans ses champs de sortie complexes.

Pour migrer vers [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md), vous devez apporter un ou plusieurs des changements suivants à votre définition de compétence. Vous pouvez mettre à jour la définition de compétence à l’aide de l’[API de mise à jour de compétences](/rest/api/searchservice/update-skillset).

1. *(Obligatoire)* Modifiez `@odata.type` en remplaçant `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` par `"#Microsoft.Skills.Text.V3.EntityRecognitionSkill"`.

2. *(Facultatif)* Si vous utilisez la sortie `entities`, utilisez la sortie de collection complexe `namedEntities` issue de `EntityRecognitionSkill V3` à la place. Quelques changements mineurs ont été apportés aux noms de propriétés de la nouvelle sortie complexe de `namedEntities` :
    1. `value` est renommé `text`
    2. `confidence` est renommé `confidenceScore`
    
    Si vous devez générer exactement les mêmes noms de propriétés, ajoutez [ShaperSkill](cognitive-search-skill-shaper.md) pour remodéliser la sortie avec les noms nécessaires. Par exemple, ce ShaperSkill renomme les propriétés en utilisant leurs anciennes valeurs.

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "NamedEntitiesShaper",
        "description": "NamedEntitiesShaper",
        "context": "/document/namedEntities",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/namedEntities/*",
                "inputs": [
                    {
                        "name": "value",
                        "source": "/document/namedEntities/*/text"
                    },
                    {
                        "name": "offset",
                        "source": "/document/namedEntities/*/offset"
                    },
                    {
                        "name": "category",
                        "source": "/document/namedEntities/*/category"
                    },
                    {
                        "name": "confidence",
                        "source": "/document/namedEntities/*/confidenceScore"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "entities"
            }
        ]
    }
    ```

3. *(Facultatif)* Si vous ne spécifiez pas explicitement `categories`, `EntityRecognitionSkill V3` peut retourner un type différent de catégories en plus de celles qui étaient prises en charge par `NamedEntityRecognitionSkill`. Si ce comportement est indésirable, veillez à définir explicitement le paramètre `categories` sur `["Person", "Location", "Organization"]`.

    _Exemples de définitions de migration_

    * Migration simple

        _(Avant) Définition de compétence NamedEntityRecognition_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```

        _(Après) Définition de compétence EntityRecognition V3_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```
    
    * Migration légèrement compliquée

        _(Avant) Définition de compétence NamedEntityRecognition_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "entities"
                }
            ]
        }
        ```

        _(Après) Définition de compétence EntityRecognition V3_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "NamedEntitiesShaper",
            "description": "NamedEntitiesShaper",
            "context": "/document/namedEntities",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/namedEntities/*",
                    "inputs": [
                        {
                            "name": "value",
                            "source": "/document/namedEntities/*/text"
                        },
                        {
                            "name": "offset",
                            "source": "/document/namedEntities/*/offset"
                        },
                        {
                            "name": "category",
                            "source": "/document/namedEntities/*/category"
                        },
                        {
                            "name": "confidence",
                            "source": "/document/namedEntities/*/confidenceScore"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "entities"
                }
            ]
        }
        ```

## <a name="see-also"></a>Voir aussi

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Compétence Reconnaissance d’entité (V3)](cognitive-search-skill-entity-recognition-v3.md)
+ [Compétence Sentiment (V3)](cognitive-search-skill-sentiment-v3.md)