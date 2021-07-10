---
title: Cartes de visite - Form Recognizer
titleSuffix: Azure Applied AI Services
description: Découvrez les concepts liés à l’analyse des cartes de visite avec l’API Form Recognizer - utilisation et limites.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: lajanuar
ms.openlocfilehash: 92817318108370f1edf9ca2b38bf01226612b53a
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111890777"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>Modèle de cartes de visite prédéfini Form Recognizer

Azure Form Recognizer peut analyser et extraire des informations de contact à partir de cartes de visite à l’aide de l’un de ses modèles de carte de visite prédéfinis. Il associe de puissantes fonctionnalités de reconnaissance optique de caractères (OCR) à notre modèle de compréhension de carte de visite pour extraire des informations clés à partir de cartes de visite en anglais. Elle extrait les informations de contact personnel, le nom de la société, la fonction et plus encore. L’API Carte de visite prédéfinie est en disponibilité publique dans Form Recognizer v2.1.

## <a name="customer-scenarios"></a>Scénarios clients

Les données extraites avec l’API Carte de visite peuvent être utilisées pour effectuer diverses tâches. L’extraction automatique de ces coordonnées permet aux utilisateurs en contact avec la clientèle de gagner du temps. Voici quelques exemples de ce que nos clients ont accompli avec l’API Carte de visite :

* Extraction d’informations de contact des cartes de visite et création rapide de contacts téléphoniques.
* Intégration à CRM pour créer automatiquement des contacts à l’aide d’images de carte de visite.
* Suivi des prospects.
* Extraction d’informations de contact en bloc à partir d’images de carte de visite existantes.

La [fonctionnalité de traitement des cartes de visite AI Builder](/ai-builder/prebuilt-business-card) est également basée sur l’API Carte de visite.


## <a name="try-it-out"></a>Faire un essai

Pour tester le service de ticket de caisse de Form Recognizer, accédez à l’outil d’exemple d’interface utilisateur en ligne :

> [!div class="nextstepaction"]
> [Essayer des modèles prédéfinis](https://aka.ms/fott-2.1-ga)

## <a name="what-does-the-business-card-service-do"></a>Quelle est la fonction du service Carte de visite ?

L’API Carte de visite prédéfinie extrait les champs clés des cartes de visite et les retourne dans une réponse JSON organisée.

![Image détaillée de Contoso à partir de la sortie JSON + FOTT](./media/business-card-example.jpg)

### <a name="fields-extracted"></a>Champs extraits :

|Nom| Type | Description | Texte | Valeur (sortie standardisée) |
|:-----|:----|:----|:----|:----|
| ContactNames | tableau d’objets | Nom de contact extrait de la carte de visite | [{ "FirstName": "Chris", "LastName": "Smith" }] | |
| FirstName | string | Prénom du contact | "Chris" | "Chris" |
| LastName | string | Nom du contact |     "Smith" | "Smith" |
| CompanyNames | tableau de chaînes | Nom de société extrait de la carte de visite | ["CONTOSO"] | CONTOSO |
| Departments | tableau de chaînes | Service ou organisation de contact | ["Cloud & Al Department"] | Cloud & Al Department |
| JobTitles | tableau de chaînes | Poste indiqué du contact | ["Senior Researcher"] | Senior Researcher |
| E-mails | tableau de chaînes | E-mail de contact extrait de la carte de visite | ["chris.smith@contoso.com"] | chris.smith@contoso.com |
| Sites web | tableau de chaînes | Site web extrait de la carte de visite | ["https://www.contoso.com"] | https://www.contoso.com |
| Adresses | tableau de chaînes | Adresse extraite de la carte de visite | ["4001 1st Ave NE Redmond, WA 98052"] | 4001 1st Ave NE Redmond, WA 98052 |
| MobilePhones | tableau de numéros de téléphone | Numéro de téléphone mobile extrait de la carte de visite | ["+1 (987) 123-4567"] | +19871234567 |
| Télécopies | tableau de numéros de téléphone | Numéro de télécopie extrait de la carte de visite | ["+1 (987) 312-6745"] | +19873126745 |
| WorkPhones | tableau de numéros de téléphone | Numéro de téléphone professionnel extrait de la carte de visite | ["+1 (987) 213-5674"] | +19872135674 | 
| OtherPhones     | tableau de numéros de téléphone | Autre numéro de téléphone extrait de la carte de visite | ["+1 (987) 213-5673"] | +19872135673 |


L’API Carte de visite peut également retourner tout le texte reconnu de la carte de visite. Cette sortie OCR est incluse dans la réponse JSON.

### <a name="input-requirements"></a>Spécifications relatives aux entrées

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Paramètres régionaux pris en charge

**Pre-built Business Cards v2.1** prend en charge les paramètres régionaux suivants : **en-us**, **en-au**, **en-ca**, **en-gb**, **en-in**

## <a name="the-analyze-business-card-operation"></a>Opération d’analyse de carte de visite

L’opération d’[analyse de carte de visite](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync) prend une image ou un fichier PDF de carte de visite comme entrée et extrait les valeurs présentant un intérêt. L’appel retourne un champ d’en-tête de réponse appelé `Operation-Location`. La valeur `Operation-Location` est une URL qui contient l’ID de résultat à utiliser à l’étape suivante.

|En-tête de réponse| URL de résultat |
|:-----|:----|
|Operation-Location | `https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync` |

## <a name="the-get-analyze-business-card-result-operation"></a>Opération d’obtention du résultat de l’analyse de la carte de visite

La seconde étape consiste à appeler l’opération d’[obtention du résultat de l’analyse de la carte de visite](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeBusinessCardResult). Cette opération prend en entrée l’ID de résultat créé par l’opération d’analyse de carte de visite. Elle retourne une réponse JSON qui contient un champ **État** avec les possibles valeurs suivantes. Vous appelez cette opération de façon itérative jusqu’à ce qu’elle retourne avec la valeur **succeeded**. Utilisez un intervalle de 3 à 5 secondes pour éviter de dépasser le taux de demandes par seconde (RPS).

|Champ| Type | Valeurs possibles |
|:-----|:----:|:----|
|status | string | notStarted : L’opération d’analyse n’a pas commencé.<br /><br />running : L’opération d’analyse est en cours.<br /><br />failed : L’opération d’analyse a échoué.<br /><br />succeeded : L’opération d’analyse a réussi.|

Quand le champ **status** a la valeur de **succeeded**, la réponse JSON inclut les résultats de la compréhension de carte de visite et de la reconnaissance de texte facultative, si nécessaire. Le résultat de compréhension de carte de visite est organisé sous la forme d’un dictionnaire de valeurs de champ nommé, où chaque valeur contient le texte extrait, la valeur normalisée, le cadre englobant, la confiance et les éléments de mot correspondants. Le résultat de reconnaissance de texte est organisé sous la forme d’une hiérarchie de lignes et de mots, avec du texte, un cadre englobant et des informations de confiance.

![Exemple de sortie de carte de visite](./media/business-card-results.png)

### <a name="sample-json-output"></a>Exemple de sortir JSON

La réponse à l’opération d’obtention du résultat de l’analyse de la carte de visite va correspondre à la représentation structurée de la carte de visite avec toutes les informations extraites.  Consultez ici un [exemple de fichier de carte de visite](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-english.jpg) et sa sortie structurée dans l’[exemple de sortie de carte de visite](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json).

Voici un exemple de réponse JSON correcte (la sortie a été raccourcie à des fins de simplicité) :
* Le nœud `"readResults"` contient tout le texte reconnu. Le texte est organisé par page, puis par ligne, puis par mots individuels.
* Le nœud `"documentResults"` contient les valeurs spécifiques de la carte de visite découvertes par le modèle. C’est là que vous trouverez des informations de contact utiles comme le prénom, le nom, le nom de la société, etc.

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-05-27T02:18:35Z",
    "lastUpdatedDateTime": "2021-05-27T02:18:37Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0.0255,
                "width": 2592,
                "height": 4608,
                "unit": "pixel",
                "lines": [
                    {
                        "text": "CONTOSO",
                        "boundingBox": [
                            533,
                            1570,
                            1334,
                            1570,
                            1333,
                            1721,
                            533,
                            1720
                        ],
                        "words": [
                            {
                                "text": "CONTOSO",
                                "boundingBox": [
                                    535,
                                    1571,
                                    1278,
                                    1571,
                                    1279,
                                    1722,
                                    534,
                                    1719
                                ],
                                "confidence": 0.994
                            }
                        ],
                        "appearance": {
                            "style": {
                                "name": "other",
                                "confidence": 0.878
                            }
                        }
                    },
                    ...
                ]
            }
        ],   
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "4001 1st Ave NE Redmond, WA 98052",
                                "text": "4001 1st Ave NE Redmond, WA 98052",
                                "boundingBox": [
                                    400,
                                    2789,
                                    1514,
                                    2789,
                                    1514,
                                    2857,
                                    400,
                                    2857
                                ],
                                "page": 1,
                                "confidence": 0.986,
                                "elements": [
                                    "#/readResults/0/lines/9/words/0",
                                    "#/readResults/0/lines/9/words/1",
                                    "#/readResults/0/lines/9/words/2",
                                    "#/readResults/0/lines/9/words/3",
                                    "#/readResults/0/lines/9/words/4",
                                    "#/readResults/0/lines/9/words/5",
                                    "#/readResults/0/lines/9/words/6"
                                ]
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "CONTOSO",
                                "text": "CONTOSO",
                                "boundingBox": [
                                    535,
                                    1571,
                                    1278,
                                    1571,
                                    1279,
                                    1722,
                                    534,
                                    1719
                                ],
                                "page": 1,
                                "confidence": 0.985,
                                "elements": [
                                    "#/readResults/0/lines/0/words/0"
                                ]
                            }
                        ]
                    },
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Chris",
                                        "text": "Chris",
                                        "boundingBox": [
                                            1556,
                                            2018,
                                            1915,
                                            2021,
                                            1915,
                                            2156,
                                            1558,
                                            2154
                                        ],
                                        "page": 1,
                                        "elements": [
                                            "#/readResults/0/lines/1/words/0"
                                        ]
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1944,
                                            2021,
                                            2368,
                                            2016,
                                            2364,
                                            2156,
                                            1944,
                                            2156
                                        ],
                                        "page": 1,
                                        "elements": [
                                            "#/readResults/0/lines/1/words/1"
                                        ]
                                    }
                                },
                                "text": "Chris Smith",
                                "boundingBox": [
                                    1556.1,
                                    2010.3,
                                    2368,
                                    2016,
                                    2367,
                                    2159.6,
                                    1555.1,
                                    2154
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/1/words/0",
                                    "#/readResults/0/lines/1/words/1"
                                ]
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    1578,
                                    2288.8,
                                    2277,
                                    2295.1,
                                    2276.3,
                                    2367.8,
                                    1577.3,
                                    2361.5
                                ],
                                "page": 1,
                                "confidence": 0.989,
                                "elements": [
                                    "#/readResults/0/lines/3/words/0",
                                    "#/readResults/0/lines/3/words/1",
                                    "#/readResults/0/lines/3/words/2",
                                    "#/readResults/0/lines/3/words/3"
                                ]
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "chris.smith@contoso.com",
                                "text": "chris.smith@contoso.com",
                                "boundingBox": [
                                    1583,
                                    2381,
                                    2309,
                                    2382,
                                    2308,
                                    2445,
                                    1584,
                                    2447
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/4/words/0"
                                ]
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "valuePhoneNumber": "+19873126745",
                                "text": "+1 (987) 312-6745",
                                "boundingBox": [
                                    740,
                                    2703.8,
                                    1273,
                                    2702.1,
                                    1273.2,
                                    2774.1,
                                    740.2,
                                    2775.8
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/8/words/1",
                                    "#/readResults/0/lines/8/words/2",
                                    "#/readResults/0/lines/8/words/3"
                                ]
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    1578,
                                    2206,
                                    2117,
                                    2207.6,
                                    2116.8,
                                    2272.6,
                                    1577.8,
                                    2271
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/2/words/0",
                                    "#/readResults/0/lines/2/words/1"
                                ]
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "valuePhoneNumber": "+19871234567",
                                "text": "+1 (987) 123-4567",
                                "boundingBox": [
                                    744,
                                    2529,
                                    1281,
                                    2529,
                                    1281,
                                    2603,
                                    744,
                                    2603
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/5/words/1",
                                    "#/readResults/0/lines/5/words/2",
                                    "#/readResults/0/lines/5/words/3"
                                ]
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    1576,
                                    2462,
                                    2383,
                                    2462,
                                    2380,
                                    2535,
                                    1576,
                                    2535
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/6/words/0"
                                ]
                            }
                        ]
                    },
                    "WorkPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "valuePhoneNumber": "+19872135674",
                                "text": "+1 (987) 213-5674",
                                "boundingBox": [
                                    736,
                                    2617.6,
                                    1267.1,
                                    2618.5,
                                    1267,
                                    2687.5,
                                    735.9,
                                    2686.6
                                ],
                                "page": 1,
                                "confidence": 0.984,
                                "elements": [
                                    "#/readResults/0/lines/7/words/1",
                                    "#/readResults/0/lines/7/words/2",
                                    "#/readResults/0/lines/7/words/3"
                                ]
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

* Essayez vos propres cartes de visite et exemples dans l’[exemple d’interface utilisateur Form Recognizer](https://fott-preview.azurewebsites.net/).
* Suivez un [démarrage rapide de Form Recognizer](quickstarts/client-library.md) pour commencer à écrire une application de traitement de carte de visite avec Form Recognizer dans le langage de développement de votre choix.
