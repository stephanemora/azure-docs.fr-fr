---
title: Mise hors service de l’API Azure Monitor
description: Décrit la mise hors service des versions antérieures de l’API de fournisseur de ressources OperationalInsights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: c9a7ba63246f747a132c315e22452ca9c7144448
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058180"
---
# <a name="operationalinsights-api-version-retirement"></a>Mise hors service de la version de l’API OperationalInsights
Microsoft envoie une notification au moins 12 mois avant la mise hors service d’une API pour faciliter la transition vers une version plus récente/prise en charge. Nous avons publié une nouvelle version (2020-08-01) des API de fournisseur de ressources **OperationalInsights** et mettront hors service toutes les versions antérieures des API le 31 octobre 2023. Puisque les nouvelles fonctionnalités et fonctions, ainsi que les optimisations, sont uniquement ajoutées à l’API actuelle, vous devez effectuer une mise à niveau vers la version la plus récente de l’API dès que possible.

Après le 31 octobre 2023, Azure Monitor ne prendra plus en charge les versions d’API antérieures à la version 2020-08-01. Si vous préférez ne pas effectuer la mise à niveau, les requêtes envoyées depuis des versions antérieures continueront d’être traitées par le service Azure Monitor jusqu’au 31 octobre 2023.

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

- Consultez la [référence pour l’API d’espace de travail OperationalInsights](https://docs.microsoft.com/rest/api/loganalytics/workspaces).
