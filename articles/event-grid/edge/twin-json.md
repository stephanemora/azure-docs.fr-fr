---
title: Jumeau de module – Azure Event Grid sur IoT Edge | Microsoft Docs
description: Configuration via un jumeau de module.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: 27e5194b86ea5f76a346412c3239dac111c07d80
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110379780"
---
# <a name="module-twin-json-schema-azure-event-grid"></a>Schéma JSON de jumeau de module (Azure Event Grid)

Event Grid sur IoT Edge s’intègre avec l’écosystème IoT Edge et prend en charge la création de rubriques et d’abonnements via le jumeau de module. Il indique également l’état actuel de l’ensemble des rubriques et abonnements aux événements des propriétés signalées sur le jumeau de module.

> [!WARNING]
> En raison des limitations de l’écosystème IoT Edge, tous les éléments de tableau dans l’exemple de json suivant ont été encodés en tant que chaînes json. Voir les clés `EventSubscription.Filter.EventTypes` et `EventSubscription.Filter.AdvancedFilters` dans l’exemple suivant.

## <a name="desired-properties-json"></a>JSON des propriétés souhaitées

* La valeur de chaque paire clé-valeur dans la section des rubriques contient exactement le même schéma json que celui utilisé pour `Topic.Properties` sur l’API lors de la création de rubriques.
* La valeur de chaque paire clé-valeur dans la section **EventSubscriptions** a exactement le même schéma json que celui utilisé pour `EventSubscription.Properties` sur l’API lors de la création de rubriques.
* Pour supprimer une rubrique, définissez sa valeur sur `null` dans les propriétés souhaitées.
* La suppression d’abonnements aux événements via les propriétés souhaitées n’est pas prise en charge.

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    }
}
```

## <a name="reported-properties-json"></a>JSON des propriétés signalées

La section des propriétés signalées du jumeau de module contient les informations suivantes :

* Ensemble des rubriques et abonnements qui existent dans le magasin du module
* Toutes les erreurs rencontrées lors de la création des rubriques/abonnements aux événements souhaités
* Toutes les erreurs de démarrage (telles que l’échec d’analyse du JSON des propriétés souhaitées)

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    },
    "errors": {
        "bootupMessage": "",
        "bootupException": "",
        "topics": {},
        "eventSubscriptions": {
            "twinTopic1EventGridUserTopicSub": "HttpStatusCode='BadRequest' ErrorCode='InvalidDestination' Message='EventSubscription.Properties.Destination failed validation. Reason: EndpointUrl must target the /api/events API of Azure Event Grid in the cloud..'"
        }
    }
}
```
