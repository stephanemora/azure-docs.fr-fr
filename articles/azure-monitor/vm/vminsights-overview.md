---
title: Qu’est-ce que VM Insights ?
description: Vue d’ensemble de VM Insights qui surveille l’intégrité et les performances des machines virtuelles Azure, et découvre et mappe automatiquement les composants d’application et leurs dépendances.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 967ff9672f55af3e9c5d2e7c3c3dbc7d2ff6cb3c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707368"
---
# <a name="overview-of-vm-insights"></a>Vue d’ensemble de VM Insights

VM Insights supervise les performances et l’intégrité de vos machines virtuelles et groupes de machines virtuelles identiques, y compris leurs processus en cours d’exécution et leurs dépendances vis-à-vis d’autres ressources. Il contribue à offrir des performances prévisibles et à assurer la disponibilité des applications vitales en identifiant les goulots d’étranglement des performances et les problèmes réseau. Par ailleurs, il permet de comprendre si un problème est lié à d’autres dépendances.

VM Insights prend en charge les systèmes d’exploitation Windows et Linux sur les supports suivants :

- Machines virtuelles Azure
- Groupes de machines virtuelles identiques Azure
- Machines virtuelles hybrides connectées avec Azure Arc
- Machines virtuelles locales
- Machines virtuelles hébergées dans un autre environnement cloud
  

VM Insights stocke ses données dans des journaux d’Azure Monitor, ce qui lui permet d’offrir de puissantes fonctions d’agrégation et de filtrage, et d’analyser l’évolution des tendances des données. Vous pouvez afficher directement ces données sur une seule machine virtuelle, ou bien utiliser Azure Monitor pour bénéficier d’une vue agrégée de plusieurs machines virtuelles.

![Perspective des insights sur les machines virtuelles sur le Portail Azure](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Tarifs
VM Insights n’occasionne aucun coût direct mais son activité dans l’espace de travail Log Analytics vous est facturée. Selon la tarification publiée sur la [page des tarifs Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/), VM Insights engendre des frais pour postes suivants :

- les données ingérées à partir des agents et stockées dans l’espace de travail ;
- Données d’état d’intégrité collectées à partir de l’intégrité des invités (préversion)
- les règles d’alerte basées sur les données des journaux et les données relatives à l’intégrité ;
- les notifications envoyées par les règles d’alerte.

La taille du journal, qui varie en fonction de la longueur des chaînes des compteurs de performances, peut augmenter avec le nombre de disques logiques et de cartes réseau alloué à la machine virtuelle. Si vous utilisez déjà Service Map, la seule différence notable concerne les données de performances supplémentaires qui sont envoyées au type de données `InsightsMetrics` Azure Monitor.


## <a name="configuring-vm-insights"></a>Configuration de VM Insights
Les étapes à suivre pour configurer VM Insights sont les suivantes. Pour obtenir des instructions détaillées sur chaque étape, suivez les liens ci-dessous :

- [Création d’un espace de travail Log Analytics](./vminsights-configure-workspace.md#create-log-analytics-workspace)
- [Ajout de la solution VMInsights à l’espace de travail](./vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Installation des agents sur la machine virtuelle et le groupe de machines virtuelles identiques à superviser](./vminsights-enable-overview.md)



## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les exigences et les méthodes pour activer la surveillance de vos machines virtuelles, consultez [Déployer VM Insights](./vminsights-enable-overview.md).
