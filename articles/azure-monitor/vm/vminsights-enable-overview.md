---
title: Activer la vue d’ensemble de VM Insights
description: Découvrez comment déployer et configurer VM Insights. Découvrez la configuration système requise.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions
ms.openlocfilehash: bb2e12082b80c397eec27409b1177379a92fdd7d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102634156"
---
# <a name="enable-vm-insights-overview"></a>Activer la vue d’ensemble de VM Insights

Cet article fournit une vue d’ensemble des options disponibles pour permettre à VM Insights de surveiller l’intégrité et les performances des éléments suivants :

- Machines virtuelles Azure 
- Groupes de machines virtuelles identiques Azure
- Machines virtuelles hybrides connectées avec Azure Arc
- Machines virtuelles locales
- Machines virtuelles hébergées dans un autre environnement cloud.  

Pour configurer VM Insights :

* Activez une seule machine virtuelle Azure, un seul groupe de machines virtuelles identiques Azure ou une seule machine Azure Arc en sélectionnant **Insights** directement à partir du menu correspondant dans le portail Azure.
* Activez plusieurs machines virtuelles Azure, plusieurs groupes de machines virtuelles identiques Azure ou plusieurs machines Azure Arc à l’aide d’Azure Policy. Cette méthode vérifie que, sur des machines virtuelles et des groupes identiques Azure, les dépendances requises sont installées et correctement configurées. Les machines virtuelles et les groupes identiques non conformes sont signalés pour vous permettre de décider si vous souhaitez les activer et les corriger.
* Activez plusieurs machines virtuelles Azure, plusieurs machines virtuelles Azure Arc, plusieurs groupes de machines virtuelles identiques Azure ou plusieurs machines Azure Arc sur un abonnement ou un groupe de ressources spécifique à l’aide de PowerShell.
* Activez VM Insights pour surveiller des machines virtuelles ou ordinateurs physiques hébergés dans votre réseau d’entreprise ou un autre environnement cloud.

## <a name="supported-machines"></a>Machines prises en charge
VM Insights prend en charge les machines suivantes :

- Machine virtuelle Azure
- Groupe de machines virtuelles identiques Azure
- Machine virtuelle hybride connectée avec Azure Arc


## <a name="supported-azure-arc-machines"></a>Machines Azure Arc prises en charge
VM Insights est disponible pour les serveurs avec Azure Arc dans les régions où le service d’extension Arc est disponible. Vous devez exécuter la version 0.9 ou une version supérieure de l’agent Arc.

| Source connectée | Prise en charge | Description |
|:--|:--|:--|
| Agents Windows | Oui | Outre l’agent [Log Analytics pour Windows](../agents/log-analytics-agent.md), les agents Windows ont besoin du Dependency Agent. Pour plus d’informations, voir [Systèmes d’exploitation pris en charge](../agents/agents-overview.md#supported-operating-systems). |
| Agents Linux | Oui | Outre l’agent [Log Analytics pour Linux](../agents/log-analytics-agent.md), les agents Linux ont besoin du Dependency Agent. Pour plus d’informations, voir [Systèmes d’exploitation pris en charge](#supported-operating-systems). |
| Groupe d’administration Microsoft System Center Operations Manager | Non | |

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

VM Insights prend en charge tout système d’exploitation prenant en charge l’agent Log Analytics et l’agent Dependency. Pour une liste complète, consultez [Vue d’ensemble des agents Azure Monitor](../agents/agents-overview.md#supported-operating-systems).

> [!IMPORTANT]
> La fonctionnalité d’intégrité des invités de VM Insights prend en charge un nombre plus restreint de systèmes d’exploitation quand elle est en préversion publique. Pour une liste détaillée, consultez [Activer l’intégrité des invités de VM Insights (préversion)](../vm/vminsights-health-enable.md).

### <a name="linux-considerations"></a>Considérations relatives à Linux
Consultez la liste suivante de considérations sur la prise en charge par Linux de l’agent Dependency qui prend en charge VM Insights :

- Seules les versions du noyau SMP Linux et par défaut sont prises en charge.
- Les versions non standard du noyau, par exemple Physical Address Extension (PAE) et Xen, ne sont prises en charge par aucune distribution Linux. Par exemple, un système avec la chaîne de version *2.6.16.21-0.8-xen* n’est pas pris en charge.
- Les noyaux personnalisés, y compris les recompilations de noyaux standard, ne sont pas pris en charge.
- Pour les distributions Debian autres que la version 9.4, la fonctionnalité de mappage n’est pas prise en charge et la fonctionnalité Performances n’est disponible qu’à partir du menu Azure Monitor. Elle n’est pas directement accessible à partir du volet gauche de la machine virtuelle Azure.
- Le noyau CentOSPlus est pris en charge.

Le noyau Linux doit être corrigé pour les vulnérabilités Spectre et Meltdown. Pour plus d’informations, consultez le fournisseur de votre distribution Linux. Exécutez la commande suivante pour vérifier si les vulnérabilités Spectre/Meltdown ont été atténuées :

```
$ grep . /sys/devices/system/cpu/vulnerabilities/*
```

La sortie de cette commande doit ressembler à ce qui suit et spécifier si un ordinateur est vulnérable à l’un ou l’autre problème. Si ces fichiers sont introuvables, la machine n’a pas été corrigée.

```
/sys/devices/system/cpu/vulnerabilities/meltdown:Mitigation: PTI
/sys/devices/system/cpu/vulnerabilities/spectre_v1:Vulnerable
/sys/devices/system/cpu/vulnerabilities/spectre_v2:Vulnerable: Minimal generic ASM retpoline
```


## <a name="log-analytics-workspace"></a>Espace de travail Log Analytics
VM Insights requiert un espace de travail Log Analytics. Pour plus d’informations sur la configuration de cet espace de travail, consultez [Configurer l’espace de travail Log Analytics pour VM Insights](vminsights-configure-workspace.md).
## <a name="agents"></a>Agents
VM Insights nécessite l’installation des deux agents suivants sur chaque machine virtuelle ou groupe de machines virtuelles identiques à surveiller. Pour intégrer la ressource, installez ces agents et connectez-les à l’espace de travail.  Pour connaître la configuration réseau requise pour ces agents, consultez [Configuration requise pour le réseau](../agents/log-analytics-agent.md#network-requirements).

- [Agent Log Analytics](../agents/log-analytics-agent.md). Collecte les événements et les données de performances de la machine virtuelle ou du groupe de machines virtuelles identiques et les livre à l’espace de travail Log Analytics. Les méthodes de déploiement de l’agent Log Analytics sur les ressources Azure utilisent l’extension de machine virtuelle pour [Windows](../../virtual-machines/extensions/oms-windows.md) et [Linux](../../virtual-machines/extensions/oms-linux.md).
- Agent de dépendances. Collecte les données découvertes concernant les processus exécutés sur la machine virtuelle et les dépendances de processus externes, qui sont utilisées par la [fonctionnalité de mappage dans VM Insights](../vm/vminsights-maps.md). Il s’appuie sur l’agent Log Analytics pour remettre ses données à Azure Monitor. Les méthodes de déploiement de l’agent Dependency Agent sur les ressources Azure utilisent l’extension de machine virtuelle pour [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) et [Linux](../../virtual-machines/extensions/agent-dependency-linux.md).

> [!NOTE]
> L’agent Log Analytics est le même agent que celui utilisé par Operations Manager. VM Insights peut surveiller des agents qui sont également surveillés par Operations Manager s’ils sont connectés directement, et que vous y installez Dependency Agent. Les agents connectés à Azure Monitor via une [connexion de groupe d’administration](../tform/../agents/om-agents.md) ne peuvent pas être surveillés par VM Insights.

Voici plusieurs méthodes pour déployer ces agents. 

| Méthode | Description |
|:---|:---|
| [Azure portal](../vm/vminsights-enable-portal.md) | Installez les deux agents sur une seule machine virtuelle, des machines virtuelles hybrides ou un groupe de machines virtuelles identiques connectés à Azure Arc. |
| [Modèles Microsoft Azure Resource Manager](../vm/vminsights-enable-resource-manager.md) | Installez les deux agents à l’aide de l’une des méthodes prises en charge pour déployer un modèle Resource Manager, notamment l’interface CLI et PowerShell. |
| [Azure Policy](../vm/vminsights-enable-policy.md) | Attribuez une initiative Azure Policy pour installer automatiquement les agents lors de la création d’une machine virtuelle ou d’un groupe de machines virtuelles identiques. |
| [Installation manuelle](../vm/vminsights-enable-hybrid.md) | Installez les agents dans le système d’exploitation invité sur les ordinateurs hébergés en dehors d’Azure, notamment dans votre centre de centres ou dans d’autres environnements clouds. |


## <a name="network-requirements"></a>Configuration requise pour le réseau

- Pour connaître la configuration réseau requise pour l’agent Log Analytics, consultez [Configuration requise pour le réseau](../agents/log-analytics-agent.md#network-requirements).
- L’agent de dépendances nécessite une connexion de la machine virtuelle à l’adresse 169.254.169.254. Il s’agit du point de terminaison de service de métadonnées Azure. Vérifiez que les paramètres du pare-feu autorisent les connexions à ce point de terminaison.


## <a name="management-packs"></a>Packs d’administration
Quand un espace de travail Log Analytics est configuré pour VM Insights, deux packs d’administration sont transférés à tous les ordinateurs Windows connectés à cet espace de travail. Les packs d’administration se nomment *Microsoft.IntelligencePacks.ApplicationDependencyMonitor* et *Microsoft.IntelligencePacks.VMInsights*, et sont écrits dans *%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs*. 

La source de données utilisée par le pack d’administration *ApplicationDependencyMonitor* est * *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. La source de données utilisée par le pack d’administration *VMInsights* est *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Données relatives aux diagnostics et à l’utilisation

Microsoft collecte automatiquement les données sur l’utilisation et les performances via votre utilisation du service Azure Monitor. Microsoft utilise ces données pour fournir et améliorer la qualité, la sécurité et l’intégrité du service. 

Pour fournir des capacités de dépannage précises et efficaces, la fonctionnalité Map inclut des données sur la configuration de votre logiciel. Les données fournissent des informations telles que le système d’exploitation et sa version, l’adresse IP, le nom DNS et nom de la station de travail. Microsoft ne collecte pas de nom, d’adresse ou d’autres coordonnées.

Pour plus d’informations sur l’utilisation et la collecte de données, voir la [Déclaration de confidentialité des Services en ligne de Microsoft](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment utiliser la fonctionnalité d’analyse des performances, consultez [Afficher les performances de VM Insights](../vm/vminsights-performance.md). Pour afficher les dépendances des applications découvertes, consultez [Afficher la carte VM Insights](../vm/vminsights-maps.md).
