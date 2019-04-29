---
title: Reconfiguration dans Azure Service Fabric | Microsoft Docs
description: Comprendre la reconfiguration des partitions dans Service Fabric
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: ''
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: a24aa6aa1695a3d1166816b7960bdd7b551e1a37
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60882195"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Reconfiguration dans Azure Service Fabric
Une *configuration* se définit comme étant les réplicas et leurs rôles pour une partition d’un service avec état.

Une *reconfiguration* est le processus de passage d’une configuration à une autre configuration. Cela revient à opérer un changement dans le jeu de réplicas pour une partition d’un service avec état. L’ancienne configuration est appelée la *configuration précédente*, et la nouvelle configuration la *configuration actuelle*. Le protocole de reconfiguration dans Azure Service Fabric préserve la cohérence et la disponibilité quand un changement est apporté au jeu de réplicas.

Failover Manager lance les reconfigurations en réponse à différents événements survenus dans le système. Par exemple, si le réplica principal échoue, une reconfiguration est lancée pour promouvoir un réplica secondaire actif à la place du réplica principal. Ce peut être aussi une réponse à des mises à niveau d’application, quand le déplacement du réplica principal s’avère nécessaire sur un autre nœud en vue de la mise à niveau du nœud.

## <a name="reconfiguration-types"></a>Types de reconfiguration
Les reconfigurations peuvent être classées selon deux types :

- Reconfigurations où le réplica principal est changé :
    - **Basculement** : Les basculements sont des reconfigurations faisant suite à la défaillance d’un principal en cours d’exécution.
    - **Échangeprincipal**: Échanges sont des reconfigurations où Service Fabric doit déplacer un en cours d’exécution principal d’un nœud vers un autre, mais généralement en réponse à l’équilibrage de charge ou une mise à niveau.

- Reconfigurations où le réplica principal ne change pas :

## <a name="reconfiguration-phases"></a>Phases de reconfiguration
Une reconfiguration se décompose en plusieurs phases :

- **Phase0**: Cette phase se produit dans les reconfigurations d’échange de principal où le principal actuel transfère son état au nouveau réplica principal et secondaire active.

- **Phase1**: Cette phase se produit pendant les reconfigurations où le principal est changé. Au cours de cette phase, Service Fabric identifie le principal approprié parmi les réplicas actuels. Cette phase est inutile pendant les reconfigurations d’échange de principal, car le nouveau réplica principal a déjà été choisi. 

- **Phase2**: Pendant cette phase, Service Fabric s’assure que toutes les données sont disponibles dans la majorité des réplicas de la configuration actuelle.

Il existe plusieurs autres phases qui sont à usage interne uniquement.

## <a name="stuck-reconfigurations"></a>Reconfigurations bloquées
Les reconfigurations peuvent se *bloquer* pour diverses raisons. Voici quelques-unes des raisons les plus courantes :

- **Réplicas arrêtés**: Certaines phases de reconfiguration nécessitent que la majorité des réplicas dans la configuration soient opérationnels.
- **Problèmes de réseau ou de communication**: Reconfigurations exigent une connectivité réseau entre les différents nœuds.
- **Échecs d’API**: Le protocole de reconfiguration requiert que les implémentations de service terminent certaines API. Par exemple, le fait de ne pas respecter le jeton d’annulation dans un service fiable entraîne le blocage des reconfigurations ÉchangePrincipal.

Utilisez les rapports d’intégrité à partir des composants système, tels que System.FM, System.RA et System.RAP, pour diagnostiquer où le blocage d’une reconfiguration a eu lieu. La [page sur les rapports d’intégrité du système](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) décrit ces rapports.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les concepts propres à Service Fabric, consultez les articles suivants :

- [Cycle de vie des Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)
- [Rapports d’intégrité du système](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Réplicas et instances](service-fabric-concepts-replica-lifecycle.md)
