---
title: Qu’est-ce qu’Azure Monitor pour machines virtuelles (préversion) ? | Microsoft Docs
description: Vue d’ensemble d’Azure Monitor pour machines virtuelles qui supervise l’intégrité et les performances des machines virtuelles Azure, en plus de découvrir et de mapper automatiquement les composants d’application et leurs dépendances.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 17aa81c626c1bfa8a8a344552b7a0fe61dd85a7e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365782"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Qu’est-ce qu’Azure Monitor pour machines virtuelles (préversion) ?

Azure Monitor pour machines virtuelles surveille les machines virtuelles et groupes de machines virtuelles identiques à grande échelle. Il analyse les performances et l’intégrité des machines virtuelles Windows et Linux, et supervise leurs processus et dépendances vis-à-vis d’autres ressources et des processus externes. 

Il prend en charge la supervision des performances et des dépendances d’applications des machines virtuelles qui sont hébergées localement ou auprès d’un autre fournisseur de cloud. Les fonctionnalités clés suivantes offrent des insights approfondis :

- **Graphiques prédéfinis de tendances des performances** : affichent les mesures de performances principales du système d’exploitation de la machine virtuelle invitée.

- **Carte des dépendances** : affiche les composants interconnectés avec la machine virtuelle de différents groupes de ressources et abonnements.  

>[!NOTE]
>Nous avons récemment fait l’[annonce des changements](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) que nous apportons à la fonctionnalité Intégrité suite aux commentaires que nous avons reçus de la part de nos clients ayant utilisé la préversion publique. En raison des multiples changements prévus, nous avons choisi de ne plus fournir la fonctionnalité Intégrité aux nouveaux clients. Les clients existants pourront continuer à utiliser la fonctionnalité Intégrité. Pour plus d’informations, consultez [Questions fréquentes (FAQ) sur la disponibilité générale](vminsights-ga-release-faq.md).  

L’intégration avec les journaux d’activité Azure Monitor offre de puissantes fonctionnalités d’agrégation et de filtrage, ainsi que la possibilité d’analyser les tendances des données au fil du temps. Pris individuellement, Azure Monitor ou Service Map ne sont pas suffisants pour effectuer un monitoring aussi complet des charges de travail.  

Vous pouvez afficher directement ces données sur une seule machine virtuelle, ou bien utiliser Azure Monitor pour avoir une vue d’ensemble de vos machines virtuelles dans laquelle la vue prend en charge les modes Azure de contexte de la ressource ou de contexte de l’espace de travail. Pour plus d’informations, consultez [Vue d’ensemble des modes d’accès](../platform/design-logs-deployment.md#access-mode).

![Perspective des insights sur les machines virtuelles sur le Portail Azure](./media/vminsights-overview/vminsights-azmon-directvm.png)

Azure Monitor pour machines virtuelles peut offrir des performances prévisibles et assurer la disponibilité des applications vitales. Il identifie les goulets d'étranglement de performances et les problèmes réseau. Azure Monitor pour machines virtuelles peut également aider à comprendre si le problème est lié à d’autres dépendances.  

## <a name="data-usage"></a>Utilisation des données

Lorsque Azure Monitor pour machines virtuelles est déployé, les données recueillies par les machines virtuelles sont ingérées et stockées dans Azure Monitor. Les données de performances et de dépendance collectées sont stockées dans un espace de travail Log Analytics. Selon la tarification publiée sur la [page des tarifs d’Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/), Azure Monitor pour machines virtuelles engendre des frais pour :

- les données ingérées et stockées ;
- les règles d’alerte créées ;
- les notifications envoyées. 

La taille du journal, qui varie en fonction de la longueur des chaînes des compteurs de performances, peut augmenter avec le nombre de disques logiques et de cartes réseau alloué à la machine virtuelle. Si vous disposez déjà d’un espace de travail et que vous collectez ces compteurs, ces frais ne seront pas facturés une deuxième fois. Si vous utilisez déjà Service Map, la seule différence concerne les données de connexion supplémentaires envoyées à Azure Monitor.

## <a name="next-steps"></a>Étapes suivantes

Pour comprendre les exigences et les méthodes de monitoring des machines virtuelles, voir [Déployer Azure Monitor pour machines virtuelles](vminsights-enable-overview.md).
