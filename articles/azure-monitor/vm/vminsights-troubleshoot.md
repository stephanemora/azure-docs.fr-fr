---
title: Résolution des problèmes de VM Insights
description: Résoudre les problèmes d’installation de VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: 59b4f38efde2416687702647031d2b37553ff8ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555057"
---
# <a name="troubleshoot-vm-insights"></a>Résolution des problèmes de VM Insights
Cet article fournit des informations sur la résolution des problèmes liés à l’activation ou à l’utilisation de VM Insights.

## <a name="cannot-enable-vm-insights-on-a-machine"></a>Impossible d’activer VM Insights sur une machine
Voici les étapes effectuées lors de l’intégration d’une machine virtuelle Azure depuis le portail Azure :

- Un espace de travail Log Analytics par défaut est créé, si cette option a été sélectionnée.
- L’agent Log Analytics est installé sur les machines virtuelles Azure à l’aide d’une extension de machine virtuelle si l’agent est déjà installé.
- L’agent Dependency est installé sur les machines virtuelles Azure à l’aide d’une extension, au besoin.
  
Pendant le processus d’intégration, chacune de ces étapes est vérifiée et un état de notification s’affiche dans le portail. La configuration de l’espace de travail et l’installation de l’agent prennent généralement de 5 à 10 minutes. L’affichage des données dans le portail prend 5 à 10 minutes de plus.

Si vous recevez un message indiquant que la machine virtuelle doit être intégrée après avoir effectué le processus d’intégration, attendez jusqu’à 30 minutes pour que le processus se termine. Si le problème persiste, consultez les sections suivantes pour connaître les causes possibles.

### <a name="is-the-virtual-machine-running"></a>La machine virtuelle est-elle en cours d’exécution ?
 Si la machine virtuelle a été désactivée pendant un certain temps, si elle est actuellement désactivée ou si elle n’a été activée que tout récemment, vous devrez attendre le temps que les données arrivent avant de pouvoir les afficher.

### <a name="is-the-operating-system-supported"></a>Le système d’exploitation est-il pris en charge ?
Si le système d’exploitation ne se trouve pas dans notre [liste des systèmes d’exploitation pris en charge](vminsights-enable-overview.md#supported-operating-systems), l’installation de l’extension échoue et vous voyez le message selon lequel nous attendons l’arrivée de données.

### <a name="did-the-extension-install-properly"></a>L’extension a-t-elle été installée correctement ?
Si vous voyez toujours un message indiquant que la machine virtuelle doit être intégrée, cela peut signifier qu’une ou les deux extensions n’ont pas pu s’installer correctement. Consultez la page **Extensions** de votre machine virtuelle dans le portail Azure pour vérifier que les extensions suivantes sont répertoriées.

| Système d'exploitation | Agents | 
|:---|:---|
| Windows | MicrosoftMonitoringAgent<br>Microsoft.Azure.Monitoring.DependencyAgent |
| Linux | OMSAgentForLinux<br>DependencyAgentForLinux |

Si vous ne voyez pas les deux extensions pour votre système d’exploitation dans la liste des extensions installées, il est nécessaire de les installer. Si les extensions sont répertoriées mais que leur état n’apparaît pas comme *Approvisionnement réussi*, l’extension doit être supprimée et réinstallée.

### <a name="do-you-have-connectivity-issues"></a>Rencontrez-vous des problèmes de connectivité ?
Pour les ordinateurs Windows, vous pouvez utiliser l’outil  *TestCloudConnectivity* pour identifier le problème de connectivité. Cet outil est installé par défaut avec l’agent dans le dossier *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent*. Exécutez l’outil à partir d’une invite de commandes avec élévation de privilèges. Il retourne des résultats et met en surbrillance l’endroit où le test échoue. 

![Outil TestCloudConnectivity](media/vminsights-troubleshoot/test-cloud-connectivity.png)

### <a name="more-agent-troubleshooting"></a>Résolution des problèmes d’agent, suite

Consultez les articles suivants pour résoudre les problèmes liés à l’agent Log Analytics :

- [Guide pratique pour résoudre les problèmes liés à l’agent Log Analytics pour Windows](../agents/agent-windows-troubleshoot.md)
- [Guide pratique pour résoudre les problèmes liés à l’agent Log Analytics pour Linux](../agents/agent-linux-troubleshoot.md)

## <a name="performance-view-has-no-data"></a>L’affichage des performances n’a pas de données
Si les agents semblent être installés correctement, mais que vous ne voyez pas de données dans l’affichage des performances, consultez les sections suivantes pour connaître les causes possibles.

### <a name="has-your-log-analytics-workspace-reached-its-data-limit"></a>Votre espace de travail Log Analytics a-t-il atteint sa limite de données ?
Vérifiez les [réservations de capacité et la tarification de l’ingestion des données](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>Votre machine virtuelle envoie-t-elle des données de journal et de performances aux journaux Azure Monitor ?

Ouvrez Log Analytics à partir de **Journaux** dans le menu Azure Monitor du Portail Azure. Exécutez la requête suivante sur votre ordinateur :

```kuso
Usage 
| where Computer == "my-computer" 
| summarize sum(Quantity), any(QuantityUnit) by DataType
```

Si vous ne voyez pas de données, il se peut que vous rencontriez des problèmes avec votre agent. Consultez la section ci-dessus pour obtenir des informations sur la résolution des problèmes de l’agent.

## <a name="virtual-machine-doesnt-appear-in-map-view"></a>La machine virtuelle n’apparaît pas dans la vue cartographique

### <a name="is-the-dependency-agent-installed"></a>L’agent Dependency est-il installé ?
 Utilisez les informations dans les sections ci-dessus pour déterminer si l’agent de dépendances est installé et fonctionne correctement.

### <a name="are-you-on-the-log-analytics-free-tier"></a>Êtes-vous sur le niveau gratuit de Log Analytics ?
Le [niveau gratuit de Log Analytics](https://azure.microsoft.com/pricing/details/monitor/) est un plan tarifaire hérité qui autorise jusqu’à cinq machines Service Map uniques. Toutes les machines suivantes n’apparaissent pas dans Service Map, même si les cinq précédentes n’envoient plus de données.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>Votre machine virtuelle envoie-t-elle des données de journal et de performances aux journaux Azure Monitor ?
Utilisez la requête de journal dans la section [La vue de performances n’a pas de données](#performance-view-has-no-data) pour déterminer si les données sont collectées pour la machine virtuelle. Si aucune donnée n’est collectée, utilisez l’outil TestCloudConnectivity décrit ci-dessus pour déterminer si vous avez des problèmes de connectivité.


## <a name="virtual-machine-appears-in-map-view-but-has-missing-data"></a>La machine virtuelle apparaît dans la vue cartographique, mais les données sont manquantes
Si la machine virtuelle se trouve dans la vue cartographique, cela signifie que l’agent de dépendances est installé et en cours d’exécution, mais que le pilote du noyau n’a pas été chargé. Vérifiez le fichier journal aux emplacements suivants :

| Système d'exploitation | Journal | 
|:---|:---|
| Windows | C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log |
| Linux | /var/opt/microsoft/dependency-agent/log/service.log |

Les dernières lignes du fichier doivent indiquer la raison pour laquelle le noyau ne s’est pas chargé. Par exemple, le noyau n’est peut-être pas pris en charge sous Linux si vous l’avez mis à jour.
## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’intégration des agents VM Insights, consultez [Activer la vue d’ensemble de VM Insights](vminsights-enable-overview.md).
