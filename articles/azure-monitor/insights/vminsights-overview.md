---
title: Qu’est-ce qu’Azure Monitor pour machines virtuelles ?
description: Vue d’ensemble d’Azure Monitor pour machines virtuelles qui supervise l’intégrité et les performances des machines virtuelles Azure, en plus de découvrir et de mapper automatiquement les composants d’application et leurs dépendances.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: f058ce1ae5b7328c6864684994a74f2fd118ca6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85506988"
---
# <a name="what-is-azure-monitor-for-vms"></a>Qu’est-ce qu’Azure Monitor pour machines virtuelles ?

Azure Monitor pour machines virtuelles supervise vos machines virtuelles, groupes de machines virtuelles identiques et machines Azure Arc à grande échelle. Il analyse les performances et l’intégrité des machines virtuelles Windows et Linux, et supervise leurs processus et dépendances vis-à-vis d’autres ressources et des processus externes. Il prend en charge la supervision des performances et des dépendances d’applications des machines virtuelles qui sont hébergées localement ou auprès d’un autre fournisseur de cloud. Les fonctionnalités clés suivantes offrent des insights approfondis :

- **Graphiques prédéfinis de tendances des performances** : affichent les mesures de performances principales du système d’exploitation de la machine virtuelle invitée.

- **Carte des dépendances** : affiche les composants interconnectés avec la machine virtuelle de différents groupes de ressources et abonnements.  

>[!NOTE]
>Nous avons récemment fait l’[annonce des changements](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) que nous apportons à la fonctionnalité Intégrité suite aux commentaires que nous avons reçus de la part de nos clients ayant utilisé la préversion publique. En raison des multiples changements prévus, nous avons choisi de ne plus fournir la fonctionnalité Intégrité aux nouveaux clients. Les clients existants pourront continuer à utiliser la fonctionnalité Intégrité. Pour plus d’informations, consultez [Questions fréquentes (FAQ) sur la disponibilité générale](vminsights-ga-release-faq.md).  

Grâce à l’intégration avec les journaux Azure Monitor et aux puissantes fonctionnalités d’agrégation et de filtrage qu’elle offre, Azure Monitor pour machines virtuelles peut analyser les tendances des données dans le temps. Vous pouvez afficher directement ces données sur une seule machine virtuelle, ou bien utiliser Azure Monitor pour avoir une vue d’ensemble de vos machines virtuelles dans laquelle la vue prend en charge les modes Azure de contexte de la ressource ou de contexte de l’espace de travail. Pour plus d’informations, consultez [Vue d’ensemble des modes d’accès](../platform/design-logs-deployment.md#access-mode).

![Perspective des insights sur les machines virtuelles sur le Portail Azure](media/vminsights-overview/vminsights-azmon-directvm.png)

Azure Monitor pour machines virtuelles peut offrir des performances prévisibles et assurer la disponibilité des applications vitales. Il identifie les goulots d’étranglement de performances et les problèmes réseau et peut aussi vous aider à déterminer si un problème est lié à d’autres dépendances.  

## <a name="data-usage"></a>Utilisation des données

Lorsque Azure Monitor pour machines virtuelles est déployé, les données recueillies par les machines virtuelles sont ingérées et stockées dans Azure Monitor. Les données de performances et de dépendance collectées sont stockées dans un espace de travail Log Analytics. Selon la tarification publiée sur la [page des tarifs d’Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/), Azure Monitor pour machines virtuelles engendre des frais pour :

- les données ingérées et stockées ;
- les règles d’alerte créées ;
- les notifications envoyées. 

La taille du journal, qui varie en fonction de la longueur des chaînes des compteurs de performances, peut augmenter avec le nombre de disques logiques et de cartes réseau alloué à la machine virtuelle. Si vous disposez déjà d’un espace de travail et que vous collectez ces compteurs, ces frais ne seront pas facturés une deuxième fois. Si vous utilisez déjà Service Map, la seule différence notable concerne les données de connexion supplémentaires qui sont envoyées à Azure Monitor.

## <a name="next-steps"></a>Étapes suivantes

Pour comprendre les exigences et les méthodes de monitoring des machines virtuelles, voir [Déployer Azure Monitor pour machines virtuelles](vminsights-enable-overview.md).
