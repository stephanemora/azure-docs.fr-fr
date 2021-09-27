---
title: Définir des projections
titleSuffix: Azure Cognitive Search
description: Créez des projections de tables, d’objets et de fichiers dans une base de connaissances pour enregistrer le contenu enrichi à partir d’un indexeur et d’un pipeline d’enrichissement d’ensembles de compétences.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 6daaf2f3f9521a61307eb8017abce72a47047333
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123537928"
---
# <a name="define-projections-in-a-knowledge-store"></a>Définir des projections dans une base de connaissances

Les [projections](knowledge-store-projection-overview.md) sont l’expression physique de documents enrichis dans une [base de connaissances](knowledge-store-concept-intro.md). Elles prennent la forme de tables, d’objets ou de fichiers dans Stockage Azure. L’utilisation efficace de documents enrichis nécessite une structure. Dans cet article, vous allez explorer la structure et les relations, apprendre à créer des propriétés de projection ainsi qu’à lier des données aux différents types de projection créés.

Les projections sont spécifiées dans une [définition knowledgeStore](knowledge-store-concept-intro.md), une fois que vous avez défini les enrichissements et modélisé les données à l’aide d’une [compétence Modélisateur ou de modélisations inline](knowledge-store-projection-shape.md). Cet article référence un exemple d’ensemble de compétences qui définit les enrichissements, notamment une compétence Modélisateur qui produit une modélisation appropriée pour une projection.

## <a name="enable-caching"></a>Activer la mise en cache

Quand vous développez des projections, [définissez la propriété du cache de l’indexeur](search-howto-incremental-index.md) pour garantir le contrôle des coûts. La modification des projections entraîne l’enrichissement de la totalité du document si le cache de l’indexeur n’est pas défini. Quand le cache est défini et que seules les projections sont mises à jour, les exécutions de l’ensemble de compétences pour les documents précédemment enrichis n’entraînent pas de frais supplémentaires liés à Cognitive Services.

## <a name="example-enrichments"></a>Exemples d’enrichissements

Pour comprendre l’intersection entre les modélisations de données et les projections, consultez l’ensemble de compétences suivant en tant que base d’un contenu enrichi. Cet ensemble de compétences traite à la fois des images brutes et du texte, ce qui produit des sorties qui sont référencées dans des modélisations et des projections.

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset that enriches blob data found in "merged_content". The enrichment granularity is a document.",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
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
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "A Cognitive Services resource in the same region as Search.",
        "key": "<COGNITIVE SERVICES All-in-ONE KEY>"
    },
    "knowledgeStore": null
}
```

## <a name="example-shaper-skill"></a>Exemple de compétence Modélisateur

La compétence Modélisateur est un utilitaire qui permet d’utiliser du contenu enrichi au lieu de le créer. L’ajout d’une compétence Modélisateur à un ensemble de compétences vous permet de créer une modélisation personnalisée que vous pouvez projeter dans le service Stockage Table. Sans modélisation personnalisée, les projections se limitent au référencement d’un seul nœud (une seule projection par sortie), ce qui ne convient pas pour les tables. La création d’une forme personnalisée permet de regrouper différents éléments en un nouvel ensemble logique qui peut être projeté sous la forme d’une table unique, ou découpé et distribué dans une collection de tables. 

Dans cet exemple, la modélisation personnalisée combine les métadonnées d’objet blob ainsi que les entités et phrases clés identifiées. La modélisation personnalisée s’appelle `projectionShape` et est apparenté à `/document`. 

L’une des finalités de la mise en forme est de s’assurer que tous les nœuds d’enrichissement sont exprimés dans un format JSON correct, ce qui est obligatoire pour la projection dans une base de connaissances. Cela est particulièrement vrai quand une arborescence d’enrichissement contient des nœuds qui n’ont pas le bon format JSON (par exemple, si un enrichissement est apparenté à une primitive comme une chaîne).

Notez que les deux derniers nœuds, `KeyPhrases` et `Entities`, sont wrappés dans un objet JSON valide avec le `sourceContext`. Cela est nécessaire, car `keyphrases` et `entities` sont des enrichissements sur les primitives et ils doivent être convertis en JSON valide avant de pouvoir être projetés.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "projectionShape"
        }
    ]
}
```

Ajoutez la compétence Modélisateur à l’ensemble de compétences. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

## <a name="projecting-to-tables"></a>Projection dans des tables

La projection vers des tables dans Stockage Azure permet de créer des rapports et d’effectuer des analyses. Pour ce faire, vous disposez d’outils tels que Power BI, qui peuvent lire les tables et découvrir des relations en fonction des clés générées durant la projection. Si vous essayez de créer un tableau de bord, l’utilisation de tables avec des relations simplifie la tâche.

Le schéma de la table est spécifié en partie par la projection (nom et clé de la table) mais également par la source, qui fournit la modélisation de la table (colonnes).

> [!NOTE] 
> Les projections de table sont des tables du Stockage Azure auxquelles s’appliquent les limites de stockage imposées par le Stockage Azure. Pour plus d’informations, consultez les [limites du stockage de table](/rest/api/storageservices/understanding-the-table-service-data-model). Il est utile de savoir que la taille maximale respective d’une entité et d’une propriété est de 1 Mo et 64 Ko. Ces contraintes font des tables une bonne solution pour stocker un grand nombre de petites entités.

À partir des exemples ci-dessus, nous pouvons voir qu’il existe une quantité connue d’enrichissements et de modélisations de données qui peuvent être référencés dans les projections de tables. Dans la projection de tables ci-dessous, trois tables sont définies à l’aide des propriétés `tableName`, `source` et `generatedKeyName`.

| Propriété | Description |
|----------|-------------|
| tableName | (Obligatoire) Détermine le nom d’une table créée dans Stockage Table Azure. Les tables sont créées avec des colonnes partitionKey et rowKey. |
| source | Chemin d’un nœud dans une arborescence d’enrichissement. Dans la mesure où une projection de table est complexe (plusieurs nœuds remplissent plusieurs colonnes), le chemin doit être résolu en une modélisation de données qui inclut les nœuds. La sortie d’une compétence Modélisateur est la valeur la plus courante pour cette propriété. Toutefois, vous pouvez également créer une modélisation à l’aide de la modélisation inline dans la projection. |
| generatedKeyName | Chaque ligne est identifiée de manière unique par une valeur générée par le système. Cette propriété détermine le nom de la colonne contenant ces valeurs. Si vous omettez cette propriété, une colonne est créée automatiquement avec pour convention de nommage l’association du nom de la table et de la « clé ». |

Ces trois tables sont liées via des clés générées et par le parent partagé `/document/projectionShape`.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "tblDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/projectionShape"
                },
                {
                    "tableName": "tblKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/projectionShape/keyPhrases/*"
                },
                {
                    "tableName": "tblEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/projectionShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

Vous pouvez effectuer votre travail en procédant comme suit :

1. Affectez à la propriété `storageConnectionString` de la base de connaissances une chaîne de connexion de compte de stockage universel V2 valide.  

1. Mettez à jour l’ensemble de compétences en envoyant la requête PUT.

1. Après la mise à jour de l’ensemble de compétences, exécutez l’indexeur. 

Vous disposez maintenant d’une projection de travail avec trois tables. [Quand vous importez ces tables dans Power BI](knowledge-store-connect-power-bi.md), ce dernier doit pouvoir détecter les relations automatiquement.

Avant de passer à l’exemple suivant, revoyons les aspects de la projection de table pour comprendre les mécanismes de découpage et des données associées.

### <a name="slicing-a-table-into-multiple-child-tables"></a>Découpage d’une table en plusieurs tables enfants

Le découpage est une technique qui divise une forme regroupée entière en plusieurs parties constitutives. Vous obtenez des tables distinctes mais liées entre elles, que vous pouvez utiliser individuellement.

Dans l’exemple, `projectionShape` est la forme regroupée (ou nœud d’enrichissement). Dans la définition de la projection, `projectionShape` est découpé en tables supplémentaires, ce qui vous permet d’extraire des parties de la forme, `keyPhrases` et `Entities`. Dans Power BI, cette opération est utile lorsque plusieurs entités et expressions clés sont associées à chaque document : vous obtenez des insights plus complets si vous pouvez visualiser les entités et les expressions clés sous forme de données classifiées par catégorie.

Le découpage génère implicitement une relation entre les tables parent et enfant, en utilisant le `generatedKeyName` dans la table parent pour créer une colonne du même nom dans la table enfant. 

### <a name="naming-relationships"></a>Nommage des relations

Les propriétés `generatedKeyName` et `referenceKeyName` sont utilisées pour lier des données entre des tables ou même entre des types de projection. Chaque ligne de la table enfant a une propriété qui pointe vers le parent. Le nom de la colonne ou de la propriété dans l’enfant est le `referenceKeyName` du parent. Quand le `referenceKeyName` n’est pas fourni, le service le définit par défaut sur le `generatedKeyName` du parent. 

Power BI utilise ces clés générées pour découvrir les relations dans les tables. S’il est nécessaire que la colonne de la table enfant soit nommée différemment, définissez la propriété `referenceKeyName` sur la table . Par exemple, vous pouvez définir `generatedKeyName` en tant qu’ID pour la table tblDocument et `referenceKeyName` en tant que DocumentID. Ainsi, la colonne des tables tblEntities et tblKeyPhrases contenant l’ID de document se nomme DocumentID.

## <a name="projecting-to-objects"></a>Projection dans des objets

Les projections d’objets sont plus simples à définir et sont utilisées pour projeter des documents entiers. Les projections d’objet sont limitées à une seule projection dans un conteneur et elles ne peuvent pas être découpées.

Pour définir une projection d’objet, utilisez le tableau `objects` dans `projections`. Vous pouvez générer une nouvelle forme à l’aide de la compétence Modélisateur ou utiliser la mise en forme inline de la projection d’objet. Tandis que l’exemple de tables illustre l’approche de la création d’une forme et du découpage, cet exemple illustre l’utilisation de la mise en forme inline. 

La mise en forme inline permet de créer une forme dans la définition des entrées d’une projection. La modélisation inline crée un objet anonyme identique à ce que produirait une compétence Modélisateur (dans le cas présent, `projectionShape`). La mise en forme inline est utile si vous définissez une forme que vous n’envisagez pas de réutiliser.

La propriété projections est un tableau. Cet exemple comprend une nouvelle instance de projection au tableau, où la définition knowledgeStore contient des projections inline. Quand vous utilisez des projections inline, vous pouvez omettre la compétence Modélisateur.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
            {
            "tables": [ ],
            "objects": [
                {
                    "storageContainer": "sampleobject",
                    "source": null,
                    "generatedKeyName": "myobject",
                    "sourceContext": "/document",
                    "inputs": [
                        {
                            "name": "metadata_storage_name",
                            "source": "/document/metadata_storage_name"
                        },
                        {
                            "name": "metadata_storage_path",
                            "source": "/document/metadata_storage_path"
                        },
                        {
                            "name": "content",
                            "source": "/document/content"
                        },
                        {
                            "name": "keyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        },
                        {
                            "name": "entities",
                            "source": "/document/merged_content/entities/*/name"
                        },
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        },
                        {
                            "name": "ocrLayoutText",
                            "source": "/document/normalized_images/*/layoutText"
                        }
                    ]

                }
            ],
            "files": []
        }
    ]
}
```

## <a name="projecting-to-file"></a>Projection dans un fichier

Les projections de fichiers sont toujours des images extraites du document source ou des sorties d’enrichissement qui peuvent être projetées à partir du processus d’enrichissement. Les projections de fichier, comme les projections d’objet, sont implémentées sous forme d’objets blob dans le Stockage Azure et contiennent l’image. 

Pour générer une projection de fichier, utilisez le tableau `files` dans l’objet de projection. Cet exemple projette toutes les images extraites du document dans un conteneur appelé `myImages`.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [ ],
            "objects": [ ],
            "files": [
                {
                    "storageContainer": "myImages",
                    "source": "/document/normalized_images/*"
                }
            ]
        }
    ]
}
```

## <a name="projecting-to-multiple-types"></a>Projection dans plusieurs types

Un scénario plus complexe peut vous obliger à projeter du contenu dans différents types de projection. Par exemple, projeter les phrases clés et les entités vers des tables, enregistrer les résultats de l’OCR du texte et du texte de disposition en tant qu’objets, puis projeter les images en tant que fichiers. 

Étapes pour plusieurs types de projection :

1. Créer une table avec une ligne pour chaque document.
1. Créer une table liée à la table de documents avec chaque expression clé identifiée en tant que ligne dans cette table.
1. Créer une table liée à la table de documents avec chaque entité identifiée en tant que ligne dans cette table.
1. Créer une projection d’objet avec le texte de disposition pour chaque image.
1. Créez une projection de fichier, en projetant chaque image extraite.
1. Créer une table de référence croisée qui contient des références à la table de document, à la projection d’objet avec le texte de disposition et à la projection de fichier.

### <a name="shape-data-for-cross-projection"></a>Mettre en forme les données pour la projection croisée

Pour obtenir les formes nécessaires pour ces projections, commencez par ajouter une nouvelle compétence Modélisateur qui crée un objet mis en forme appelé `crossProjection`. 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCrossProjection",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>Définir des projections de table, d’objet et de fichier

À partir de l’objet crossProjection regroupé, découpez l’objet en plusieurs tables, capturez la sortie OCR sous forme d’objets blob, puis enregistrez l’image en tant que fichiers (également dans le Stockage Blob).

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
            {
            "tables": [
                {
                    "tableName": "crossDocument",
                    "generatedKeyName": "Id",
                    "source": "/document/crossProjection"
                },
                {
                    "tableName": "crossEntities",
                    "generatedKeyName": "EntityId",
                    "source": "/document/crossProjection/entities/*"
                },
                {
                    "tableName": "crossKeyPhrases",
                    "generatedKeyName": "KeyPhraseId",
                    "source": "/document/crossProjection/keyPhrases/*"
                },
                {
                    "tableName": "crossReference",
                    "generatedKeyName": "CrossId",
                    "source": "/document/crossProjection/images/*"
                }
                    
            ],
            "objects": [
                {
                    "storageContainer": "crossobject",
                    "generatedKeyName": "crosslayout",
                    "source": null,
                    "sourceContext": "/document/crossProjection/images/*/layoutText",
                    "inputs": [
                        {
                            "name": "OcrLayoutText",
                            "source": "/document/crossProjection/images/*/layoutText"
                        }
                    ]
                }
            ],
            "files": [
                {
                    "storageContainer": "crossimages",
                    "generatedKeyName": "crossimages",
                    "source": "/document/crossProjection/images/*/image"
                }
            ]
        }
    ]
}
```

Les projections d’objets nécessitent un nom de conteneur pour chaque projection. Les projections d’objets et les projections de fichiers ne peuvent pas partager de conteneur. 

### <a name="relationships-among-table-object-and-file-projections"></a>Relations entre les projections de table, d’objet et de fichier

Cet exemple met également en lumière une autre fonctionnalité des projections. En définissant plusieurs types de projections dans le même objet de projection, il existe une relation exprimée dans et entre les différents types (tables, objets, fichiers). Cela vous permet de commencer avec une ligne de tableau pour un document et de rechercher tout le texte OCR pour les images contenues dans ce document dans la projection de l’objet. 

Si vous ne souhaitez pas que les données soient liées, définissez les projections dans des groupes de projections distincts. Par exemple, l’extrait de code suivant entraîne la liaison des tables, sans qu’aucune relation ne soit toutefois établie entre les tables et les projections d’objet (texte OCR). 

Les groupes de projection sont utiles quand vous souhaitez projeter les mêmes données dans différentes formes en fonction de besoins différents. Par exemple, un groupe de projections pour le tableau de bord Power BI et un autre groupe de projections pour la capture des données utilisées pour entraîner un modèle Machine Learning wrappé dans une compétence personnalisée.

Lors de la création de projections de types différents, les projections de fichier et d’objet sont générées en premier, et les chemins sont ajoutés aux tables.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "unrelatedDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/projectionShape"
                },
                {
                    "tableName": "unrelatedKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/projectionShape/keyPhrases"
                }
            ],
            "objects": [
                
            ],
            "files": []
        }, 
        {
            "tables": [],
            "objects": [
                {
                    "storageContainer": "unrelatedocrtext",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*/text",
                    "inputs": [
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        }
                    ]
                },
                {
                    "storageContainer": "unrelatedocrlayout",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*/layoutText",
                    "inputs": [
                        {
                            "name": "ocrLayoutText",
                            "source": "/document/normalized_images/*/layoutText"
                        }
                    ]
                }
            ],
            "files": []
        }
    ]
}
```

## <a name="common-issues"></a>Problèmes courants

Lors de la définition d’une projection, il existe quelques problèmes courants qui peuvent entraîner des résultats imprévus. Vérifiez les points ci-dessous si la sortie dans la base de connaissances ne correspond pas à la sortie prévue.

+ Absence de la mise en forme des enrichissements de chaînes dans un JSON valide. Quand des chaînes sont enrichies, par exemple la chaîne `merged_content` enrichie d’expressions clés, la propriété enrichie est représentée comme enfant de `merged_content` dans l’arborescence des enrichissements. La représentation par défaut n’est pas un JSON correctement mis en forme. Au moment de la projection, vous devez donc transformer l’enrichissement en un objet JSON valide avec un nom et une valeur.

+ Omission de ```/*``` à la fin d’un chemin source. Si la source d’une projection est `/document/projectionShape/keyPhrases`, le tableau d’expressions clés est projeté sous la forme d’un objet ou d’une ligne unique. À la place, définissez le chemin source sur `/document/projectionShape/keyPhrases/*` afin d’obtenir une seule ligne ou un seul objet pour chacune des expressions clés.

+ Erreurs dans la syntaxe du chemin. Les sélecteurs de chemin respectent la casse et peuvent entraîner des avertissements d’entrée manquante si vous n’utilisez pas la casse exacte du sélecteur.

## <a name="next-steps"></a>Étapes suivantes

Les exemples de cet article montrent des modèles courants de création de projections. Maintenant que vous avez bien compris les concepts, vous êtes mieux armé pour créer des projections adaptées à votre scénario particulier.

À mesure que vous explorez de nouvelles fonctionnalités, envisagez l’enrichissement incrémentiel en guise d’étape suivante. L’enrichissement incrémentiel est basé sur la mise en cache, ce qui vous permet de réutiliser tous les enrichissements qui ne sont pas impactés par une modification de l’ensemble de compétences. Cela s’avère particulièrement utile pour les pipelines qui incluent l’OCR et l’analyse des images.

> [!div class="nextstepaction"]
> [Configurer la mise en cache pour l’enrichissement incrémentiel](search-howto-incremental-index.md)
