---
title: Obtenir les changements des ressources
description: Comprendre comment rechercher quand une ressource a été modifiée et obtenir une liste des propriétés modifiées.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 4e28ca15197f89caeaeaca0aabb648755b8235f1
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551539"
---
# <a name="get-resource-changes"></a>Obtenir les changements des ressources

Ressources obtient changés au cours du redéploiement même utilisation quotidienne et reconfiguration.
Modification peut provenir d’une personne ou par un processus automatisé. La plupart des modifications sont par conception, mais parfois, il n’est pas. Avec les 14 derniers jours de l’historique des modifications, graphique des ressources Azure vous permet de :

- à quel moment des changements ont été détectés sur une propriété Azure Resource Manager ;
- quelles propriétés ont changé dans le cadre de cet événement.

Détection des modifications et les détails sont utiles pour les exemples de scénarios suivants :

- Au cours de la gestion des incidents pour comprendre _potentiellement_ les modifications associées. Interroger des événements de modification pendant une fenêtre de temps spécifique et évaluer les détails de la modification.
- En conservant une base de données de gestion de Configuration, appelé une base de données, à jour. Au lieu de l’actualisation de toutes les ressources et leurs jeux de propriétés complet selon une fréquence planifiée, obtenir uniquement ce qui a changé.
- Comprendre quelles autres propriétés ont été modifiées lorsqu’une ressource a changé d’état de conformité. Évaluation de ces propriétés supplémentaires peut fournir des informations dans les autres propriétés devant être gérés via une définition de stratégie de Azure.

Cet article montre comment rassembler ces informations via le kit SDK du graphique de la ressource. Pour obtenir ces informations dans le portail Azure, consultez de la stratégie Azure [l’historique des modifications](../../policy/how-to/determine-non-compliance.md#change-history-preview) ou le journal d’activité Azure [l’historique des modifications](../../../azure-monitor/platform/activity-logs-overview.md#view-change-history).

> [!NOTE]
> Détail des modifications dans le graphique des ressources est pour les propriétés de Resource Manager. Pour le suivi des modifications à l’intérieur d’une machine virtuelle, consultez d’Azure Automation [le suivi des modifications](../../../automation/automation-change-tracking.md) ou de la stratégie Azure [Configuration invité pour les machines virtuelles](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Historique des modifications dans le graphique des ressources Azure sont en version préliminaire publique.

## <a name="find-when-changes-were-detected"></a>Rechercher lorsque des modifications ont été détectées

La première étape pour voir ce qui a changé sur une ressource consiste à trouver les événements de modification associées à cette ressource au sein d’une fenêtre de temps. Cette étape s’effectue via le **resourceChanges** point de terminaison REST.

Le **resourceChanges** point de terminaison requiert deux paramètres dans le corps de la demande :

- **resourceId**: La ressource Azure pour rechercher des modifications à.
- **Intervalle**: Une propriété avec _Démarrer_ et _fin_ dates de contrôle pour un événement de modification à l’aide de la **Zulu fuseau horaire (Z)**.

Exemple de corps de requête :

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

Avec le corps de la requête ci-dessus, l’URI d’API REST pour **resourceChanges** est :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

La réponse ressemble à ceci :

```json
{
    "changes": [{
            "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
            "beforeSnapshot": {
                "timestamp": "2019-03-29T01:32:05.993Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-29T01:54:24.42Z"
            }
        },
        {
            "changeId": "9dc352cb-b7c1-4198-9eda-e5e3ed66aec8",
            "beforeSnapshot": {
                "timestamp": "2019-03-28T10:30:19.68Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-28T21:12:31.337Z"
            }
        }
    ]
}
```

Chaque détecté d’événement de modification pour le **resourceId** a un **changeId** qui est unique à cette ressource. Bien que le **changeId** chaîne peut-être parfois contenir d’autres propriétés, il a uniquement garanti pour être unique. L’enregistrement de modification inclut les heures qui l’avant et après les captures instantanées ont été effectuées.
L’événement de modification s’est produite à un moment donné dans cette fenêtre de temps.

## <a name="see-what-properties-changed"></a>Consultez les propriétés modifiées

Avec le **changeId** à partir de la **resourceChanges** point de terminaison, le **resourceChangeDetails** point de terminaison REST est ensuite utilisé pour obtenir des détails de l’événement de modification.

Le **resourceChangeDetails** point de terminaison requiert deux paramètres dans le corps de la demande :

- **resourceId**: La ressource Azure pour rechercher des modifications à.
- **changeId**: L’événement de modification unique pour le **resourceId** collectées à partir de **resourceChanges**.

Exemple de corps de requête :

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Avec le corps de la requête ci-dessus, l’URI d’API REST pour **resourceChangeDetails** est :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

La réponse ressemble à ceci :

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** et **afterSnapshot** chacun donner à la durée de la capture instantanée et les propriétés à ce moment-là. La modification a eu lieu à un moment donné entre ces captures instantanées. En examinant l’exemple ci-dessus, nous pouvons voir que la propriété ayant changé a été **supportsHttpsTrafficOnly**.

Pour comparer les résultats par programmation, comparer les **contenu** partie de chaque capture instantanée pour déterminer la différence. Si vous comparez l’intégralité de l’instantané, le **timestamp** affiche toujours sous forme de différence en dépit d’en cours attendu.

## <a name="next-steps"></a>Étapes suivantes

- Consultez le langage en cours d’utilisation dans [les requêtes de démarrage](../samples/starter.md).
- Consultez avancée utilise dans [requêtes avancées](../samples/advanced.md).
- Apprenez à [Explorez les ressources](../concepts/explore-resources.md).