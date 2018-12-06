---
title: Service EventStore d’Azure Service Fabric | Microsoft Docs
description: En savoir plus sur le service EventStore d’Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: cd58e24a51b153d6bf217f7d32a82e882183ed73
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290669"
---
# <a name="eventstore-service-overview"></a>Vue d’ensemble du service EventStore

## <a name="overview"></a>Vue d’ensemble

Introduit dans la version 6.2, le service EventStore est une option d’analyse de Service Fabric, qui permet de comprendre l’état de votre cluster ou des charges de travail à un moment donné. Le service EventStore expose les événements Service Fabric via un ensemble d’API (accessibles via les points de terminaison REST ou la bibliothèque cliente). Ces API EventStore vous permettent d’interroger le cluster directement pour obtenir des données de diagnostic sur une entité du cluster. Elles doivent être utilisées pour aider à :

* Diagnostiquer les problèmes de développement ou de test, ou lorsque vous utilisez peut-être un pipeline de surveillance.
* Vérifier que les actions de gestion que vous entreprenez sur votre cluster sont traitées correctement par le cluster.

Pour obtenir la liste complète des événements disponibles dans le service EventStore, consultez l’article relatif aux [événements Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>Depuis Service Fabric version 6.2, les API EventStore sont en préversion pour les clusters Windows s’exécutant sur Azure uniquement. Nous travaillons au portage de cette fonctionnalité vers Linux et vers nos clusters autonomes.

Vous pouvez interroger le service EventStore à propos des événements qui sont disponibles pour chaque entité et type d’entité du cluster. Cela signifie que vous pouvez rechercher des événements aux niveaux suivants :
* Cluster : événements à tous les niveaux du cluster
* Nœuds : événements à tous les niveaux de nœuds
* Nœud : événements propres à un nœud, en fonction de `nodeName`
* Applications : événements à tous les niveaux des applications
* Application : événements propres à une application
* Services : événements de tous les services des clusters
* Service : événements d’un service spécifique
* Partitions : événements de toutes les partitions
* Partition : événements d’une partition spécifique
* Réplicas : événements de l’ensemble des réplicas/instances
* Réplica : événements d’un réplica/d’une instance spécifique


Le service EventStore a également la possibilité de mettre en corrélation les événements du cluster. En examinant les événements écrits en même temps à partir de différentes entités dont les conséquences peuvent être mutuelles, le service EventStore est en mesure de lier ces événements pour aider à identifier les causes des activités du cluster. Par exemple, si l’une de vos applications se retrouve à l’état défectueux sans aucune modification forcée, le service EventStore examine également d’autres événements exposés par la plateforme et peut les mettre en corrélation avec un événement `NodeDown`. Cela contribue à accélérer la détection des défaillances et l’analyse des causes racines.

Nous vous recommandons d’utiliser EventStore pour effectuer une analyse rapide, obtenir un instantané du fonctionnement du cluster et vérifier si tout se passe comme prévu.

Pour commencer à utiliser le service EventStore, consultez [Query EventStore APIs for cluster events](service-fabric-diagnostics-eventstore-query.md) (Interroger les API EventStore pour rechercher les événements de cluster).

## <a name="next-steps"></a>Étapes suivantes
* Vue d’ensemble de la surveillance et des diagnostics de Service Fabric : [Monitoring et diagnostics pour Azure Service Fabric](service-fabric-diagnostics-overview.md)
* Pour plus d’informations sur la surveillance du cluster, consultez [Monitoring du cluster et de la plateforme](service-fabric-diagnostics-event-generation-infra.md).