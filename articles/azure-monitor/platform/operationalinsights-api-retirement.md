---
title: Mise hors service de l’API Azure Monitor
description: Décrit la mise hors service des versions antérieures de l’API de fournisseur de ressources OperationalInsights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/29/2020
ms.openlocfilehash: d094e1760db1ddfcff520b0022a232735d21d355
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525820"
---
# <a name="operationalinsights-api-version-retirement"></a>Mise hors service de la version de l’API OperationalInsights
Microsoft envoie une notification au moins 12 mois avant la mise hors service d’une API pour faciliter la transition vers une version plus récente/prise en charge. Nous avons publié une nouvelle version (2020-08-01) des API de fournisseur de ressources **OperationalInsights** et nous mettront hors service toutes les versions antérieures des API le 29 février 2024.

Nous vous encourageons à utiliser la version 2020-08-01 dès à présent pour tirer parti des nouvelles fonctionnalités, telles que les [clusters dédiés](../log-query/logs-dedicated-clusters.md), les [clés gérées par le client](./customer-managed-keys.md), la [liaison privée](./private-link-security.md) et l’[exportation de données](./logs-data-export.md). En outre, les nouvelles fonctionnalités et optimisations sont ajoutées uniquement à l’API actuelle.

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


### <a name="more-information"></a>Informations complémentaires
Si vous avez des questions, posez-les aux [experts de la communauté technologique]( https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor). Si vous disposez d’un plan de support et que vous avez besoin d’une aide technique, créez une [demande de support]( https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) : 
1.  Sous *Type de problème*, sélectionnez **Technique**. 
2.  Sous *Abonnement*, sélectionnez votre abonnement. 
3.  Sous *Service*, sélectionnez **Mes services**, puis **Log Analytics**. 
4.  Sous *Résumé*, entrez une description de votre problème. 
5.  Sous *Type de problème*, sélectionnez **Gestion de l’espace de travail Log Analytics**.  
6.  Sous *Sous-type du problème*, sélectionnez **Modèles ARM, PowerShell et CLI**. 

## <a name="next-steps"></a>Étapes suivantes

- Consultez la [référence pour l’API d’espace de travail OperationalInsights](/rest/api/loganalytics/workspaces).