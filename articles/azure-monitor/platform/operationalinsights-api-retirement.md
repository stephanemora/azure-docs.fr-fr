---
title: Mise hors service de l’API Azure Monitor
description: Décrit la mise hors service des versions antérieures de l’API de fournisseur de ressources OperationalInsights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/29/2020
ms.openlocfilehash: 7ddfb3221c73c740a339dd8d9d4f60cca46c6425
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026766"
---
# <a name="operationalinsights-api-version-retirement"></a>Mise hors service de la version de l’API OperationalInsights
Microsoft envoie une notification au moins 12 mois avant la mise hors service d’une API pour faciliter la transition vers une version plus récente/prise en charge. Nous avons publié une nouvelle version (2020-08-01) des API de fournisseur de ressources **OperationalInsights** et nous mettront hors service toutes les versions antérieures des API le 29 février 2024.

Nous vous encourageons à utiliser la version 2020-08-01 dès à présent pour tirer parti des nouvelles fonctionnalités, telles que les [clusters dédiés](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters), les [clés gérées par le client](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys), la [liaison privée](https://docs.microsoft.com/azure/azure-monitor/platform/private-link-security) et l’[exportation de données](https://docs.microsoft.com/azure/azure-monitor/platform/logs-data-export). En outre, les nouvelles fonctionnalités et optimisations sont ajoutées uniquement à l’API actuelle.

Après le 29 février 2024, Azure Monitor ne prendra plus en charge les versions d’API antérieures à la version 2020-08-01. Si vous préférez ne pas effectuer la mise à niveau, les requêtes envoyées depuis des versions antérieures continueront d’être traitées par le service Azure Monitor jusqu’au 29 février 2024.

## <a name="migration-steps"></a>Étapes de la migration
Selon la méthode de configuration que vous utilisez, vous devez mettre à jour la nouvelle version dans des requêtes **REST** et des **modèles Resource Manager**. Suivez les exemples ci-dessous pour mettre à jour la version de l’API :

1. Les requêtes de l’API REST utilisent la version de l’API dans l’URL de la demande. Remplacez cette version par la version la plus récente (2020-08-01), comme indiqué dans l’exemple suivant.

    ```rest
    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
    ```

2. Les modèles Resource Manager utilisent la version de l’API dans la propriété **apiVersion** de la ressource. Remplacez cette version par la version la plus récente (2020-08-01), comme indiqué dans l’exemple suivant.


    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                "description": "Name of the workspace."
                }
            },
            "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('workspaceName')]",
                "apiVersion": "2020-08-01",
                "location": "westus",
                "properties": {
                    "sku": {
                        "name": "pergb2018"
                    },
                    "retentionInDays": 30,
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0,
                        "enableLogAccessUsingOnlyResourcePermissions": true
                    }
                }
            }
        ]
    }
    }
    ```


## <a name="next-steps"></a>Étapes suivantes

- Consultez la [référence pour l’API d’espace de travail OperationalInsights](/rest/api/loganalytics/workspaces).
