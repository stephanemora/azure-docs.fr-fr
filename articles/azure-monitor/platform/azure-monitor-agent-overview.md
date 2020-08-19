---
title: Vue d’ensemble de l’agent Azure Monitor
description: Vue d’ensemble de l’agent Azure Monitor (AMA), qui collecte des données de supervision à partir du système d’exploitation invité des machines virtuelles.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: e38d59ff1eb31dd5fc3ecf6b7df6b12504141d5e
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88082787"
---
# <a name="azure-monitor-agent-overview-preview"></a>Vue d’ensemble de l’agent Azure Monitor (préversion)
L’agent Azure Monitor (AMA) collecte des données de supervision à partir du système d’exploitation invité des machines virtuelles et les remet à Azure Monitor. Cet article fournit une vue d’ensemble de l’agent Azure Monitor, notamment son installation et la configuration de la collecte de données.

## <a name="relationship-to-other-agents"></a>Relation avec les autres agents
L’agent Azure Monitor remplace les agents suivants qui sont actuellement utilisés par Azure Monitor pour collecter des données d’invités à partir de machines virtuelles :

- [Agent Log Analytics](log-analytics-agent.md) : envoie des données à l’espace de travail Log Analytics et prend en charge les solutions d’Azure Monitor pour machines virtuelles et de surveillance.
- [Extension Diagnostic](diagnostics-extension-overview.md) : envoie des données aux Métriques Azure Monitor (Windows uniquement), à Azure Event Hubs et au Stockage Azure.
- [Agent Telegraf](collect-custom-metrics-linux-telegraf.md) : envoie des données aux métriques Azure Monitor (Linux uniquement).

En plus de consolider cette fonctionnalité en un seul agent, l’agent Azure Monitor offre les avantages suivants par rapport aux agents existants :

- Étendue de l’analyse. Configurez de manière centralisée un regroupement de différents jeux de données à partir de différents ensembles de machines virtuelles.  
- Multihébergement Linux : Envoyer des données à partir de machines virtuelles Linux vers plusieurs espaces de travail.
- Filtrage d’événements Windows : Utilisez des requêtes XPATH pour filtrer les événements Windows qui sont collectés.
- Gestion améliorée des extensions : L’agent Azure Monitor utilise une nouvelle méthode de gestion de l’extensibilité qui est plus transparente et contrôlable que les packs d’administration et les plug-ins Linux dans les agents Log Analytics actuels.

### <a name="changes-in-data-collection"></a>Modifications de la collecte de données
Les méthodes permettant de définir la collecte de données pour les agents existants diffèrent les unes des autres, et chacune d’entre elles présente des défis qui sont résolus avec l’agent Azure Monitor.

- L’agent Log Analytics obtient sa configuration à partir d’un espace de travail Log Analytics. La configuration de manière centralisée est facile, mais il est difficile de définir des définitions indépendantes pour des machines virtuelles différentes. Il peut uniquement envoyer des données à un espace de travail Log Analytics.

- L’extension de diagnostic a une configuration pour chaque machine virtuelle. Définir des définitions indépendantes pour différentes machines virtuelles est facile, mais il est difficile de les gérer de manière centralisée. Il peut uniquement envoyer des données aux métriques Azure Monitor, à Azure Event Hubs ou au Stockage Azure. Pour les agents Linux, l’agent Telegraf open source est nécessaire pour envoyer des données aux métriques Azure Monitor.

L’agent Azure Monitor utilise des [Règles de collecte de données (DCR)](data-collection-rule-overview.md) pour configurer les données à collecter à partir de chaque agent. Les règles de collecte de données permettent de gérer les paramètres de regroupement à grande échelle tout en autorisant des configurations uniques et étendues pour les sous-ensembles d’ordinateurs. Elles ne dépendent pas de l’espace de travail et de la machine virtuelle, ce qui leur permet d’être définies une fois réutilisées par les ordinateurs et environnements. Consultez [Configurer la collecte de données pour l’agent Azure Monitor (version préliminaire)](data-collection-rule-azure-monitor-agent.md).



## <a name="current-limitations"></a>Limites actuelles
Les limitations suivantes s’appliquent pendant la préversion publique de l’Agent Azure Monitor :

- L’agent Azure Monitor ne prend pas en charge les solutions et les insights telles qu’Azure Monitor pour machines virtuelles et Azure Security Center. Le seul scénario actuellement pris en charge consiste à collecter des données à l’aide des règles de collecte de données que vous configurez. 
- Les règles de collecte de données doivent être créées dans la même région que n’importe quel espace de travail Log Analytics utilisé comme destination.
- Seules les machines virtuelles Azure sont actuellement prises en charge. Les machines virtuelles locales, les groupes de machines virtuelles identiques, l’arc pour les serveurs, le service Azure Kubernetes Service et d’autres types de ressources de calcul ne sont pas pris en charge actuellement.
- La machine virtuelle doit avoir accès aux points de terminaison HTTPS suivants :
  - *.ods.opinsights.azure.com
  - *.ingest.monitor.azure.com
  - *.control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>Coexistence avec d’autres agents
L’agent Azure Monitor peut coexister avec les agents existants afin que vous puissiez continuer à utiliser leurs fonctionnalités existantes lors de l’évaluation ou de la migration. Ceci est particulièrement important en raison des limitations de la préversion publique dans la prise en charge des solutions existantes. Vous devez être prudent lors de la collecte de données en double, car cela peut fausser les résultats de la requête et entraîner des frais supplémentaires pour l’ingestion et la rétention de données.

Par exemple, Azure Monitor pour machines virtuelles utilise l’agent Log Analytics pour envoyer des données de performances à un espace de travail Log Analytics. Vous avez peut-être également configuré l’espace de travail pour collecter les événements Windows et les événements Syslog à partir d’agents. Si vous installez l’agent Azure Monitor et que vous créez une règle de collecte de données pour ces mêmes événements et données de performances, des données dupliquées sont générées.


## <a name="costs"></a>Coûts
Il n’y a aucun coût lié à l’agent Azure Monitor, mais vous pouvez être facturé pour les données ingérées. Pour plus d’informations sur la collecte et la rétention de données Log Analytics et les mesures client, consultez [Tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="data-sources-and-destinations"></a>Sources et destinations de données
Le tableau suivant répertorie les types de données que vous pouvez actuellement collecter avec l’agent Azure Monitor à l’aide de règles de collecte de données et de l’emplacement où vous pouvez envoyer ces données. Consultez [Quels sont les éléments supervisés par Azure Monitor ?](../monitor-reference.md) pour obtenir la liste des analyses, des solutions et d’autres solutions qui utilisent l’agent Azure Monitor pour collecter d’autres types de données.


L’agent Azure Monitor envoie des données aux métriques Azure Monitor ou à un espace de travail Log Analytics qui prend en charge les journaux d’Azure Monitor.

| source de données | Destinations | Description |
|:---|:---|:---|
| Performances        | Métriques Azure Monitor<br>Espace de travail Log Analytics | Valeurs numériques mesurant les performances de différents aspects du système d’exploitation et des charges de travail. |
| Journaux d'événements Windows | Espace de travail Log Analytics | Informations envoyées au système de journalisation des événements Windows. |
| syslog             | Espace de travail Log Analytics | Informations envoyées au système de journalisation des événements Linux. |


## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge
Les systèmes d’exploitation suivants sont actuellement pris en charge par l’agent Azure Monitor.

### <a name="windows"></a>Windows 
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012
  - Windows Server 2012 R2

### <a name="linux"></a>Linux
  - CentOS 6<sup>1</sup>, 7
  - Debian 9, 10
  - Oracle Linux 6<sup>1</sup>, 7
  - RHEL 6<sup>1</sup>, 7, 8
  - SLES 11, 12, 15
  - Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS

> [!IMPORTANT]
> <sup>1</sup>Pour que ces distributions envoient des données Syslog, vous devez supprimer rsyslog et installer syslog-ng.


## <a name="security"></a>Sécurité
L’agent Azure Monitor n’a besoin d’aucune clé, mais nécessite une [identité managée affectée par le système](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity). Vous devez disposer d’une identité managée affectée par le système activée sur chaque machine virtuelle avant de déployer l’agent.


## <a name="install-the-azure-monitor-agent"></a>Installer l’agent Azure Monitor
L’agent Azure Monitor est implémenté en tant qu’[extension de machine virtuelle Azure](../../virtual-machines/extensions/overview.md) avec les détails dans le tableau suivant. 

| Propriété | Windows | Linux |
|:---|:---|:---|
| Serveur de publication | Microsoft.Azure.Monitor  | Microsoft.Azure.Monitor |
| Type      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 0.9 |

Installez l’agent Azure Monitor à l’aide de l’une des méthodes d’installation des agents de machine virtuelle, y compris les éléments suivants à l’aide de PowerShell ou de l’interface CLI. Vous pouvez également installer l’agent et configurer la collecte de données sur les machines virtuelles de votre abonnement Azure à l’aide du portail avec la procédure décrite dans [Configurer la collecte de données pour l’agent Azure Monitor (préversion)](data-collection-rule-azure-monitor-agent.md#create-using-the-azure-portal).

### <a name="windows"></a>Windows

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI1)

```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --version 1.0 --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell1)

```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -Version 1.0 -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---


### <a name="linux"></a>Linux

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI2)

```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --version 0.9 --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell2)

```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -Version 0.9 -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---

## <a name="next-steps"></a>Étapes suivantes

- [Créez une règle de collecte de données](data-collection-rule-azure-monitor-agent.md) pour collecter des données à partir de l’agent et les envoyer à Azure Monitor.
