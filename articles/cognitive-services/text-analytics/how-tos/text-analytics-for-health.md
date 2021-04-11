---
title: Comment utiliser l’Analyse de texte pour la santé
titleSuffix: Azure Cognitive Services
description: Découvrez comment extraire et étiqueter des informations médicales de texte clinique non structuré avec l’Analyse de texte pour la santé.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 80a943d235783852f57832363b5af8048f010575
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599428"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Procédure : Utiliser l’Analyse de texte pour la santé (préversion)

> [!IMPORTANT] 
> L’Analyse de texte pour l’intégrité est une fonctionnalité en version préliminaire fournie « en l’état » et « avec toutes les erreurs ». Par conséquent, **l’Analyse de texte pour l’intégrité (préversion) ne doit pas être implémentée ou déployée dans le cadre d’une utilisation en production.** L’Analyse de texte pour l’intégrité n’est pas destinée à être utilisée en tant que dispositif médical, support clinique, outil de diagnostic ou autre technologie destinée à être utilisée dans le diagnostic, la guérison, l’atténuation, le traitement ou la prévention de maladies ou d’autres conditions, et aucune licence ou droit n’est accordé par Microsoft pour utiliser cette fonctionnalité à ces fins. Cette fonctionnalité n’est pas conçue ou destinée à être mise en œuvre ou déployée en remplacement de conseils médicaux professionnels ou d’avis de santé, de diagnostic, de traitement ou de jugement clinique d’un professionnel de la santé, et ne doit pas être utilisé en tant que tel. Le client est seul responsable de l’utilisation de l’Analyse de texte pour l’intégrité. Microsoft ne garantit pas que l’Analyse de texte pour l’intégrité ou les ressources fournies dans le cadre de la fonctionnalité sont suffisantes pour des raisons médicales ou répondent aux exigences médicales de toute personne. 


Analyse de texte pour la santé est une fonctionnalité du service de l’API Analyse de texte qui extrait et étiquette des informations médicales pertinentes à partir de textes non structurés, tels que les notes du médecin, les bilans de sortie d’hospitalisation, les documents cliniques et les dossiers médicaux électroniques.  Il existe deux façons d’utiliser ce service : 

* [API web (asynchrone)](#structure-the-api-request-for-the-hosted-asynchronous-web-api)
* [Conteneur Docker (synchrone)](#hosted-asynchronous-web-api-response)   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>Fonctionnalités

Analyse de texte pour la santé effectue une reconnaissance d’entité nommée (NER), une extraction de relations, une négation d’entité et une liaison d’entité sur du texte en anglais pour découvrir des insights dans du texte clinique et biomédical non structuré.

### <a name="named-entity-recognition"></a>[Reconnaissance d’entité nommée](#tab/ner)

La Reconnaissance d’entité nommée détecte les mots et les expressions mentionnés dans du texte non structuré qui peuvent être associés à un ou plusieurs types sémantiques, tels que le diagnostic, le nom de médicaments, le symptôme/le signe ou l’âge.

> [!div class="mx-imgBorder"]
> ![NER d’intégrité](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[Extraction de relations](#tab/relation-extraction)

L’extraction de relations identifie les connexions pertinentes entre les concepts mentionnés dans le texte. Par exemple, une relation « temps de la condition » est trouvée en associant un nom de condition à un temps ou entre une abréviation et la description complète.  

> [!div class="mx-imgBorder"]
> ![RE d’intégrité](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Entity Linking](#tab/entity-linking)

La liaison d’entités distingue des entités distinctes en associant des entités nommées mentionnées dans du texte à des concepts trouvés dans une base de données de concepts prédéfinie, dont le système de langage médical unifié (UMLS). Les concepts médicaux reçoivent également un nom préféré comme forme de normalisation supplémentaire.

> [!div class="mx-imgBorder"]
> ![EL d’intégrité](../media/ta-for-health/health-entity-linking.png)

L’Analyse de texte pour la santé prend en charge la liaison avec le vocabulaire médical et biomédical figurant dans la source de connaissances Metathesaurus de l’[UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html) (Unified Medical Language System).

### <a name="assertion-detection"></a>[Détection d’assertion](#tab/assertion-detection) 

La signification du contenu médical est fortement affectée par les modificateurs, tels que la négation et les assertions conditionnelles, qui peuvent avoir une incidence critique en cas de mauvaise interprétation. L’analyse de texte pour la santé prend en charge trois catégories de détection d’assertion pour les entités dans le texte : 

* certitude
* conditionnelles
* association

> [!div class="mx-imgBorder"]
> ![NEG d’intégrité](../media/ta-for-health/assertions.png)

---

Consultez les [catégories d’entité](../named-entity-types.md?tabs=health) retournées par l’Analyse de texte pour la santé pour obtenir un liste complète des entités prises en charge. Pour plus d’informations sur les scores de confiance, consultez la [note de transparence relative à Analyse de texte](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context). 

### <a name="supported-languages-and-regions"></a>Langues et régions prises en charge

L’Analyse de texte pour la santé prend uniquement en charge les documents en langue anglaise. 

L’API web hébergée Analyse de texte pour la santé est actuellement disponible uniquement dans les régions suivantes : USA Ouest 2, USA Est 2, USA Centre, Europe Nord et Europe Ouest.

## <a name="request-access-to-the-public-preview"></a>Demander accès à la préversion publique

Renseignez et envoyez le [formulaire de demande Cognitive Services](https://aka.ms/csgate) pour demander l’accès à la préversion publique d’Analyse de texte pour la santé. Vous ne serez pas facturé pour l’utilisation de l’Analyse de texte pour la santé. 

Le formulaire demande des informations sur vous, votre entreprise et le scénario d’utilisateur pour lequel vous allez utiliser le conteneur. Une fois le formulaire envoyé, l’équipe Azure Cognitive Services Azure l’examinera et vous informera de sa décision par courrier électronique.

> [!IMPORTANT]
> * Dans le formulaire, vous devez utiliser une adresse e-mail associée à un ID d’abonnement Azure.
> * La ressource Azure que vous utilisez doit avoir été créée avec l’ID d’abonnement Azure approuvé. 
> * Vérifiez votre adresse e-mail (boîtes de réception et dossiers de courrier indésirable) pour obtenir des mises à jour sur l’état de votre application auprès de Microsoft.

## <a name="using-the-docker-container"></a>Utilisation du conteneur Docker 

Pour exécuter le conteneur Analyse de texte pour la santé dans votre propre environnement, suivez ces [instructions pour télécharger et installer le conteneur](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare).

## <a name="using-the-client-library"></a>Utilisation de la bibliothèque de client

La dernière préversion de la bibliothèque de client Analyse de texte vous permet d’appeler Analyse de texte pour la santé en utilisant un objet client. Reportez-vous à la documentation de référence et consultez les exemples sur GitHub :
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>Envoie d’une requête d’API REST 

### <a name="preparation"></a>Préparation

La qualité des résultats de la fonctionnalité Analyse de texte pour la santé est d’autant meilleure que vous lui donnez de petites quantités de texte à analyser. C’est l’inverse de certaines autres fonctionnalités Analyse de texte comme l’extraction d’expressions clés qui donne de meilleurs résultats avec des blocs de texte plus volumineux. Pour obtenir des résultats optimaux pour ces opérations, envisagez de restructurer les entrées en conséquence.

Vous devez disposer des documents JSON dans ce format : ID, texte et langue. 

La taille du document doit être inférieure à 5 120 caractères par document. Pour connaître le nombre maximal de documents autorisés dans une collection, consultez l’article [Limites de données](../concepts/data-limits.md?tabs=version-3) sous Concepts. La collection est soumise dans le corps de la demande.

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>Structurer la requête d’API pour l’API web asynchrone hébergée

Pour le conteneur et l’API web hébergée, vous devez créer une requête POST. Vous pouvez [utiliser Postman](text-analytics-how-to-call-api.md), une commande cURL ou la **console de test d’API** indiquée dans les [informations de référence sur l’API hébergée Analyse de texte pour la santé](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) pour créer et envoyer rapidement une requête POST à l’API web hébergée dans la région de votre choix. 

> [!NOTE]
> Les points de terminaison asynchrones `/analyze` et `/health` sont uniquement disponibles dans les régions suivantes : USA Ouest 2, USA Est 2, USA Centre, Europe Nord et Europe Ouest.  Pour que ces points de terminaison acceptent les requêtes, vérifiez que votre ressource est créée dans l’une de ces régions.

Voici un exemple de fichier JSON joint au corps POST de la requête d’API Analyse de texte pour la santé :

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>Réponse de l’API web asynchrone hébergée 

Étant donné que cette requête POST est utilisée pour envoyer un travail pour l’opération asynchrone, il n’y a pas de texte dans l’objet de la réponse.  Toutefois, vous avez besoin de la valeur operation-location KEY dans les en-têtes de réponse pour effectuer une requête GET afin de vérifier l’état du travail et de la sortie.  Voici un exemple de valeur operation-location KEY dans l’en-tête de réponse de la requête POST :

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/health/jobs/<jobID>`

Pour vérifier l’état du travail, faites une requête GET à l’URL dans la valeur de l’en-tête operation-location KEY de la réponse POST.  Les états suivants sont utilisés pour refléter l’état d’un travail : `NotStarted`, `running`, `succeeded`, `failed`, `rejected`, `cancelling` et `cancelled`.  

Vous pouvez annuler un travail dont l’état est `NotStarted` ou `running` avec un appel DELETE HTTP à la même URL que la requête GET.  Pour plus d’informations sur l’appel DELETE, consultez les [informations de référence sur l’API hébergée Analyse de texte pour la santé](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob).

Voici un exemple de réponse d’une requête GET.  La sortie est disponible et récupérable jusqu’à ce que le délai `expirationDateTime` (24 heures à partir de l’heure de création du travail) soit écoulé, après quoi la sortie est purgée.

```json
{
    "jobId": "be437134-a76b-4e45-829e-9b37dcd209bf",
    "lastUpdateDateTime": "2021-03-11T05:43:37Z",
    "createdDateTime": "2021-03-11T05:42:32Z",
    "expirationDateTime": "2021-03-12T05:42:32Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "name": "remdesivir",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "DRUGBANK",
                                "id": "DB14761"
                            },
                            {
                                "dataSource": "GS",
                                "id": "6192"
                            },
                            {
                                "dataSource": "MEDCIN",
                                "id": "398132"
                            },
                            {
                                "dataSource": "MMSL",
                                "id": "d09540"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "MTHSPL",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NDDF",
                                "id": "018308"
                            },
                            {
                                "dataSource": "RXNORM",
                                "id": "2284718"
                            },
                            {
                                "dataSource": "SNOMEDCT_US",
                                "id": "870592005"
                            },
                            {
                                "dataSource": "VANDF",
                                "id": "4039395"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.94
                    }
                ],
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
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/3",
                                "role": "Time"
                            }
                        ]
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-03-01"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>Structurer la requête d’API pour le conteneur

Vous pouvez [utiliser Postman](text-analytics-how-to-call-api.md) ou l’exemple de requête cURL ci-dessous pour envoyer une requête au conteneur que vous avez déployé, en remplaçant la variable `serverURL` par la valeur appropriée.  Notez que la version de l’API dans l’URL du conteneur est différente de celle de l’API hébergée.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Le code JSON suivant est un exemple de fichier JSON joint au corps POST de la requête API d’Analyse de texte pour la santé :

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>Corps de la réponse du conteneur

Le code JSON suivant est un exemple de corps de la réponse de l’API Analyse de texte pour la santé à l’issue de l’appel synchrone conteneurisé :

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "name": "remdesivir",
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "DRUGBANK",
                            "id": "DB14761"
                        },
                        {
                            "dataSource": "GS",
                            "id": "6192"
                        },
                        {
                            "dataSource": "MEDCIN",
                            "id": "398132"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "d09540"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "MTHSPL",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NDDF",
                            "id": "018308"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "2284718"
                        },
                        {
                            "dataSource": "SNOMEDCT_US",
                            "id": "870592005"
                        },
                        {
                            "dataSource": "VANDF",
                            "id": "4039395"
                        }
                    ]
                },
                {
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.94
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/0",
                            "role": "Dosage"
                        },
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        }
                    ]
                },
                {
                    "relationType": "RouteOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/2",
                            "role": "Route"
                        }
                    ]
                },
                {
                    "relationType": "TimeOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/3",
                            "role": "Time"
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-03-01"
}
```

### <a name="assertion-output"></a>Sortie d’assertion

L’analyse de texte pour la santé retourne des modificateurs d’assertion, qui sont des attributs informatifs affectés à des concepts médicaux qui fournissent une compréhension plus approfondie du contexte des concepts dans le texte. Ces modificateurs sont divisés en trois catégories, chacune se concentrant sur un aspect différent et contenant un ensemble de valeurs s’excluant mutuellement. Une seule valeur par catégorie est attribuée à chaque entité. La valeur par défaut est la valeur la plus courante pour chaque catégorie. La réponse de sortie du service contient uniquement des modificateurs d’assertion qui sont différents de la valeur par défaut.

**CERTAINTY** : fournit des informations sur la présence (présent ou absent) du concept et du niveau de certitude du texte quant à sa présence (certain ou possible).
*   **Positive** [par défaut] : le concept existe ou est survenu.
* **Negative** : le concept n’existe pas maintenant ou n’a jamais eu lieu.
* **Positive_Possible** : le concept existe probablement, mais il y a une certaine incertitude.
* **Negative_Possible** : l’existence du concept est peu probable, mais il y a une certaine incertitude.
* **Neutral_Possible** : le concept peut exister ou non sans une tendance claire dans l’une ou l’autre direction.

**CONDITIONALITY** : fournit des informations indiquant si l’existence d’un concept dépend de certaines conditions. 
*   **None** [par défaut] : le concept est factuel et non hypothétique, et ne dépend pas de certaines conditions.
*   **Hypothetical** : le concept peut se développer ou se produire à l’avenir.
*   **Conditional** : le concept existe ou se produit uniquement sous certaines conditions.

**ASSOCIATION** : indique si le concept est associé à l’objet du texte ou à une autre personne.
*   **Subject** [par défaut] : le concept est associé à l’objet du texte, généralement le patient.
*   **Someone_Else**: le concept est associé à une personne qui n’est pas l’objet du texte.


La détection d’assertion représente des entités inversées comme une valeur négative pour la catégorie de certitude, par exemple :

```json
{
                        "offset": 381,
                        "length": 3,
                        "text": "SOB",
                        "category": "SymptomOrSign",
                        "confidenceScore": 0.98,
                        "assertion": {
                            "certainty&quot;: &quot;negative"
                        },
                        "name": "Dyspnea",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id&quot;: &quot;C0013404"
                            },
                            {
                                "dataSource": "AOD",
                                "id&quot;: &quot;0000005442"
                            },
    ...
```

### <a name="relation-extraction-output"></a>Sortie d’extraction de relations

L’analyse de texte pour la santé reconnaît les relations entre les différents concepts, y compris les relations entre l’attribut et l’entité (par exemple, la direction de la structure du corps, la posologie du médicament) et entre les entités (par exemple, la détection des abréviations).

**ABBREVIATION**

**DIRECTION_OF_BODY_STRUCTURE**

**DIRECTION_OF_CONDITION**

**DIRECTION_OF_EXAMINATION**

**DIRECTION_OF_TREATMENT**

**DOSAGE_OF_MEDICATION**

**FORM_OF_MEDICATION**

**FREQUENCY_OF_MEDICATION**

**FREQUENCY_OF_TREATMENT**

**QUALIFIER_OF_CONDITION**

**RELATION_OF_EXAMINATION**

**ROUTE_OF_MEDICATION** 

**TIME_OF_CONDITION**

**TIME_OF_EVENT**

**TIME_OF_EXAMINATION**

**TIME_OF_MEDICATION**

**TIME_OF_TREATMENT**

**UNIT_OF_CONDITION**

**UNIT_OF_EXAMINATION**

**VALUE_OF_CONDITION**  

**VALUE_OF_EXAMINATION**

> [!NOTE]
> * Les relations faisant référence à la CONDITION peuvent faire référence au type d’entité DIAGNOSIS ou SYMPTOM_OR_SIGN.
> * Les relations faisant référence à un médicament peuvent faire référence au type d’entité MEDICATION_NAME ou au MEDICATION_CLASS.
> * Les relations qui font référence au temps peuvent faire référence au type d’entité TIME ou DATE.

La sortie de l’extraction des relations contient les références URI et les rôles attribués des entités du type de relation. Par exemple :

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

## <a name="see-also"></a>Voir aussi

* [Vue d’ensemble d’Analyse de texte](../overview.md)
* [Catégories d’entité nommée](../named-entity-types.md)
* [Nouveautés](../whats-new.md)
