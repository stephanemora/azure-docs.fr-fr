---
title: Diffuser en continu les journaux de diagnostic Azure vers Log Analytics
description: Découvrez comment diffuser en continu les journaux de diagnostic Azure vers un espace de travail Log Analytics.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: 009ad79e6e0bddaa5d736e7375fad80ae015f8c5
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53578699"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Diffuser en continu les journaux de diagnostic Azure vers Log Analytics

**[Les journaux de diagnostic Azure](diagnostic-logs-overview.md)** peuvent être diffusés en continu quasiment en temps réel vers Azure Log Analytics à l’aide du portail, des applets de commande PowerShell ou de l’interface de ligne de commande Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>Ce que vous pouvez faire avec les journaux de diagnostic dans Log Analytics

Azure Log Analytics est un outil d’analytique et de recherche de journaux flexible qui vous offre une meilleure visibilité sur les données de journal brutes générées à partir des ressources Azure. Il inclut, entre autres, les fonctionnalités suivantes :

* **Recherche dans les journaux** : écrivez des requêtes avancées pour rechercher des données de journal spécifiques, mettez en corrélation des journaux à partir de diverses sources, ou encore générez des graphiques qui peuvent être épinglés à votre tableau de bord Azure.
* **Génération d’alertes** : soyez informé par e-mail ou appel webhook lorsqu’un ou plusieurs événements correspond(ent) à une requête particulière.
* **Solutions** : utilisez des tableaux de bord et des vues prédéfinies pour bénéficier d’une meilleure visibilité sur vos données de journal.
* **Analytique avancée** : appliquez des algorithmes de Machine Learning et de correspondance à des critères spéciaux pour identifier d’éventuels problèmes consignés dans vos journaux.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Activer la diffusion en continu des journaux de diagnostic vers Log Analytics

Vous pouvez activer la diffusion en continu des journaux de diagnostic par programme, via le portail ou à l’aide des [API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). Dans tous les cas, vous créez un paramètre de diagnostic dans lequel vous spécifiez un espace de travail Log Analytics et les catégories de journal et les indicateurs de performance que vous voulez envoyer dans cet espace de travail. Une **catégorie de journal** de diagnostic est un type de journal qu’une ressource peut générer.

Il n’est pas nécessaire que l’espace de travail Log Analytics se trouve dans le même abonnement que la ressource générant des journaux, à condition que l’utilisateur qui configure le paramètre ait un accès RBAC approprié aux deux abonnements.

> [!NOTE]
> L’envoi de métriques multidimensionnelles via les paramètres de diagnostic n’est pas pris en charge actuellement. Les métriques à plusieurs dimensions sont exportées en tant que métriques dimensionnelles uniques aplaties, puis agrégées dans les valeurs de la dimension.
>
> *Par exemple* : La métrique « Messages entrants » sur un Event Hub peut être examinée et représentée sur un niveau par file d’attente. Toutefois, lors de l’exportation via les paramètres de diagnostic, la métrique est représentée sous la forme de tous les messages entrants, dans toutes les files d’attente de l’Event Hub.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Diffuser en continu les journaux de diagnostic à l’aide du portail
1. Dans le portail, accédez à Azure Monitor, puis cliquez sur **Paramètres de diagnostic**

    ![Section Surveillance d’Azure Monitor](media/diagnostic-logs-stream-log-store/diagnostic-settings-blade.png)

2. Vous pouvez également filtrer la liste par type ou groupe de ressources, puis cliquez sur la ressource pour laquelle vous souhaitez définir un paramètre de diagnostic.

3. S’il n’existe aucun paramètre sur la ressource que vous avez sélectionnée, vous êtes invité à créer un paramètre. Cliquez sur « Activer les diagnostics ».

   ![Ajouter le paramètre de diagnostic - aucun paramètre existant](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   S’il existe des paramètres existants sur la ressource, vous verrez une liste de paramètres déjà configurés sur cette ressource. Cliquez sur « Ajouter le paramètre de diagnostic ».

   ![Ajouter le paramètre de diagnostic - paramètres existants](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. Donnez un nom à votre définition, cochez la case **Envoyer à Log Analytics**, puis sélectionnez un espace de travail Log Analytics.

   ![Ajouter le paramètre de diagnostic - paramètres existants](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. Cliquez sur **Enregistrer**.

Après quelques instants, le nouveau paramètre apparaît dans la liste des paramètres de cette ressource, et les journaux de diagnostic sont diffusés en continu dans cet espace de travail dès que de nouvelles données d’événement sont générées. Notez qu’un délai de quinze minutes peut s’écouler entre l’événement et sa consignation dans Log Analytics.

### <a name="via-powershell-cmdlets"></a>Via les applets de commande PowerShell
Pour activer la diffusion en continu via les [applets de commande Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md), vous pouvez utiliser l’applet de commande `Set-AzureRmDiagnosticSetting` avec ces paramètres :

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Notez que la propriété workspaceID tient compte de l’ID complet de la ressource Azure dans l’espace de travail, et non de la clé/de l’ID de l’espace de travail affiché(e) dans le portail Log Analytics.

### <a name="via-azure-cli"></a>Via l’interface de ligne de commande Azure

Pour activer la diffusion en continu par le biais [d’Azure CLI](../../azure-monitor/platform/cli-samples.md), vous pouvez utiliser la commande [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Vous pouvez ajouter des catégories supplémentaires dans le journal de diagnostic par l’adjonction de dictionnaires au tableau JSON transmis en tant que paramètre `--logs`.

L’argument `--resource-group` est obligatoire seulement si `--workspace` n’est pas un ID d’objet.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Comment faire pour interroger les données dans Log Analytics ?

Dans le panneau Recherche dans les journaux du portail ou dans la fonctionnalité Analytique avancée accessible depuis Log Analytics, vous pouvez interroger les journaux de diagnostic dans le cadre de la solution de gestion des journaux au niveau de la table AzureDiagnostics. Il existe également [plusieurs solutions pour les ressources Azure](../../azure-monitor/insights/solutions.md) que vous pouvez installer pour avoir une visibilité immédiate sur les données de journal que vous envoyez vers Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les journaux de diagnostic Azure](diagnostic-logs-overview.md)
