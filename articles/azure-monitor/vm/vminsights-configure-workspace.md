---
title: Configurer l’espace de travail Log Analytics pour VM Insights
description: Décrit comment créer et configurer l’espace de travail Log Analytics utilisé par VM Insights.
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.openlocfilehash: 5a0e04772cfc1c9be77c0ad8b32b0e93be8b9f54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046718"
---
# <a name="configure-log-analytics-workspace-for-vm-insights"></a>Configurer l’espace de travail Log Analytics pour VM Insights
VM Insights collecte ses données à partir d’un ou plusieurs espaces de travail Log Analytics dans Azure Monitor. Avant d’intégrer des agents, vous devez créer et configurer un espace de travail. Cet article décrit les exigences de l’espace de travail et sa configuration pour VM Insights.

## <a name="overview"></a>Vue d’ensemble
Un abonnement unique peut utiliser n’importe quel nombre d’espaces de travail en fonction de vos besoins. Toutefois, il existe un impératif : l’espace de travail doit se trouver dans un emplacement pris en charge et doit être configuré avec la solution *VMInsights*.

Une fois l’espace de travail configuré, vous pouvez utiliser l’une des options disponibles afin d’installer les agents nécessaires sur la machine virtuelle et le groupe de machines virtuelles identiques, puis spécifier un espace de travail pour leur permettre d’envoyer leurs données. VM Insights collectera les données de tout espace de travail configuré dans son abonnement.

> [!NOTE]
> Quand vous activez VMSS sur une seule machine virtuelle ou un seul groupe de machines virtuelles identiques via le portail Azure, vous pouvez sélectionner un espace de travail existant ou en créer un. La solution *VMInsights* sera installée dans cet espace de travail, si ce n’est déjà fait. Vous pouvez ensuite utiliser cet espace de travail pour d’autres agents.


## <a name="create-log-analytics-workspace"></a>Créer un espace de travail Log Analytics

>[!NOTE]
>Les informations décrites dans cette section s’appliquent également à la solution [Service Map](service-map.md). 

Accédez aux espace de travail Log Analytics dans le portail Azure à partir du menu **Espaces de travail Log Analytics**.

[![Espaces de travail Log Analytics](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

Vous pouvez créer un espace de travail Log Analytics à l’aide de l’une des méthodes suivantes. Consultez [Conception de votre déploiement de journaux Azure Monitor](../logs/design-logs-deployment.md) pour obtenir des conseils sur la détermination du nombre d'espaces de travail que vous devez utiliser dans votre environnement et sur la façon de concevoir leur stratégie d'accès.


* [Azure portal](../logs/quick-create-workspace.md)
* [Azure CLI](../logs/quick-create-workspace-cli.md)
* [PowerShell](../logs/powershell-workspace-configuration.md)
* [Azure Resource Manager](../logs/resource-manager-workspace.md)

## <a name="supported-regions"></a>Régions prises en charge
VM Insights prend en charge tout espace de travail Log Analytics dans les [régions prises en charge par Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all), à l’exception des suivantes :

- Allemagne Centre-Ouest
- Centre de la Corée

>[!NOTE]
>Vous pouvez surveiller les machines virtuelles Azure de n’importe quelle région. Les machines virtuelles elles-même ne sont pas limitées aux régions prises en charge par l’espace de travail Log Analytics.

## <a name="azure-role-based-access-control"></a>Contrôle d'accès en fonction du rôle Azure
Pour activer les fonctionnalités et y accéder dans VM Insights, vous devez avoir le rôle [Contributeur Log Analytics](../logs/manage-access.md#manage-access-using-azure-permissions) dans l’espace de travail. Pour afficher les données de performances, d’intégrité et de mappage, vous devez avoir le rôle [Lecteur d’analyse](../roles-permissions-security.md#built-in-monitoring-roles) pour la machine virtuelle Azure. Pour plus d’informations sur la façon de contrôler l’accès à un espace de travail Log Analytics, consultez [Gérer les espaces de travail](../logs/manage-access.md).

## <a name="add-vminsights-solution-to-workspace"></a>Ajout de la solution VMInsights à l’espace de travail
Pour pouvoir utiliser un espace de travail Log Analytics avec VM Insights, la solution *VMInsights* doit être installée. Les méthodes de configuration de l’espace de travail sont décrites dans les sections suivantes.

> [!NOTE]
> Lorsque vous ajoutez la solution *VMInsights* à l’espace de travail, toutes les machines virtuelles existantes connectées à l’espace de travail commencent à envoyer des données à InsightsMetrics. Les données des autres types de données ne seront pas collectées tant que vous n'aurez pas ajouté Dependency Agent aux machines virtuelles existantes connectées à l'espace de travail.

### <a name="azure-portal"></a>Portail Azure
Il existe trois options pour configurer un espace de travail existant à l’aide du portail Azure. Chaque variable est décrite ci-après.

Pour configurer un seul espace de travail, accédez à l’option **Machines virtuelles** dans le menu **Azure Monitor**, puis sélectionnez **Autres options d’intégration** et **Configurer un espace de travail**. Sélectionnez un abonnement et un espace de travail, puis cliquez sur **Configurer**.

[![Configurer l’espace de travail](../vm/media/vminsights-enable-policy/configure-workspace.png)](../vm/media/vminsights-enable-policy/configure-workspace.png#lightbox)

Pour configurer plusieurs espaces de travail, sélectionnez l’onglet **Configuration de l’espace de travail** dans le sous-menu **Machines virtuelles** du menu **Monitor** du portail Azure. Définissez les valeurs de filtre pour afficher la liste des espaces de travail existants. Cochez la case en regard de chaque espace de travail à activer, puis cliquez sur **Configurer la sélection**.

[![Configuration de l’espace de travail](../vm/media/vminsights-enable-policy/workspace-configuration.png)](../vm/media/vminsights-enable-policy/workspace-configuration.png#lightbox)


Quand vous activez VMSS sur une seule machine virtuelle ou un seul groupe de machines virtuelles identiques via le portail Azure, vous pouvez sélectionner un espace de travail existant ou en créer un. La solution *VMInsights* sera installée dans cet espace de travail, si ce n’est déjà fait. Vous pouvez ensuite utiliser cet espace de travail pour d’autres agents.

[![Activer une machine virtuelle unique dans le portail](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Modèle Resource Manager
Les modèles Azure Resource Manager pour VM Insights sont fournis dans un fichier d’archive (.zip) que vous pouvez [télécharger à partir de notre dépôt GitHub](https://aka.ms/VmInsightsARMTemplates). Celui-ci inclut un modèle appelé **ConfigureWorkspace** qui configure un espace de travail Log Analytics pour VM Insights. Vous déployez ce modèle à l’aide de l’une des méthodes standard, y compris les exemples de commandes PowerShell et CLI ci-dessous : 

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>Étapes suivantes
- Pour connecter des agents à des VM Insights, consultez [Intégrer des agents à VM Insights](vminsights-enable-overview.md).
- Consultez [Ciblage des solutions de supervision dans Azure Monitor (préversion)](../insights/solution-targeting.md) pour limiter la quantité de données envoyées d'une solution à l'espace de travail.
