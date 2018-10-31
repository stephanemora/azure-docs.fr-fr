---
title: Présentation de la surveillance et des diagnostics Azure Service Fabric | Microsoft Docs
description: Découvrez la surveillance et les diagnostics pour les clusters, applications et services Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/18/2018
ms.author: srrengar
ms.openlocfilehash: 5fc2674a145be99fb8867c5cf1b1f65ba860db80
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457831"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Surveillance et diagnostics pour Azure Service Fabric

Cet article fournit une vue d’ensemble du monitoring et des diagnostics dans Azure Service Fabric. Le monitoring et les diagnostics sont essentiels au développement, au test et au déploiement de charges de travail dans tout environnement cloud. Le monitoring vous permet de faire le suivi de la façon dont vos applications sont utilisées, de votre utilisation des ressources et de l’intégrité globale de votre cluster. Vous pouvez utiliser ces informations pour diagnostiquer et corriger les problèmes, et éviter qu’ils ne se reproduisent. 

## <a name="application-monitoring"></a>Monitoring des applications
Le monitoring des applications permet de suivre l’utilisation des fonctionnalités et des composants de votre application. L’objectif est d’intercepter les problèmes qui impactent les utilisateurs. Le monitoring des applications peut être utile dans les scénarios suivants :
* Déterminer la charge de l’application et le trafic des utilisateurs pour savoir s’il faut mettre à l’échelle les services pour répondre aux demandes des utilisateurs ou traiter un goulot d’étranglement potentiel
* Identifier les problèmes de communication avec le service, à distance ou non, sur l’ensemble de votre cluster
* Comprendre ce que font vos utilisateurs avec vos applications : la collecte des données de télémétrie dans vos applications peut contribuer à orienter le développement de nouvelles fonctionnalités et à améliorer les diagnostics d’erreurs
* Monitoring des conteneurs en cours d’exécution

Service Fabric prend en charge plusieurs options pour instrumenter votre code d’application avec les suivis et données de télémétrie appropriés. Il est recommandé d’utiliser Application Insights (AI). L’intégration d’Application Insights à Service Fabric comprend des outils pour Visual Studio et pour le portail Azure, ainsi que des métriques Service Fabric, ce qui fournit une expérience de journalisation complète et prête à l’emploi. Même si de nombreux journaux sont automatiquement créés et collectés pour vous quand vous utilisez Application Insights, nous vous recommandons d’ajouter une journalisation personnalisée à vos applications pour créer une expérience de diagnostics plus riche. Pour plus d’informations sur l’utilisation d’Application Insights avec Service Fabric, consultez [Analyse d’événements avec Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).

## <a name="platform-cluster-monitoring"></a>Monitoring de la plateforme (cluster)
Le monitoring de votre cluster Service Fabric est essentiel pour garantir que la plateforme et toutes les charges de travail fonctionnent comme prévu. L’un des objectifs de Service Fabric est d’assurer le bon fonctionnement des applications même en cas de défaillances matérielles. Cet objectif repose sur la capacité des services système de la plateforme à détecter les problèmes d’infrastructure et à basculer rapidement les charges de travail sur d’autres nœuds du cluster. Mais dans ce cas précis, que se passe-t-il si les services système subissent eux aussi des problèmes ? Que se passe-t-il si, durant une tentative de déplacement d’une charge de travail, les règles de placement des services sont enfreintes ? Le monitoring du cluster vous permet d’être tenu informé de l’activité dans votre cluster pour diagnostiquer les problèmes et les résoudre efficacement. Voici quelques points clés à évaluer :
* Service Fabric se comporte-t-il de la manière attendue, aussi bien au niveau du placement de vos applications que de l’équilibrage de la charge de travail autour du cluster ? 
* Les actions utilisateur effectuées sur votre cluster sont-elles reconnues et exécutées comme prévu ? Ceci s’applique surtout lors de la mise à l’échelle d’un cluster.
* Service Fabric gère-t-il correctement vos données et vos communications de service à service à l’intérieur du cluster ?

Service Fabric fournit un ensemble complet d’événements prêts à l’emploi. Ces [événements Service Fabric](service-fabric-diagnostics-events.md) sont accessibles via les API EventStore ou le canal opérationnel (canal d’événements exposé par la plateforme). 
* EventStore : EventStore (disponible sur Windows dans les versions 6.2 et ultérieures ; Linux toujours en cours d’avancement à la date de la dernière mise à jour de cet article) expose ces événements via un ensemble d’API (accessible via des points de terminaison REST ou la bibliothèque de client). Pour en savoir plus sur EventStore, consultez [Vue d’ensemble d’EventStore](service-fabric-diagnostics-eventstore.md).
* Canaux d’événements Service Fabric : sur Windows, les événements Service Fabric sont disponibles à partir d’un seul fournisseur ETW avec un ensemble de filtres `logLevelKeywordFilters` pertinents permettant de choisir entre le canal opérationnel et le canal de données et de messagerie. Il s’agit de la méthode à l’aide de laquelle nous séparons des événements Service Fabric sortants à filtrer en fonction de vos besoins. Sur Linux, les événements Service Fabric transitent par LTTng et sont placés dans une table de stockage où ils peuvent être filtrés selon les besoins. Ces canaux contiennent des événements organisés et structurés que vous pouvez utiliser pour mieux comprendre l’état de votre cluster. Les diagnostics sont activés par défaut au moment de la création du cluster. Vous disposez ainsi d’une table Stockage Azure où sont envoyés les événements de ces canaux que vous pourrez interroger ultérieurement. 

Nous vous recommandons d’utiliser EventStore pour effectuer une analyse rapide, obtenir un instantané du fonctionnement du cluster et vérifier si tout se passe comme prévu. Pour collecter les journaux et les événements générés par votre cluster, nous conseillons généralement l’utilisation de [l’extension Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md). Celle-ci s’intègre bien à Service Fabric Analytics, la solution propre à Service Fabric de Log Analytics, qui fournit un tableau de bord personnalisé pour la supervision des clusters Service Fabric et vous permet d’interroger les événements de votre cluster et de configurer des alertes. Pour plus d’informations, consultez l’article [Analyse et visualisation d’événements avec Log Analytics](service-fabric-diagnostics-event-analysis-oms.md). 

 Pour plus d’informations sur le monitoring de votre cluster, consultez [Génération d’événements et de journaux au niveau plateforme](service-fabric-diagnostics-event-generation-infra.md).

## <a name="performance-monitoring"></a>Analyse des performances
Le monitoring de l’infrastructure sous-jacente est essentiel pour comprendre l’état de votre cluster et l’utilisation de vos ressources. La mesure des performances système dépend de plusieurs facteurs, chacun d’eux étant généralement mesuré au moyen de KPI (indicateurs de performance clés). Les KPI pertinents pour Service Fabric peuvent être mappés à des métriques que vous pouvez collecter à partir des nœuds de votre cluster sous la forme de compteurs de performances.
Ces KPI peuvent vous aider à :
* Comprendre l’utilisation et la charge des ressources dans le but de mettre à l’échelle votre cluster ou d’optimiser vos processus de service
* Prédire les problèmes d’infrastructure. De nombreux problèmes sont précédés de baisses soudaines des performances. Vous pouvez donc utiliser des KPI comme E/S réseau et Utilisation de l’UC pour prédire et diagnostiquer les problèmes liés à l’infrastructure.

Vous trouverez la liste des compteurs de performances à collecter au niveau infrastructure dans [Métriques de performances](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric propose un ensemble de compteurs de performances pour les modèles de programmation Reliable Services et Actors. Si vous utilisez l’un de ces modèles, ces compteurs de performances peuvent fournir des KPI vous permettant de vérifier que les procédures de « spin up » et de « spin down » de vos acteurs se déroulent correctement ou que vos demandes de services fiables sont gérées assez rapidement. Pour plus d’informations, consultez [Surveillance pour Reliable Service Remoting](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) et [Surveillance des performances pour Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). Application Insights comprend également un ensemble de métriques de performances qui sont collectées si vous les configurez avec votre application.

Utilisez [l’agent Log Analytics](service-fabric-diagnostics-oms-agent.md) pour collecter les compteurs de performances appropriés et visualiser ces KPI dans Azure Log Analytics.

![Graphique de vue d’ensemble des diagnostics](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="health-monitoring"></a>Surveillance de l’intégrité
La plateforme Service Fabric inclut un modèle d’intégrité qui fournit des rapports d’intégrité extensibles sur l’état des entités dans un cluster. Chaque nœud, application, service, partition, réplica ou instance a un état d’intégrité qui est mis à jour en permanence. L’état d’intégrité peut avoir la valeur « OK », « Avertissement » ou « Erreur ». Il est changé par le biais des rapports d’intégrité qui sont émis pour chaque entité en fonction des problèmes affectant le cluster. Vous pouvez vérifier à tout moment l’état d’intégrité de vos entités dans Service Fabric Explorer (SFX), comme indiqué ci-dessous, ou l’interroger au moyen de l’API Health de la plateforme. Vous pouvez aussi personnaliser les rapports d’intégrité et modifier l’état d’intégrité d’une entité en ajoutant vos propres rapports d’intégrité ou en utilisant l’API Health. Pour plus d’informations sur le modèle d’intégrité, consultez [Présentation du contrôle d’intégrité de Service Fabric](service-fabric-health-introduction.md).

![Tableau de bord d’intégrité SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

Outre l’affichage des derniers rapports d’intégrité dans SFX, chaque rapport est également disponible sous la forme d’un événement. Vous pouvez collecter les événements d’intégrité par le biais du canal opérationnel (voir [Agrégation d’événements avec Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)) et les stocker dans Log Analytics pour générer des alertes et lancer des requêtes par la suite. Ceci facilite la détection des problèmes qui peuvent impacter la disponibilité de votre application. Nous vous recommandons donc de définir des alertes pour les scénarios d’échec correspondants (alertes personnalisées par le biais de Log Analytics).

## <a name="other-logging-solutions"></a>Autres solutions de journalisation

Même si les deux solutions que nous vous recommandons, [Azure Log Analytics](service-fabric-diagnostics-event-analysis-oms.md) et [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md), sont intégrées à Service Fabric, de nombreux événements sont écrits par les fournisseurs ETW et peuvent être étendus avec d’autres solutions de journalisation. Intéressez-vous également à [Elastic Stack](https://www.elastic.co/products) (notamment si vous envisagez d’exécuter un cluster dans un environnement hors connexion), à [Dynatrace](https://www.dynatrace.com/) ou à toute autre plateforme de votre choix. Vous trouverez la liste des partenaires intégrés disponibles [ici](service-fabric-diagnostics-partners.md).

Quelle que soit la plateforme que vous choisissez, ses points clés doivent inclure la convivialité de l’interface utilisateur et des options de requêtes, la possibilité de visualiser les données et de créer des tableaux de bord faciles à lire, ainsi que les outils supplémentaires qu’elle met à votre disposition pour améliorer la surveillance, tels que l’alerte automatisée.

## <a name="next-steps"></a>Étapes suivantes

* Pour commencer à instrumenter vos applications, consultez [Génération d’événements et de journaux au niveau application](service-fabric-diagnostics-event-generation-app.md).
* Pour plus d’informations sur la surveillance de la plateforme et des événements fournis par Service Fabric, consultez [Génération d’événements et de journaux au niveau plateforme](service-fabric-diagnostics-event-generation-infra.md).
* Suivez les étapes de configuration d’Application Insights pour votre application dans la section [Surveiller et diagnostiquer une application ASP.NET Core dans Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Pour découvrir comment configurer OMS Log Analytics pour la surveillance des conteneurs, consultez la page [Surveillance et diagnostics des conteneurs Windows dans Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Consultez des exemples de problèmes de diagnostic et de solutions avec Service Fabric dans l’article [Diagnostiquer des scénarios courants](service-fabric-diagnostics-common-scenarios.md).
* Découvrez d’autres produits de diagnostic qui s’intègrent à Service Fabric dans l’article [Solutions de partenaires pour la surveillance d’Azure Service Fabric](service-fabric-diagnostics-partners.md).
* Découvrez les recommandations générales sur la surveillance des ressources Azure : [Bonnes pratiques : Surveillance et diagnostics](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 
