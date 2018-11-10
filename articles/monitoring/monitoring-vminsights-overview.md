---
title: Qu’est-ce qu’Azure Monitor pour machines virtuelles (préversion) ? | Microsoft Docs
description: Azure Monitor pour machines virtuelles est une fonctionnalité d'Azure Monitor qui surveille à la fois l'intégrité et les performances du système d'exploitation de la machine virtuelle Azure. En outre, elle identifie automatiquement les composants des applications et leurs dépendances avec les autres ressources, et mappe leurs communications respectives. Cet article fournit une vue d’ensemble de la fonctionnalité.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: e39954f9155419e0dea019203efa22ba0d654f92
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093681"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Qu’est-ce qu’Azure Monitor pour machines virtuelles (préversion) ?

Azure Monitor pour machines virtuelles surveille vos machines virtuelles Azure en analysant les performances et l’intégrité de vos machines virtuelles Windows et Linux, y compris leurs différents processus et les dépendances interconnectées envers d’autres ressources et processus externes. Cette solution inclut la prise en charge de la supervision des performances et des dépendances des applications pour les machines virtuelles hébergées sur site ou par un autre fournisseur cloud.  Elle comprend trois fonctionnalités clés permettant d’offrir ces insights détaillés :

* Les composants logiques des machines virtuelles Azure exécutant les systèmes d’exploitation Windows et Linux sont mesurés en fonction d’un ensemble de critères d’intégrité préconfigurés qui donnent l’alerte lorsque la condition évaluée est atteinte.  
* Les principales métriques de performance (relatives au processeur, à la mémoire, au disque et à la carte réseau du système d’exploitation de la machine virtuelle invitée) sont collectées et présentées sous forme de graphiques prédéfinis indiquant la tendance des performances.
* Le mappage des dépendances indique les composants découverts qui sont interconnectés avec cette machine virtuelle à partir de plusieurs groupes de ressources et abonnements.  

Ces fonctionnalités sont organisées selon trois perspectives :

* Intégrité
* Performances
* Mappage

>[!NOTE]
>À l’heure actuelle, la fonctionnalité de contrôle de l’intégrité est exclusivement proposée pour les machines virtuelles Azure.
>

L’intégration avec Log Analytics fournit de puissantes fonctionnalités d’agrégation et de filtrage, ainsi que la possibilité d’effectuer des analyses des tendances sur les données au fil du temps. Il est impossible d’effectuer une surveillance complète de vos charges de travail en utilisant uniquement Azure Monitor, Service Map ou Log Analytics.  

Dans le cas d’une machine virtuelle unique, vous pouvez consulter ces données directement à partir de la machine virtuelle. Sion, Azure Monitor fournit une vue agrégée de vos machines virtuelles en fonction de la perspective suivante pour chaque fonctionnalité :

* Intégrité : machines virtuelles associées à un groupe de ressources
* Mappage et performances : machines virtuelles configurées pour rendre compte à un espace de travail Log Analytics spécifique

![Perspective des insights de machine virtuelle à partir du portail](./media/monitoring-vminsights-overview/vminsights-azmon-directvm-01.png)

DevOps peut efficacement prévoir les performances et la disponibilité des applications vitales en identifiant les événements critiques du système d’exploitation, les goulots d’étranglement des performances et les problèmes réseau, et comprendre si un problème est lié aux autres dépendances.  

## <a name="data-usage"></a>Utilisation des données 

Dès que vous adoptez Azure Monitor pour machines virtuelles, les données recueillies par vos machines virtuelles sont ingérées et stockées dans Azure Monitor.  La facturation d’Azure Monitor pour machines virtuelles est effectuée en fonction des données ingérées et conservées, du nombre de séries chronologiques des critères d’intégrité surveillées, des règles d’alerte créées et des notifications envoyées, selon la tarification publiée sur la [page de tarification](https://azure.microsoft.com/pricing/details/monitor/) Azure Monitor.

La taille du journal varie en fonction des longueurs de chaîne des compteurs, et peut augmenter avec le nombre de disques logiques et de cartes réseau.  Si vous disposez déjà d’un espace de travail et que vous collectez ces compteurs, aucun frais en double ne sera appliqué.  Si vous utilisez déjà Service Map, l’unique changement apparaît sur les données de connexion supplémentaires qui sont envoyées à Azure Monitor.

## <a name="next-steps"></a>Étapes suivantes
Pour découvrir les exigences et les méthodes relatives à l’activation de la supervision de vos machines virtuelles, consultez l’article [Intégrer Azure Monitor pour machines virtuelles](monitoring-vminsights-onboard.md).
