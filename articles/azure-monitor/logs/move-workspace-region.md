---
title: Déplacer un espace de travail Log Analytics vers une autre région Azure à l’aide du portail Azure
description: Utilisez un modèle Azure Resource Manager pour déplacer espace de travail Log Analytics d’une région Azure vers une autre à l’aide du portail Azure.
author: yossiy
ms.topic: how-to
ms.date: 08/17/2021
ms.author: yossiy
ms.openlocfilehash: f3aeb0614907d1f10e3f080a2399029711192bb8
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662217"
---
# <a name="move-log-analytics-workspace-to-another-region-using-the-azure-portal"></a>Déplacer un espace de travail Log Analytics vers une autre région à l’aide du portail Azure

Il existe différents scénarios dans lesquels vous pouvez être amené à déplacer votre espace de travail Log Analytics existant d’une région à une autre. Par exemple, Log Analytics est récemment devenu disponible dans une région qui héberge la plupart de vos ressources et vous voulez que l’espace de travail soit plus proche et fasse des économies sur les frais de sortie. Vous pouvez également souhaiter déplacer votre espace de travail vers une région nouvellement ajoutée pour des raisons de souveraineté des données.

Un espace de travail Log Analytics ne peut pas être déplacé d’une région à une autre. Toutefois, vous pouvez utiliser un modèle de Azure Resource Manager pour exporter la ressource d’espace de travail et les ressources associées. Vous pouvez ensuite indexer les ressources dans une autre région en exportant l’espace de travail vers un modèle, en modifiant les paramètres pour qu’ils correspondent à la région de destination, puis en déployant le modèle dans la nouvelle région. Pour plus d’informations sur Resource Manager et les modèles, consultez [Démarrage rapide : Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). Un environnement d’espace de travail peut être complexe et inclure des sources connectées, des solutions gérées, des services liés, des alertes et des packs de requêtes. Toutes les ressources ne peuvent pas être exportées dans un modèle Resource Manager, et certaines nécessitent une configuration distincte lorsque vous déplacez un espace de travail.

## <a name="prerequisites"></a>Prérequis

- Pour exporter la configuration de l’espace de travail vers un modèle qui peut être déployé dans une autre région, vous devez avoir le rôle [Contributeur Log Analytics](../../role-based-access-control/built-in-roles.md#log-analytics-contributor) ou [Contributeur d’analyse](../../role-based-access-control/built-in-roles.md#monitoring-contributor), ou avoir un rôle ayant des privilèges plus élevés.

- Identifiez toutes les ressources actuellement associées à votre espace de travail, notamment :
  - Agents connectés : Accédez à *Journaux* dans votre espace de travail et interrogez la table [Heartbeat](../insights/solution-agenthealth.md#heartbeat-records) pour répertorier les agents connectés.
    ```kusto
    Heartbeat
    | summarize by Computer, Category, OSType, _ResourceId
    ```
  - Solutions installées : Cliquez sur **Solutions** dans le volet de navigation de l’espace de travail pour obtenir la liste des solutions installées.
  - API de collecte de données : Les données arrivant par le biais de l’[API de collecte de données](../logs/data-collector-api.md) sont stockées dans des tables de journal personnalisées. Cliquez sur ***Journaux** _ dans le volet de navigation de l’espace de travail, puis sur _ *Journal personnalisé** dans le volet du schéma pour obtenir la liste des tables de journal personnalisées.
  - Services liés : L’espace de travail peut avoir des services liés à des ressources dépendantes telles qu’un compte Automation, un compte de stockage ou un cluster dédié. Supprimez les services liés de votre espace de travail. Ils doivent être reconfigurés manuellement dans l’espace de travail cible.
  - Alertes : Cliquez sur **Alertes** dans le volet de navigation de votre espace de travail, puis sur **Gérer les règles d’alerte** dans la barre d’outils pour répertorier les alertes. Les alertes des espaces de travail créés après le 1er juin 2019 ou des espaces de travail qui ont été [mis à niveau de l’API d’alerte Log Analytics héritée vers l’API scheduledQueryRules](../alerts/alerts-log-api-switch.md) peuvent être incluses dans le modèle. Vous pouvez [vérifier si l’API scheduledQueryRules est utilisée pour les alertes dans votre espace de travail](../alerts/alerts-log-api-switch.md#check-switching-status-of-workspace). Vous pouvez également configurer manuellement les alertes dans l’espace de travail cible.
  - Packs de requêtes : Un espace de travail peut être associé à plusieurs packs de requêtes. Pour identifier les packs de requêtes dans votre espace de travail, cliquez sur **Journaux** dans le volet de navigation de l’espace de travail, sur **requêtes** dans le volet de gauche, puis sur le bouton de sélection à droite de la zone de recherche. Une boîte de dialogue contenant les packs de requêtes sélectionnés s’ouvre sur la droite. Si vos packs de requêtes se trouvent dans le même groupe de ressources que l’espace de travail que vous déplacez, vous pouvez les inclure dans cette migration.
- Vérifiez que votre abonnement Azure vous permet de créer un espace de travail Log Analytics dans la région cible.

## <a name="prepare-and-move"></a>Préparer et déplacer
Les étapes suivantes vous montrent comment préparer l’espace de travail et les ressources pour le déplacement en utilisant un modèle Resource Manager, puis comment les déplacer vers la région cible en utilisant le portail. Toutes les ressources ne peuvent pas être exportées à l’aide d’un modèle. Vous devrez les configurer séparément après la création de l’espace de travail dans la région cible.

### <a name="export-the-template-and-deploy-from-the-portal"></a>Exporter le modèle et le déployer à partir du portail

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis sélectionnez **Groupes de ressources**.
2. Localisez le groupe de ressources contenant votre espace de travail et cliquez dessus.
3. Pour afficher la ressource des alertes, cochez la case **Afficher les types cachés**.
4. Cliquez sur le filtre « Type » et sélectionnez **Espace de travail Log Analytics**, **Solution**, **SavedSearches**, **microsoft.insights/scheduledqueryrules** et **defaultQueryPack** le cas échéant, puis cliquez sur Appliquer.
5. Sélectionnez l’espace de travail, les solutions, les alertes, les recherches enregistrées et le(s) pack(s) de requêtes que vous souhaitez déplacer, puis cliquez sur **Exporter le modèle** dans la barre d’outils.
    
    > [!NOTE]
    > Sentinel ne peut pas être exporté avec le modèle et vous devez [intégrer Sentinel](../../sentinel/quickstart-onboard.md) à l’espace de travail cible.
   
6. Cliquez sur **Déployer** dans la barre d’outils pour modifier et préparer le modèle pour le déploiement.
7. Cliquez sur **Modifier les paramètres** dans la barre d’outils pour ouvrir le fichier **parameters.json** dans l’éditeur en ligne.
8. Pour modifier les paramètres, modifiez la propriété **value** sous **parameters** :

    Exemple de fichier de paramètres :

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaces_name": {
          "value": "my-workspace-name"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/resource-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/workspaces/workspace-name"
        },
        "alertName": {
          "value": "my-alert-name"
        },
        "querypacks_name": {
          "value": "my-default-query-pack-name"
        }
      }
    }
    ```

9. Cliquez sur **Enregistrer** dans l’éditeur.
10. Cliquez sur **Modifier le modèle** dans la barre d’outils pour ouvrir le fichier **template.json** dans l’éditeur en ligne.
11. Pour modifier la région cible dans laquelle l’espace de travail Log Analytics sera déployé, modifiez la propriété **location** sous **resources** dans l’éditeur en ligne. Pour obtenir les codes d’emplacement des régions, consultez [Emplacements Azure](https://azure.microsoft.com/global-infrastructure/locations/). Le code d’une région est le nom de la région en anglais et sans espace, **Central US** = **centralus**.
12. Supprimez les ressources des services liées `microsoft.operationalinsights/workspaces/linkedservices` si ces derniers sont présents dans le modèle. Ils doivent être reconfigurés manuellement dans l’espace de travail cible.

    Exemple de modèle incluant l’espace de travail, la recherche enregistrée, les solutions, l’alerte et le pack de requêtes :

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaces_name": {
          "type": "String"
        },
        "workspaceResourceId": {
          "type": "String"
        },
        "alertName": {
          "type": "String"
        },
        "querypacks_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "type": "microsoft.operationalinsights/workspaces",
          "apiVersion": "2020-08-01",
          "name": "[parameters('workspaces_name')]",
          "location": "france central",
          "properties": {
            "sku": {
              "name": "pergb2018"
            },
            "retentionInDays": 30,
            "features": {
              "enableLogAccessUsingOnlyResourcePermissions": true
            },
            "workspaceCapping": {
              "dailyQuotaGb": -1
            },
            "publicNetworkAccessForIngestion": "Enabled",
            "publicNetworkAccessForQuery": "Enabled"
          }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
          "apiVersion": "2020-08-01",
          "name": "[concat(parameters('workspaces_name'), '/2b5112ec-5ad0-5eda-80e9-ad98b51d4aba')]",
          "dependsOn": [
            "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaces_name'))]"
          ],
          "properties": {
            "category": "VM Monitoring",
            "displayName": "List all versions of curl in use",
            "query": "VMProcess\n| where ExecutableName == \"curl\"\n| distinct ProductVersion",
            "tags": [],
            "version": 2
          }
        },
        {
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "2015-11-01-preview",
          "name": "[concat('Updates(', parameters('workspaces_name'))]",
          "location": "france central",
          "dependsOn": [
            "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]"
          ],
          "plan": {
            "name": "[concat('Updates(', parameters('workspaces_name'))]",
            "promotionCode": "",
            "product": "OMSGallery/Updates",
            "publisher": "Microsoft"
          },
          "properties": {
            "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]",
            "containedResources": [
              "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name')), '/views/Updates(', parameters('workspaces_name'), ')')]"
            ]
          }
        }
        {
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "2015-11-01-preview",
          "name": "[concat('VMInsights(', parameters('workspaces_name'))]",
          "location": "france central",
          "plan": {
            "name": "[concat('VMInsights(', parameters('workspaces_name'))]",
            "promotionCode": "",
            "product": "OMSGallery/VMInsights",
            "publisher": "Microsoft"
          },
          "properties": {
            "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]",
            "containedResources": [
              "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name')), '/views/VMInsights(', parameters('workspaces_name'), ')')]"
            ]
          }
        },
        {
          "type": "microsoft.insights/scheduledqueryrules",
          "apiVersion": "2021-02-01-preview",
          "name": "[parameters('alertName')]",
          "location": "france central",
          "properties": {
            "displayName": "[parameters('alertName')]",
            "severity": 3,
            "enabled": true,
            "evaluationFrequency": "PT5M",
            "scopes": [
              "[parameters('workspaceResourceId')]"
            ],
            "windowSize": "PT15M",
            "criteria": {
              "allOf": [
                {
                  "query": "Heartbeat | where computer == 'my computer name'",
                  "timeAggregation": "Count",
                  "operator": "LessThan",
                  "threshold": 14,
                  "failingPeriods": {
                    "numberOfEvaluationPeriods": 1,
                    "minFailingPeriodsToAlert": 1
                  }
                }
              ]
            },
            "autoMitigate": true,
            "actions": {}
          }
        },
        {
          "type": "Microsoft.OperationalInsights/querypacks",
          "apiVersion": "2019-09-01-preview",
          "name": "[parameters('querypacks_name')]",
          "location": "francecentral",
          "properties": {}
        },
        {
          "type": "Microsoft.OperationalInsights/querypacks/queries",
          "apiVersion": "2019-09-01-preview",
          "name": "[concat(parameters('querypacks_name'), '/00000000-0000-0000-0000-000000000000')]",
          "dependsOn": [
            "[resourceId('Microsoft.OperationalInsights/querypacks', parameters('querypacks_name'))]"
          ],
          "properties": {
            "displayName": "my-query-name",
            "body": "my-query-text",
            "related": {
              "categories": [],
              "resourceTypes": [
                  "microsoft.operationalinsights/workspaces"
              ]
            },
            "tags": {
              "labels": []
            }
          }
        }
      ]
    }
    ```

13. Cliquez sur **Enregistrer** dans l’éditeur en ligne.
14. Cliquez sur **Abonnement** pour choisir l’abonnement dans lequel l’espace de travail cible sera déployé.
16. Cliquez sur **Groupe de ressources** pour choisir le groupe de ressources dans lequel l’espace de travail cible sera déployé. Vous pouvez cliquer sur **Créer nouveau** pour créer un groupe de ressources pour l’espace de travail cible.
17. Vérifiez que la **région** est définie su l’emplacement cible où vous souhaitez déployer le groupe de sécurité réseau.
18. Cliquez sur le bouton **Vérifier + créer** pour vérifier votre modèle.
19. Cliquez sur **Créer** pour déployer l’espace de travail et la ressource sélectionnée dans la région cible.
20. Votre espace de travail, ressources sélectionnées comprises, est maintenant déployé dans la région cible, et vous pouvez terminer la configuration restante dans l’espace de travail pour récupérer les fonctionnalités de l’espace de travail original.
    - Connecter des agents : Utilisez l’une des options disponibles, notamment DCR, pour configurer les agents requis sur les machines virtuelles et les groupes de machines virtuelles identiques, et spécifiez le nouvel espace de travail cible comme destination.
    - Installer des solutions : Certaines solutions, comme [Azure Sentinel](../../sentinel/quickstart-onboard.md), nécessitent une procédure d’intégration particulière et n’ont pas été incluses dans le modèle. Vous devez les intégrer séparément au nouvel espace de travail.
    - API de collecte de données : Configurez des instances de l’API de collecte de données pour envoyer des données à l’espace de travail cible.
    - Règles d’alerte : Lorsque les alertes ne sont pas exportées dans le modèle, vous devez les configurer manuellement dans l’espace de travail cible.
21. Vérifiez que les nouvelles données ne sont pas ingérées dans l’espace de travail d’origine. Exécutez cette requête dans votre espace de travail d’origine et assurez-vous qu’il n’y a pas d’ingestion après la migration.

    ```kusto
    search *
    | summarize max(TimeGenerated) by Type
    ```

Les données ingérées après la connexion des sources de données à l’espace de travail cible sont stockées dans l’espace de travail cible, tandis que les données plus anciennes restent dans l’espace de travail d’origine. Vous pouvez effectuer une [requête sur plusieurs espaces de travail](./cross-workspace-query.md#performing-a-query-across-multiple-resources) et, si les deux espaces de travail ont reçu le même nom, utilisez le nom qualifié (*subscriptionName/resourceGroup/componentName*) dans la référence de l’espace de travail.

Exemple de requête sur deux espaces de travail portant le même nom :

```kusto
union 
  workspace('subscription-name1/<resource-group-name1/<original-workspace-name>')Update, 
  workspace('subscription-name2/<resource-group-name2/<target-workspace-name>').Update, 
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="discard"></a>Abandonner

Si vous souhaitez abandonner l’espace de travail source, supprimez les ressources exportées ou le groupe de ressources qui les contient. Pour ce faire, sélectionnez le groupe de ressources cible dans Portail Azure : si vous avez créé un nouveau groupe de ressources pour ce déploiement, cliquez sur **Supprimer le groupe de ressources** dans la barre d’outils de la page Vue d’ensemble. Si le modèle a été déployé sur un groupe de ressources existant, sélectionnez les ressources qui ont été déployées avec le modèle, puis cliquez sur **Supprimer** dans la barre d’outils.

## <a name="clean-up"></a>Nettoyage

Alors que les nouvelles données sont ingérées dans votre nouvel espace de travail, les anciennes données de l’espace de travail d’origine restent disponibles pour les requêtes et sont soumises à la stratégie de rétention définie dans l’espace de travail. Il est recommandé de conserver l’espace de travail d’origine pendant toute la durée de vie de vos données antérieures, afin de vous permettre d’effectuer des [requêtes](./cross-workspace-query.md#performing-a-query-across-multiple-resources) sur les espaces de travail. Si vous n’avez plus besoin d’accéder à des données antérieures dans l’espace de travail d’origine, sélectionnez le groupe de ressources d’origine dans le Portail Azure, sélectionnez les ressources que vous souhaitez supprimer et cliquez sur **Supprimer** dans la barre d’outils.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez déplacé un espace de travail Log Analytics et les ressources associées d’une région vers une autre et nettoyé les ressources sources. Pour en savoir plus sur le déplacement de ressources entre régions et la reprise d’activité après sinistre dans Azure, consultez :

- [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Déplacer des machines virtuelles Azure vers une autre région](../../site-recovery/azure-to-azure-tutorial-migrate.md)
