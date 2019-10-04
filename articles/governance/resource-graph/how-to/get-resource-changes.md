---
title: Obtenir les changements des ressources
description: Découvrez comment savoir quand une ressource a été modifiée et obtenir une liste des propriétés modifiées.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: b6ef57a3f39c82be30d92aef72c1bbe03b653768
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66236504"
---
# <a name="get-resource-changes"></a>Obtenir les changements des ressources

Les ressources se transforment au fil de l’utilisation quotidienne, de la reconfiguration et même du redéploiement.
Ces modifications peuvent provenir d’une personne ou d’un processus automatisé. La plupart des modifications sont normales, mais ce n’est parfois pas le cas. Avec les 14 derniers jours d’historique des modifications, Azure Resource Graph vous permet de :

- Savoir à quel moment des changements ont été détectés sur une propriété Azure Resource Manager.
- Voir quelles propriétés ont changé dans le cadre de cet événement.

La détection des modifications et les détails sont utiles dans les scénarios suivants :

- Au cours de la gestion des incidents pour comprendre les modifications _potentiellement_ associées. Interrogez les événements de modification dans une fenêtre de temps spécifique et évaluez les détails des modifications.
- Dans le maintien d’une base de données de gestion de la configuration (CMBD) à jour. Au lieu de l’actualisation de toutes les ressources et de leurs jeux de propriétés complets selon une fréquence planifiée, obtenez uniquement les informations relatives à ce qui a changé.
- Pour la compréhension des autres propriétés modifiées lorsqu’une ressource a changé d’état de conformité. L’évaluation de ces propriétés supplémentaires peut fournir des insights sur les autres propriétés susceptibles de nécessiter une gestion par le biais d’une définition Azure Policy.

Cet article montre comment rassembler ces informations au moyen du kit SDK Resource Graph. Pour obtenir ces informations dans le Portail Azure, voir [Historique des changements](../../policy/how-to/determine-non-compliance.md#change-history-preview) d’Azure Policy ou [Historique des changements](../../../azure-monitor/platform/activity-log-view.md#azure-portal) d’Azure Activity Log.

> [!NOTE]
> Les détails des modifications dans Resource Graph concernent les propriétés Resource Manager. Pour le suivi des modifications à l’intérieur d’une machine virtuelle, voir [Suivi des modifications](../../../automation/automation-change-tracking.md) d’Azure Automation ou [Guest Configuration for VMs (Configuration invitée pour les machines virtuelles)](../../policy/concepts/guest-configuration.md) d’Azure Policy.

> [!IMPORTANT]
> L’historique des changements dans Azure Resource Graph est en préversion publique.

## <a name="find-when-changes-were-detected"></a>Découvrir quand des modifications ont été détectées

La première étape pour voir ce qui a changé sur une ressource consiste à rechercher les événements de modification associés à cette ressource au sein d’une fenêtre de temps donnée. Cette étape s’effectue au moyen du point de terminaison REST **resourceChanges**.

Le point de terminaison **resourceChanges** requiert deux paramètres dans le corps de la requête :

- **resourceId** : ressource Azure sur laquelle rechercher des modifications.
- **interval** : propriété avec dates de _début_ et de _fin_ pour le contrôle d’un événement de modification à l’aide de **Zulu fuseau horaire (Z)** .

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

Avec le corps de la demande ci-dessus, l’URI d’API REST pour **resourceChanges** est :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

La réponse se présente ainsi :

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

Chaque événement de modification détecté pour **resourceId** a un **changeId** unique pour cette ressource. Bien que la chaîne **changeId** puisse parfois contenir d’autres propriétés, elle est uniquement garantie pour son unicité. L’enregistrement de modification inclut les heures avant et après lesquelles les captures instantanées ont été effectuées.
L’événement de modification s’est produit à un moment donné dans cette fenêtre de temps.

## <a name="see-what-properties-changed"></a>Consulter les propriétés modifiées

Avec **changeId** dans le point de terminaison **resourceChanges**, le point de terminaison REST **resourceChangeDetails** permet d’obtenir des détails de l’événement de modification.

Le point de terminaison **resourceChangeDetails** requiert deux paramètres dans le corps de la demande :

- **resourceId** : ressource Azure sur laquelle rechercher des modifications.
- **changeId** : événement de modification unique pour le **resourceId** collecté à partir de **resourceChanges**.

Exemple de corps de requête :

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Avec le corps de la demande ci-dessus, l’URI d’API REST pour **resourceChangeDetails** est :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

La réponse se présente ainsi :

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

**beforeSnapshot** et **afterSnapshot** donnent chacun l’heure à laquelle la capture instantanée a été effectuée et les propriétés à ce moment-là. La modification a eu lieu à un moment donné entre ces captures instantanées. L’exemple ci-dessus montre que la propriété modifiée est **supportsHttpsTrafficOnly**.

Pour comparer les résultats par programmation, comparez la portion **content** de chaque capture instantanée. Si vous comparez l’intégralité de l’instantané, le **timestamp** s’affiche toujours sous forme de différence en dépit de ce qui est attendu.

## <a name="next-steps"></a>Étapes suivantes

- Examinez le langage utilisé dans les [requêtes de démarrage](../samples/starter.md).
- Examinez les utilisations avancées dans les [Requêtes avancées](../samples/advanced.md).
- Apprenez à [explorer les ressources](../concepts/explore-resources.md).