---
title: Journaux de diagnostic Azure des événements et du compteur de règles du groupe de sécurité réseau | Microsoft Docs
description: Découvrez comment activer les journaux de diagnostic des événements et du compteur de règles pour un groupe de sécurité réseau Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: jdial
ms.openlocfilehash: ee1e70e58c2f6dd15ae48c15373d4b1dc58f9328
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384987"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Journalisation des diagnostics pour un groupe de sécurité réseau

Un groupe de sécurité réseau (NSG) comprend les règles qui autorisent ou refusent le trafic vers un sous-réseau de réseau virtuel, une interface réseau, ou les deux à la fois. Lorsque vous activez la journalisation des diagnostics pour un groupe de sécurité réseau, vous pouvez consigner les catégories d’informations suivantes :

* **Événement :** les entrées sont enregistrées pour lesquelles des règles NSG sont appliquées aux machines virtuelles, en fonction de l’adresse MAC. L’état de ces règles est collecté toutes les 60 secondes.
* **Compteur de règles :** contient les entrées correspondant au nombre de fois où chaque règle NSG a été appliquée pour refuser ou autoriser le trafic.

Les journaux de diagnostic ne sont disponibles que pour les groupes de sécurité réseau déployés avec le modèle de déploiement Azure Resource Manager. Vous ne pouvez pas activer la journalisation des diagnostics pour les groupes de sécurité réseau déployés via le modèle de déploiement classique. Pour mieux comprendre ces deux modèles, consultez [Présentation des modèles de déploiement Azure](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

La journalisation des diagnostics est activée séparément pour *chaque* groupe de sécurité réseau dont vous souhaitez collecter des données de diagnostic. Si vous êtes plus intéressé par les journaux des opérations, ou ceux des activités, consultez [Journalisation des activités](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enable-logging"></a>Activation de la journalisation

Vous pouvez utiliser le [portail Azure](#azure-portal), [PowerShell](#powershell) ou [Azure CLI](#azure-cli) pour activer la journalisation des diagnostics.

### <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail](https://portal.azure.com).
2. Sélectionnez **Tous les services**, puis tapez *groupes de sécurité réseau*. Quand la mention **Groupes de sécurité réseau** apparaît dans les résultats de recherche, sélectionnez-la.
3. Sélectionnez le groupe de sécurité réseau pour lequel vous souhaitez activer la journalisation.
4. Sous **SURVEILLANCE**, sélectionnez **Journaux de diagnostic**, puis **Activer les diagnostics**, comme illustré dans l’image suivante :
 
    ![Activer les diagnostics](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. Sous **Paramètres de diagnostic**, entrez, ou sélectionnez, les informations suivantes, puis sélectionnez **Enregistrer** :

    | Paramètre                                                                                     | Valeur                                                          |
    | ---------                                                                                   |---------                                                       |
    | NOM                                                                                        | Un nom de votre choix.  Par exemple : *myNsgDiagnostics*      |
    | **Archiver dans un compte de stockage**, **Diffuser vers Event Hub** et **Envoyer à Log Analytics** | Vous pouvez sélectionner autant de destinations que vous le souhaitez. Pour en savoir plus sur chacune d’elles, consultez [Destinations des journaux](#log-destinations).                                                                                                                                           |
    | JOURNAL                                                                                         | Sélectionnez une catégorie de journal, ou les deux. Pour en savoir plus sur les données consignées pour chaque catégorie, consultez [Catégories de journal](#log-categories).                                                                                                                                             |
6. Consultez et analysez les journaux. Pour plus d’informations, voir [Afficher et analyser les journaux](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

Vous pouvez exécuter les commandes qui suivent dans [Azure Cloud Shell](https://shell.azure.com/powershell), ou en exécutant PowerShell à partir de votre ordinateur. Azure Cloud Shell est un interpréteur de commandes interactif gratuit. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Si vous exécutez PowerShell à partir de votre ordinateur, vous avez besoin du module PowerShell *AzureRM*, version 6.1.1 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` sur votre ordinateur pour trouver la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell localement, vous devez aussi exécuter `Login-AzureRmAccount` pour vous connecter à Azure avec un compte disposant des [autorisations nécessaires](virtual-network-network-interface.md#permissions).

Pour activer la journalisation des diagnostics, vous avez besoin de l’Id d’un groupe de sécurité réseau existant. Si vous ne disposez pas de groupe de sécurité réseau, vous pouvez en créer un avec [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup).

Récupérez le groupe de sécurité réseau pour lequel vous souhaitez activer la journalisation des diagnostics avec [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup). Par exemple, pour récupérer un groupe de sécurité réseau nommé *myNsg*, qui existe dans un groupe de ressources appelé *myResourceGroup*, entrez la commande suivante :

```azurepowershell-interactive
$Nsg=Get-AzureRmNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Vous pouvez écrire des journaux de diagnostic sur trois types de destinations. Pour plus d’informations, consultez [Destinations de journaux](#log-destinations). Dans cet article, les journaux sont envoyés à la destination *Log Analytics*, à titre d’exemple. Récupérez un espace de travail Log Analytics avec [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace). Par exemple, pour récupérer un espace de travail existant nommé *myWorkspace*, dans un groupe de ressources appelé *myWorkspaces*, entrez la commande suivante :

```azurepowershell-interactive
$Oms=Get-AzureRmOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Si vous n’avez pas d’espace de travail, vous pouvez en créer un avec [New-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace).

Il existe deux catégories de journalisation pour lesquelles vous pouvez activer les journaux. Pour plus d’informations, consultez [Catégories de journal](#log-categories). Activez la journalisation des diagnostics pour le groupe de sécurité réseau avec [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting). L’exemple suivant enregistre à la fois les données des catégories d’événements et de compteurs dans l’espace de travail d’un groupe de sécurité réseau, à l’aide des ID du groupe de sécurité réseau et de l’espace de travail récupérés précédemment :

```azurepowershell-interactive
Set-AzureRmDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Si vous souhaitez uniquement enregistrer les données de l’une de ces catégories, plutôt que des deux, ajoutez l’option `-Categories` à la commande précédente, suivie de *NetworkSecurityGroupEvent* ou *NetworkSecurityGroupRuleCounter*. Si vous voulez consigner les données dans une autre [destination](#log-destinations) que celle d’un espace de travail Log Analytics, utilisez les paramètres appropriés pour un [compte de stockage](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure ou [Event Hub](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Consultez et analysez les journaux. Pour plus d’informations, voir [Afficher et analyser les journaux](#view-and-analyze-logs).

### <a name="azure-cli"></a>Azure CLI

Vous pouvez exécuter les commandes qui suivent dans [Azure Cloud Shell](https://shell.azure.com/bash), ou en exécutant Azure CLI à partir de votre ordinateur. Azure Cloud Shell est un interpréteur de commandes interactif gratuit. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Si vous exécutez l’interface CLI à partir de votre ordinateur, vous avez besoin de la version 2.0.38 ou ultérieure. Exécutez `az --version` sur votre ordinateur pour trouver la version installée. Si vous devez mettre à niveau, consultez [Installation d’Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Si vous exécutez l’interface CLI localement, vous devez aussi exécuter `az login` pour vous connecter à Azure avec un compte disposant des [autorisations nécessaires](virtual-network-network-interface.md#permissions).

Pour activer la journalisation des diagnostics, vous avez besoin de l’Id d’un groupe de sécurité réseau existant. Si vous ne disposez pas de groupe de sécurité réseau, vous pouvez en créer un avec la commande [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create).

Récupérez le groupe de sécurité réseau pour lequel vous souhaitez activer la journalisation des diagnostics avec la commande [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show). Par exemple, pour récupérer un groupe de sécurité réseau nommé *myNsg*, qui existe dans un groupe de ressources appelé *myResourceGroup*, entrez la commande suivante :

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Vous pouvez écrire des journaux de diagnostic sur trois types de destinations. Pour plus d’informations, consultez [Destinations de journaux](#log-destinations). Dans cet article, les journaux sont envoyés à la destination *Log Analytics*, à titre d’exemple. Pour plus d’informations, consultez [Catégories de journal](#log-categories). 

Activez la journalisation des diagnostics pour le groupe de sécurité réseau avec la commande [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create). L’exemple suivant journalise les données de catégorie événement et compteur dans un espace de travail existant nommé *myWorkspace*, qui se trouve dans un groupe de ressources nommé *myWorkspaces*, et l’ID de groupe de sécurité réseau que vous avez récupéré :

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Si vous n’avez pas d’espace de travail, vous pouvez en créer un à l’aide du [portail Azure](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou de [PowerShell](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace). Il existe deux catégories de journalisation pour lesquelles vous pouvez activer les journaux. 

Si vous souhaitez journaliser les données d’une seule des deux catégories, supprimez l’autre catégorie de la commande précédente. Si vous voulez consigner les données dans une autre [destination](#log-destinations) que celle d’un espace de travail Log Analytics, utilisez les paramètres appropriés pour un [compte de stockage](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure ou [Event Hub](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Consultez et analysez les journaux. Pour plus d’informations, voir [Afficher et analyser les journaux](#view-and-analyze-logs).

## <a name="log-destinations"></a>Destinations des journaux

Les données de diagnostic peuvent être :
- [Écrites dans un compte de stockage Azure](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), pour l’audit ou l’inspection manuelle. Vous pouvez spécifier la durée de rétention (en jours) à l’aide des paramètres de diagnostic des ressources.
- [Diffusées vers un hub d’événements](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour qu’un service tiers ou une solution d’analyse personnalisée (p. ex. PowerBI) les ingère.
- [Écrites dans Azure Log Analytics](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-diagnostics-direct-to-log-analytics).

## <a name="log-categories"></a>Catégories de journal

Les données au format JSON sont écrites pour les catégories de journal suivantes :

### <a name="event"></a>Événement

Le journal des événements contient des informations sur les règles de groupe de sécurité réseau qui sont appliquées aux machines virtuelles, en fonction de l’adresse MAC. Les données suivantes sont enregistrées pour chaque événement. Dans l’exemple suivant, les données sont enregistrées pour une machine virtuelle avec l’adresse IP 192.168.1.4 et l’adresse MAC 00-0D-3A-92-6A-7C :

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "priority":[PRIORITY-SPECIFIED-IN-RULE],
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "conditions":{
            "protocols":"[PROTOCOLS-SPECIFIED-IN-RULE]",
            "destinationPortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourcePortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourceIP":"[SOURCE-IP-OR-RANGE-SPECIFIED-IN-RULE]",
            "destinationIP":"[DESTINATION-IP-OR-RANGE-SPECIFIED-IN-RULE]"
            }
        }
}
```

### <a name="rule-counter"></a>Compteur de règles

Le journal du compteur de règles contient des informations sur chacune des règles appliquées aux ressources. Les données de l’exemple suivant sont enregistrées chaque fois qu’une règle est appliquée. Dans l’exemple suivant, les données sont enregistrées pour une machine virtuelle avec l’adresse IP 192.168.1.4 et l’adresse MAC 00-0D-3A-92-6A-7C :

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> L’adresse IP source pour la communication n’est pas enregistrée. En revanche, vous pouvez activer l’[enregistrement des flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md) pour un groupe de sécurité réseau ; il enregistre toutes les informations de compteur de règles, ainsi que l’adresse IP source qui a initié la communication. Les données du journal de flux NSG sont écrites dans un compte Stockage Azure. Vous pouvez analyser les données avec la fonctionnalité d’[analytique du trafic](../network-watcher/traffic-analytics.md) d’Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Afficher et analyser les journaux

Pour savoir comment voir les données des journaux de diagnostic, consultez [Vue d’ensemble des journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Si vous envoyez des données de diagnostic à :
- **Log Analytics** : vous pouvez utiliser la solution d’[analytique de groupe de sécurité réseau](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-log-analytics
) pour obtenir des insights plus poussés. La solution offre des visualisations pour les règles NSG qui autorisent ou refusent le trafic, par adresse MAC, de l’interface réseau sur une machine virtuelle.
- **Compte Stockage Azure** : les données sont écrites dans un fichier PT1H.json. Vous pouvez trouver le :
    - Journal des événements dans le chemin suivant : `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
    - Journal du compteur de règles dans le chemin suivant : `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [Journalisation des activités](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), appelée auparavant « audit » ou « journaux des opérations ». La journalisation des activités est activée par défaut pour les groupes de sécurité réseau créés au moyen d’un modèle de déploiement Azure. Pour déterminer les opérations qui ont été effectuées sur les groupes de sécurité réseau dans le journal d’activité, recherchez les entrées qui contiennent les types de ressources suivants :
    - Microsoft.ClassicNetwork/networkSecurityGroups
    - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
    - Microsoft.Network/networkSecurityGroups
    - Microsoft.Network/networkSecurityGroups/securityRules
- Pour savoir comment enregistrer les informations de diagnostic et y inclure l’adresse IP source de chaque flux, consultez [Journalisation des flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
