---
title: Définir des projections dans une base de connaissances
titleSuffix: Azure Cognitive Search
description: Exemples de modèles courants montrant comment projeter des documents enrichis dans la base de connaissances pour les utiliser avec Power BI ou Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: f29f4b91b85c0027df4be2fd5f26ef8f9749fe33
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163818"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments-to-the-knowledge-store"></a>Projections dans une base de connaissances : guide pratique pour mettre en forme et exporter des enrichissements dans la base de connaissances

> [!IMPORTANT] 
> La base de connaissances est actuellement disponible en préversion publique. Les fonctionnalités en préversion sont fournies sans contrat de niveau de service et ne sont pas recommandées pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). L’[API REST version 2019-05-06-Preview](search-api-preview.md) fournit des fonctionnalités en préversion. La prise en charge du portail est actuellement limitée, et il n’existe pas de prise en charge du kit SDK .NET.

Les projections sont l’expression physique de documents enrichis dans une base de connaissances. L’utilisation efficace de vos documents enrichis nécessite une structure. Dans cet article, vous allez explorer la structure et les relations, apprendre à créer des propriétés de projection ainsi qu’à lier des données à différents types de projection que vous créez. 

Pour créer une projection, vous devez mettre en forme les données à l’aide d’une compétence Modélisateur afin de créer un objet personnalisé ou utiliser la syntaxe de mise en forme inline. Une forme de données contient toutes les données que vous envisagez de projeter. Ce document vous donne un exemple de chaque option ; vous pouvez choisir d’utiliser l’une ou l’autre des options pour les projections que vous créez.


Il existe trois types de projections :
+ Tables
+ Objets
+ Fichiers

Les projections de table sont stockées dans le stockage Table Azure. Les projections d’objet et de fichier sont écrites dans le stockage d’objets blob, tandis que les projections d’objet sont enregistrées en tant que fichiers JSON et peuvent contenir le contenu du document ainsi que les sorties ou enrichissements de compétence. Le pipeline d’enrichissement peut également extraire des binaires tels que des images ; ces binaires sont projetés en tant que projections de fichier. Quand un objet binaire est projeté en tant que projection d’objet, seules les métadonnées qui lui sont associées sont enregistrées en tant qu’objet blob JSON. 

Pour comprendre l’intersection entre la mise en forme des données et les projections, nous allons utiliser l’ensemble de compétences suivant comme base pour l’exploration de différentes configurations. Cet ensemble de compétences traite le contenu d’images et de texte brut. Pour les scénarios que nous souhaitons prendre en charge, les projections sont définies à partir du contenu du document et des sorties des compétences.

Vous pouvez également télécharger et utiliser un [exemple d’API REST](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json) avec tous les appels de cette procédure pas à pas.

> [!IMPORTANT] 
> Quand vous expérimentez des projections, il est utile de [définir la propriété de cache de l’indexeur](search-howto-incremental-index.md) pour garantir le contrôle des coûts. La modification des projections entraîne l’enrichissement de la totalité du document si le cache de l’indexeur n’est pas défini. Quand le cache est défini et que seules les projections sont mises à jour, les exécutions de l’ensemble de compétences pour les documents précédemment enrichis n’entraînent pas de frais liés à Cognitive Services.


```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

Nous pouvons maintenant ajouter l’objet `knowledgeStore` et configurer les projections pour chacun des scénarios, si nécessaire. 

## <a name="projecting-to-tables-for-scenarios-like-power-bi"></a>Projection dans des tables pour les scénarios tels que Power BI

> [!NOTE] 
> La base de connaissances étant un compte Stockage Azure, les projections de table sont des tables Stockage Azure et sont régies par les limites de stockage sur les tables. Pour plus d’informations, consultez [Limites de stockage de table](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). Il est utile de savoir que la taille maximale respective d’une entité et d’une propriété est de 1 Mo et 64 Ko. Ces contraintes font des tables une bonne solution pour stocker un grand nombre de petites entités.

Power BI peut lire à partir de tables et découvrir des relations basées sur les clés créées par les projections de la base de connaissances ; les tables constituent ainsi une bonne option pour projeter des données quand vous essayez de créer un tableau de bord sur vos données enrichies. En supposant que nous essayons de créer un tableau de bord dans lequel nous pouvons visualiser les expressions clés extraites de documents telles qu’un nuage de mots, nous pouvons ajouter une compétence Modélisateur à l’ensemble de compétences pour créer une forme personnalisée qui contient les expressions clés et les détails propres au document. Ajoutez la compétence Modélisateur à l’ensemble de compétences pour créer un enrichissement appelé ```pbiShape``` sur le ```document```.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Utilisation d’une compétence Modélisateur pour créer une forme personnalisée

Créez une forme personnalisée que vous pouvez projeter dans le stockage de table. Sans forme personnalisée, une projection ne peut référencer qu’un seul nœud (une projection par sortie). La création d’une forme personnalisée vous permet de regrouper différents éléments en un nouvel ensemble logique qui peut être projeté sous la forme d’une table unique, ou découpé et distribué dans une collection de tables. Dans cet exemple, la forme personnalisée combine les métadonnées et les entités et expressions clés identifiées. L’objet est appelé pbiShape et est apparenté à `/document`. 

> [!IMPORTANT] 
> Il est nécessaire que les chemins source pour les enrichissements soient des objets JSON bien formés pour pouvoir être projetés. L’arborescence des enrichissements peut représenter des enrichissements qui ne sont pas des objets JSON bien formés, par exemple quand un enrichissement est apparenté à une primitive telle qu’une chaîne. Comme vous pouvez le constater, `KeyPhrases` et `Entities` sont wrappés dans un objet JSON valide avec le `sourceContext` ; cela est nécessaire, car `keyphrases` et `entities` sont des enrichissements sur les primitives et doivent être convertis en JSON valide avant de pouvoir être projetés.

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
                    "targetName": "pbiShape"
                }
            ]
        }
```
Ajoutez la compétence Modélisateur que nous venons de définir à la liste des compétences dans l’ensemble de compétences. 

Toutes les données nécessaires à la projection dans des tables étant désormais disponibles, mettez à jour l’objet knowledgeStore avec les définitions des tables. 

```json

"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

Définissez la propriété ```storageConnectionString``` sur une chaîne de connexion de compte de stockage v2 universel valide. Dans ce scénario, nous définissons trois tables dans l’objet de projection en définissant les propriétés ```tableName```, ```source``` et ```generatedKeyName```. Vous pouvez maintenant mettre à jour l’ensemble de compétences en émettant la demande PUT.

### <a name="slicing"></a>Découpage 

Quand vous commencez avec une forme consolidée dont tout le contenu à projeter se trouve dans une seule forme (ou nœud d’enrichissement), le découpage vous permet de découper un seul nœud en plusieurs tables ou objets. Ici, l’objet ```pbiShape``` est découpé en plusieurs tables. La fonctionnalité de découpage vous permet d’extraire des parties de la forme, ```keyPhrases``` et ```Entities``` en tables distinctes. Cela est utile quand plusieurs entités et expressions clés sont associées à chaque document. Le découpage génère implicitement une relation entre les tables parent et enfant, en utilisant le ```generatedKeyName``` dans la table parent pour créer une colonne portant le même nom dans la table enfant. 

### <a name="naming-relationships"></a>Nommage des relations
Les propriétés ```generatedKeyName``` et ```referenceKeyName``` sont utilisées pour lier des données entre des tables ou même entre des types de projection. Chaque ligne de la table enfant/projection a une propriété qui pointe vers le parent. Le nom de la colonne ou de la propriété dans l’enfant est le ```referenceKeyName``` du parent. Quand le ```referenceKeyName``` n’est pas fourni, le service le définit par défaut sur le ```generatedKeyName``` du parent. PowerBI s’appuie sur ces clés générées pour découvrir les relations dans les tables. S’il est nécessaire que la colonne de la table enfant soit nommée différemment, définissez la propriété ```referenceKeyName``` sur la table . Par exemple, vous pouvez définir le ```generatedKeyName``` en tant qu’ID sur la table pbiDocument et le ```referenceKeyName``` en tant que DocumentID. Ainsi, la colonne dans les tables pbiEntities et pbiKeyPhrases contenant l’ID de document seraient nommées DocumentID.

Enregistrez l’ensemble de compétences mis à jour et exécutez l’indexeur ; vous disposez maintenant d’une projection de travail opérationnelle avec trois tables. Quand vous importez ces tables dans Power BI, celui-ci doit être en mesure de détecter les relations automatiquement.

## <a name="projecting-to-objects"></a>Projection dans des objets

Les projections d’objet n’ont pas les mêmes limitations que les projections de table ; elles sont mieux adaptées à la projection de grands documents. Dans cet exemple, nous projetons la totalité du document dans une projection d’objet. Les projections d’objet sont limitées à une seule projection dans un conteneur.
Pour définir une projection d’objet, nous allons utiliser le tableau ```objects``` dans les projections. Vous pouvez générer une nouvelle forme à l’aide de la compétence Modélisateur ou utiliser la mise en forme inline de la projection d’objet. Tandis que l’exemple de tables illustre l’approche de la création d’une forme et du découpage, cet exemple illustre l’utilisation de la mise en forme inline. La mise en forme inline vous permet de créer une forme dans la définition des entrées d’une projection. La mise en forme inline crée un objet anonyme identique à ce qu’un modélisateur similaire produirait. La mise en forme inline est utile si vous définissez une forme que vous n’envisagez pas de réutiliser.
La propriété projections est un tableau ; dans cet exemple, nous ajoutons une nouvelle instance de projection au tableau. Mettez à jour la définition de knowledgeStore avec les projections définies inline ; vous n’avez pas besoin de compétence Modélisateur quand vous utilisez des projections en ligne.

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
## <a name="file-projections"></a>Projections de fichier

Les projections de fichier sont des images extraites du document source ou des sorties d’enrichissements qui peuvent être projetées à partir du processus d’enrichissement. Les projections de fichier, similaires aux projections d’objet, sont implémentées en tant qu’objets blob et contiennent l’image. Pour générer une projection de fichier, nous utilisons le tableau ```files``` dans l’objet de projection. Cet exemple projette toutes les images extraites du document dans un conteneur appelé `samplefile`.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>Projection dans plusieurs types

Un scénario plus complexe peut vous obliger à projeter du contenu dans différents types de projection. Par exemple, si vous devez projeter des données telles que des expressions clés et des entités dans des tables, enregistrez les résultats OCR du texte et du texte de disposition en page sous forme d’objets et projetez les images en tant que fichiers. Cette mise à jour de l’ensemble de compétences effectue les opérations suivantes :

1. Créer une table avec une ligne pour chaque document.
2. Créer une table liée à la table de documents avec chaque expression clé identifiée en tant que ligne dans cette table.
3. Créer une table liée à la table de documents avec chaque entité identifiée en tant que ligne dans cette table.
4. Créer une projection d’objet avec le texte de disposition pour chaque image.
5. Créez une projection de fichier, en projetant chaque image extraite.
6. Créer une table de référence croisée qui contient des références à la table de document, à la projection d’objet avec le texte de disposition et à la projection de fichier.

Commencez par ajouter une nouvelle compétence Modélisateur au tableau de compétences qui crée un objet mis en forme. 

```json
{
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "ShaperForCross",
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

Les projections d’objet nécessitent un nom de conteneur pour chaque projection ; les projections d’objet ou les projections de fichier ne peuvent pas partager de conteneur. 

### <a name="relationships"></a>Relations

Cet exemple met également en évidence une autre fonctionnalité des projections ; la définition de plusieurs types de projections dans le même objet de projection se traduit par l’expression d’une relation dans et entre les différents types (tables, objets, fichiers) de projections. Ainsi, dans la projection d’objet, vous pouvez, à partir d’une ligne de table pour un document, rechercher tout le texte OCR pour les images contenues dans ce document. Si vous ne souhaitez pas que les données soient liées, définissez les projections dans différents objets de projection ; par exemple, l’extrait de code suivant entraîne la liaison des tables, sans qu’aucune relation ne soit toutefois établie entre les tables et les projections de texte OCR. Les groupes de projection sont utiles quand vous souhaitez projeter les mêmes données dans différentes formes en fonction de besoins différents. Par exemple, un groupe de projection pour le tableau de bord Power BI et un autre groupe de projection pour l’utilisation des données en vue de l’entraînement d’un modèle d’intelligence artificielle pour une compétence.
Lors de la création de projections de types différents, les projections de fichier et d’objet sont générées en premier, puis les chemins sont ajoutés aux tables.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
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

Les exemples contenus dans ce document illustrent les modèles courants d’utilisation des projections ; vous devez maintenant avoir une bonne compréhension des concepts relatifs à la création d’une projection dans le cadre de votre propre scénario.

## <a name="common-issues"></a>Problèmes courants

Lors de la définition d’une projection, il existe quelques problèmes courants qui peuvent entraîner des résultats imprévus.

1. Absence de la mise en forme des enrichissements de chaînes. Quand des chaînes sont enrichies, par exemple ```merged_content``` enrichie de phrases clés, la propriété enrichie est représentée sous la forme d’un enfant de merged_content dans l’arborescence des enrichissements. Toutefois, au moment de la projection, une transformation en un objet JSON valide avec un nom et une valeur est nécessaire.
2. Omission de ```/*``` à la fin d’un chemin source. Si, par exemple, la source d’une projection est ```/document/pbiShape/keyPhrases```, le tableau d’expressions clés est projeté sous la forme d’un objet ou d’une ligne unique. La définition du chemin source sur ```/document/pbiShape/keyPhrases/*``` produit une seule ligne ou un seul objet pour chacune des expressions clés.
3. Les sélecteurs de chemin respectent la casse et peuvent entraîner des avertissements d’entrée manquante si vous n’utilisez pas la casse exacte du sélecteur.

