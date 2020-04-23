---
title: Collecter et analyser les journaux d’activités Azure dans Azure Monitor
description: Collectez le journal d'activité Azure dans les journaux Azure Monitor et utilisez la solution de supervision pour analyser et rechercher le journal d’activité Azure dans tous vos abonnements Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382869"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Collecter et analyser les journaux d’activités Azure dans Azure Monitor
Le [journal d’activité Azure](platform-logs-overview.md) est un [journal de plateforme](platform-logs-overview.md) qui fournit un aperçu de tous les événements de niveau d’abonnement qui se sont produits dans Azure. Si vous pouvez afficher le journal d’activité dans le Portail Azure, vous devez le configurer pour qu’il soit envoyé à un espace de travail Log Analytics afin d’activer des fonctionnalités Azure Monitor supplémentaires. Cet article explique comment effectuer cette configuration et comment envoyer le journal d’activité à Stockage Azure et aux Event Hubs.

La collecte du journal d’activité dans un espace de travail Log Analytics offre les avantages suivants :

- Aucune ingestion des données ni aucuns frais de rétention pour les données de journal d’activité stockées dans un espace de travail Log Analytics.
- Mettre en corrélation les données du journal d’activité avec d’autres données d’analyse collectées par Azure Monitor.
- Utiliser les requêtes de journal pour effectuer des analyses complexes et obtenir des informations détaillées sur les entrées du journal d’activité.
- Utiliser des alertes de journal avec des entrées d’activité qui permettent une logique d’alerte plus complexe.
- Stocker les entrées du journal d’activité au-delà de 90 jours.
- Consolider les entrées de journal de plusieurs abonnements et locataires Azure en un seul endroit pour les analyser ensemble.

> [!IMPORTANT]
> La collecte des journaux de plusieurs locataires nécessite [Azure Lighthouse](/azure/lighthouse).

## <a name="collecting-activity-log"></a>Collecte du journal d’activité
Le journal d’activité est collecté automatiquement pour [être affiché dans le Portail Azure](activity-log-view.md). Pour le collecter dans un espace de travail Log Analytics ou l’envoyer au stockage Azure ou à des Event Hubs, créez un [paramètre de diagnostic](diagnostic-settings.md). Il s’agit de la même méthode que celle utilisée pour les journaux de ressources, ce qui la rend cohérente pour tous les [journaux de plateforme](platform-logs-overview.md).  

Afin de créer un paramètre de diagnostic pour le journal d’activité, sélectionnez **Paramètres de diagnostic** dans le menu **Journal d’activité** d’Azure Monitor. Pour plus d’informations sur la création du paramètre, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](diagnostic-settings.md). Pour obtenir une description des catégories que vous pouvez filtrer, consultez [Catégories dans le journal d’activité](activity-log-view.md#categories-in-the-activity-log). Si vous avez des paramètres hérités, veillez à les désactiver avant de créer un paramètre de diagnostic. Si les deux sont activées, les données peuvent être dupliquées.

![Paramètres de diagnostic](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> Actuellement, vous pouvez uniquement créer un paramètre de diagnostic de niveau d’abonnement à l’aide du Portail Azure et d’un modèle Resource Manager. 


## <a name="legacy-settings"></a>Paramètres hérités 
Alors que les paramètres de diagnostic sont la méthode recommandée pour envoyer le journal d’activité vers différentes destinations, les méthodes héritées continuent de fonctionner si vous ne choisissez pas de les remplacer par un paramètre de diagnostic. Les paramètres de diagnostic présentent les avantages suivants par rapport aux méthodes héritées, et il est recommandé de mettre à jour votre configuration :

- Méthode cohérente pour collecter tous les journaux de plateforme.
- Collecte du journal d’activité pour plusieurs abonnements et locataires.
- Filtrage de la collecte pour collecter uniquement les journaux de catégories spécifiques.
- Collecte de toutes les catégories du journal d’activité. Certaines catégories ne sont pas collectées à l’aide de la méthode héritée.
- Latence plus rapide pour l’ingestion des journaux. La méthode précédente a une latence d’environ 15 minutes, tandis que les paramètres de diagnostic n’ajoutent qu’environ 1 minute.



### <a name="log-profiles"></a>Profils de journal
Les profils de journal sont la méthode héritée pour l’envoi du journal d’activité vers le stockage Azure ou les Event Hubs. Utilisez la procédure suivante pour continuer à utiliser un profil de journal ou pour le désactiver en vue de la migration vers un paramètre de diagnostic.

1. À partir du menu **Azure Monitor** du Portail Azure, sélectionnez **Journal d’activité**.
3. Cliquez sur **Paramètres de diagnostic**.

   ![Paramètres de diagnostic](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Cliquez sur la bannière violette pour obtenir l’expérience héritée.

    ![Expérience héritée](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Espace de travail Log Analytics
La méthode héritée pour la collecte du journal d’activité dans un espace de travail Log Analytics consiste à connecter le journal dans la configuration de l’espace de travail. 

1. À partir du menu **Espaces de travail Log Analytics** du portail Azure, sélectionnez l’espace de travail pour collecter le journal d’activité.
1. Dans la section **Sources de données de l’espace de travail** du menu de l’espace de travail, sélectionnez **Journal d’activité Azure**.
1. Cliquez sur l’abonnement que vous souhaitez connecter.

    ![Workspaces](media/activity-log-collect/workspaces.png)

1. Cliquez sur **Connecter** pour connecter le journal d’activité de l’abonnement à l’espace de travail sélectionné. Si l’abonnement est déjà connecté à un autre espace de travail, cliquez d'abord sur **Déconnecter** pour le déconnecter.

    ![Connecter des espaces de travail](media/activity-log-collect/connect-workspace.png)


Pour désactiver le paramètre, effectuez la même procédure et cliquez sur **Déconnecter** pour supprimer l’abonnement de l’espace de travail.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Analyser le journal d’activité dans l’espace de travail Log Analytics
Lorsque vous connectez un journal d’activité à un espace de travail Log Analytics, les entrées sont écrites dans l’espace de travail dans une table appelée *AzureActivity* que vous pouvez récupérer à l'aide d'une [requête de journal](../log-query/log-query-overview.md). La structure de cette table varie selon la [catégorie de l’entrée de journal](activity-log-view.md#categories-in-the-activity-log). Consultez [Schéma d’événements du journal d’activité](activity-log-schema.md) pour obtenir une description de chaque catégorie.


### <a name="data-structure-changes"></a>Modifications de la structure de données
Les paramètres de diagnostic collectent les mêmes données que la méthode héritée utilisée pour collecter le journal d’activité, avec quelques modifications de la structure de la table *AzureActivity*.

Les colonnes de la table suivante ont été dépréciées dans le schéma mis à jour. Elles existent toujours dans *AzureActivity*, mais elles ne contiennent aucune donnée. Les colonnes de remplacement ne sont pas nouvelles, mais elles contiennent les mêmes données que les colonnes dépréciées. Elles sont dans un format différent, ce qui signifie que vous devrez peut-être modifier les requêtes de journal qui les utilisent. 

| Colonne dépréciée | Colonne de remplacement |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| NomOpération     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> Dans certains cas, les valeurs de ces colonnes peuvent être en majuscules. Si vous avez une requête qui inclut ces colonnes, vous devez utiliser l’[opérateur =~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) pour effectuer une comparaison non sensible à la casse.

La colonne suivante a été ajoutée à *AzureActivity* dans le schéma mis à jour :

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Solution de supervision Activity Log Analytics
La solution de supervision Azure Log Analytics sera bientôt déconseillée et remplacée par un classeur utilisant le schéma mis à jour dans l’espace de travail Log Analytics. Vous pouvez toujours utiliser la solution si vous l’avez déjà activée, mais elle ne peut être utilisée que si vous collectez le journal d’activité à l’aide des paramètres hérités. 



### <a name="use-the-solution"></a>Utiliser la solution
Les solutions de supervision sont accessibles à partir du menu **Superviser** du portail Azure. Sélectionnez **Plus** dans la section **Insights** pour ouvrir la page **Vue d’ensemble** avec les vignettes des solutions. La vignette **Azure Activity Log** affiche le nombre d'enregistrements **AzureActivity** dans votre espace de travail.

![Vignette Journaux d’activité Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Cliquez sur la vignette **Journaux d’activité Azure** pour ouvrir l'affichage **Journaux d’activité Azure**. La vue inclut les composants de visualisation dans le tableau suivant. Chaque partie répertorie jusqu'à 10 éléments répondant à ses critères en ce qui concerne l’intervalle de temps spécifié. Vous pouvez exécuter une requête de journal qui renvoie tous les enregistrements correspondants en cliquant sur **Afficher tout** en bas du composant.

![Tableau de bord Journaux d’activité Azure](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Activer la solution pour les nouveaux abonnements
Vous ne pourrez bientôt plus ajouter la solution Activity Log Analytics à votre abonnement à l’aide du Portail Azure. Vous pouvez l’ajouter à l’aide de la procédure suivante avec un modèle Resource Manager. 

1. Copiez le code  JSON suivant dans un fichier appelé *ActivityLogTemplate*.json.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Déployez le modèle à l’aide des commandes PowerShell suivantes :

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus en détail le [journal d’activité](platform-logs-overview.md).
- Apprenez-en davantage sur la [plateforme de données Azure Monitor](data-platform.md).
- Utilisez les [requêtes de journal](../log-query/log-query-overview.md) pour afficher des informations détaillées provenant de votre journal d’activité.
