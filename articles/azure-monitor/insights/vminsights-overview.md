---
title: Qu’est-ce qu’Azure Monitor pour machines virtuelles ?
description: Vue d’ensemble d’Azure Monitor pour machines virtuelles, qui supervise l’intégrité et les performances des machines virtuelles Azure, et découvre et mappe automatiquement les composants d’application et leurs dépendances.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 5c3cb13d0b2da9370f402083d82397679f2c9343
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022494"
---
# <a name="what-is-azure-monitor-for-vms"></a>Qu’est-ce qu’Azure Monitor pour machines virtuelles ?

Azure Monitor pour machines virtuelles supervise les performances et l’intégrité des machines virtuelles et des groupes de machines virtuelles identiques, y compris leurs processus en cours d’exécution et leurs dépendances vis-à-vis d’autres ressources. Il contribue à offrir des performances prévisibles et à assurer la disponibilité des applications vitales en identifiant les goulots d’étranglement des performances et les problèmes réseau. Par ailleurs, il permet de comprendre si un problème est lié à d’autres dépendances.

Azure Monitor pour machines virtuelles prend en charge les systèmes d’exploitation Windows et Linux sur les supports suivants :

- Machines virtuelles Azure
- Groupes de machines virtuelles identiques Azure
- Machines virtuelles hybrides connectées avec Azure Arc
- Machines virtuelles locales
- Machines virtuelles hébergées dans un autre environnement cloud
  



Azure Monitor pour machines virtuelles stocke ses données dans les journaux Azure Monitor, ce qui lui permet d’offrir de puissantes fonctions d’agrégation et de filtrage et d’analyser l’évolution des tendances des données. Vous pouvez afficher directement ces données sur une seule machine virtuelle, ou bien utiliser Azure Monitor pour bénéficier d’une vue agrégée de plusieurs machines virtuelles.

![Perspective des insights sur les machines virtuelles sur le Portail Azure](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Tarifs
Azure Monitor pour machines virtuelles n’engendre aucun coût direct ; en revanche, son activité dans l’espace de travail Log Analytics vous est facturée. Selon la tarification publiée sur la [page des tarifs d’Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/), Azure Monitor pour machines virtuelles engendre des frais pour :

- les données ingérées à partir des agents et stockées dans l’espace de travail ;
- les règles d’alerte basées sur les données des journaux et les données relatives à l’intégrité ;
- les notifications envoyées par les règles d’alerte.

La taille du journal, qui varie en fonction de la longueur des chaînes des compteurs de performances, peut augmenter avec le nombre de disques logiques et de cartes réseau alloué à la machine virtuelle. Si vous utilisez déjà Service Map, la seule différence notable concerne les données de performances supplémentaires qui sont envoyées au type de données `InsightsMetrics` Azure Monitor.


## <a name="configuring-azure-monitor-for-vms"></a>Configuration d’Azure Monitor pour machines virtuelles
Les étapes de configuration d’Azure Monitor pour machines virtuelles sont les suivantes. Pour obtenir des instructions détaillées sur chaque étape, suivez les liens ci-dessous :

- [Création d’un espace de travail Log Analytics](vminsights-configure-workspace.md#create-log-analytics-workspace)
- [Ajout de la solution VMInsights à l’espace de travail](vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Installation des agents sur la machine virtuelle et le groupe de machines virtuelles identiques à superviser](vminsights-enable-overview.md)



## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les exigences et les méthodes permettant la supervision des machines virtuelles, consultez [Déploiement d’Azure Monitor pour machines virtuelles](vminsights-enable-overview.md).

