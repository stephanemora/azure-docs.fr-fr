---
title: Bonnes pratiques de supervision pour Azure Service Fabric | Microsoft Docs
description: Bonnes pratiques relatives à la supervision des clusters et des applications Service Fabric
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: d90daaf18e5161053e00671b7667d05ec8e5db76
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242919"
---
# <a name="monitoring-and-diagnostics"></a>Surveillance et diagnostics

[La supervision et les diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) sont essentiels au développement, au test et au déploiement des charges de travail dans tout environnement cloud. Par exemple, vous pouvez suivre la façon dont vos applications sont utilisées, les actions effectuées par la plateforme Service Fabric, votre utilisation des ressources avec des compteurs de performances et l’intégrité globale de votre cluster. Vous pouvez utiliser ces informations pour diagnostiquer et corriger les problèmes, et éviter qu’ils ne se reproduisent.

## <a name="application-monitoring"></a>Monitoring des applications

Le monitoring des applications permet de suivre l’utilisation des fonctionnalités et des composants de votre application. Supervisez vos applications pour intercepter les problèmes qui impactent les utilisateurs. La responsabilité de la supervision des applications revient aux utilisateurs qui développent l’application et ses services, car elle s’applique uniquement à la logique métier de l’application. Il est recommandé de configurer la supervision des applications avec [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet), qui est l’outil de supervision des applications d’Azure.

## <a name="cluster-monitoring"></a>Monitoring du cluster

L’un des objectifs de Service Fabric est d’assurer le bon fonctionnement des applications, même en cas de défaillances matérielles. Cet objectif repose sur la capacité des services système de la plateforme à détecter les problèmes d’infrastructure et à basculer rapidement les charges de travail sur d’autres nœuds du cluster. Mais que se passe-t-il si les services système subissent eux aussi des problèmes ? Que se passe-t-il si, durant une tentative de déploiement ou de déplacement d’une charge de travail, les règles de placement des services sont enfreintes ? Dans ce cas, Service Fabric fournit des diagnostics pour vous informer de la façon dont la plateforme Service Fabric interagit avec vos applications, services, conteneurs et nœuds.

Pour les clusters Windows, il est recommandé de définir la surveillance de cluster avec [Agent de Diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) et [Azure Monitor enregistre](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup).

Pour les clusters Linux, les journaux d’Azure Monitor est également l’outil recommandé pour la plateforme Azure et la surveillance de l’infrastructure. Les diagnostics de plateforme Linux nécessitent une configuration différente, comme indiqué dans [Événements de cluster Linux Service Fabric dans Syslog](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Supervision des infrastructures

[Journaux d’analyse Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) est recommandé pour la surveillance des événements de niveau de cluster. Une fois que vous aurez configuré l’agent Log Analytics avec votre espace de travail comme décrit dans le lien précédent, vous pourrez collecter des métriques de performances telles que l’utilisation du processeur, des compteurs de performances .NET tels que l’utilisation du processeur au niveau du processus, des compteurs performances Service Fabric tels que le nombre d’exceptions d’un service fiable, et des métriques de conteneur, telles que l’utilisation du processeur.  Vous devez écrire des journaux de conteneurs dans stdout ou stderr afin qu’elles seront disponibles dans les journaux Azure Monitor.

## <a name="watchdogs"></a>Agents de surveillance

En général, un agent de surveillance est un service distinct capable de surveiller l’intégrité et la charge des services, d’effectuer des tests ping sur les points de terminaison et de créer des rapports à partir des événements non sains du cluster. Cela vous permet de détecter plus facilement les erreurs que vous n’auriez pas pu détecter en vous basant uniquement sur les performances d’un seul service. Les agents de surveillance constituent également un bon emplacement pour héberger du code qui exécute des actions correctives sans intervention de l’utilisateur (par exemple, le nettoyage de fichiers journaux dans le stockage à intervalles réguliers). Pour voir un exemple d’implémentation de l’agent de surveillance, consultez [Événements de cluster Linux Service Fabric dans Syslog](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="next-steps"></a>Étapes suivantes

* Pour instrumenter vos applications : [Événements au niveau de l’application et génération de journaux](service-fabric-diagnostics-event-generation-app.md).
* Suivez les étapes de configuration d’Application Insights pour votre application dans [Surveiller et diagnostiquer une application ASP.NET Core dans Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Pour plus d’informations sur la supervision de la plateforme et les événements fournis par Service Fabric : [Événements au niveau de la plateforme et génération de journaux](service-fabric-diagnostics-event-generation-infra.md).
* Configurer l’intégration des journaux Azure Monitor avec Service Fabric : [Configurer les journaux Azure Monitor pour un cluster](service-fabric-diagnostics-oms-setup.md)
* Découvrez comment configurer les journaux Azure Monitor pour surveiller les conteneurs : [Supervision et diagnostic des conteneurs Windows dans Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Pour consulter des exemples de problèmes de diagnostic et de solutions Service Fabric : [Diagnostiquer des scénarios courants](service-fabric-diagnostics-common-scenarios.md)
* Découvrez les recommandations générales sur la supervision des ressources Azure : [Bonnes pratiques : Supervision et diagnostics](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).