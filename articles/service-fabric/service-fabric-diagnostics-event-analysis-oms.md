---
title: Analyse d’événements Azure Service Fabric avec Log Analytics | Microsoft Docs
description: Découvrez l’analyse et la visualisation d’événements à l’aide de Log Analytics pour la surveillance et le diagnostic de clusters Azure Service Fabric.
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
ms.date: 05/29/2018
ms.author: srrengar
ms.openlocfilehash: 49d9b5306a0fcf51cc0de036c725fca8345cd0ec
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36302180"
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>Analyse et visualisation d’événements avec Log Analytics
Log Analytics collecte et analyse les données de télémétrie des applications et services hébergés dans le Cloud et fournit des outils d’analyse pour vous aider à maximiser leur disponibilité et performance. Cet article explique comment exécuter des requêtes dans Log Analytics pour obtenir des informations détaillées et résoudre les problèmes qui surviennent dans votre cluster. Les questions courantes suivantes sont traitées :

* Comment résoudre les problèmes d’intégrité ?
* Comment savoir quand un nœud tombe en panne ?
* Comment savoir si les services de mon application ont démarré ou se sont arrêtés ?

## <a name="log-analytics-workspace"></a>Espace de travail Log Analytics

Log Analytics collecte des données à partir de ressources gérées, notamment un agent ou une table de stockage Azure, et les conserve dans un référentiel central. Les données peuvent ensuite être utilisées pour l’analyse, l’alerte et la visualisation, ou une nouvelle exportation. Log Analytics prend en charge les événements, les données de performances ou toute autre donnée personnalisée. Consultez [étapes de configuration de l’extension de diagnostics pour regrouper des événements](service-fabric-diagnostics-event-aggregation-wad.md) et [étapes de création d’un espace de travail Log Analytics pour lire les événements dans le stockage](service-fabric-diagnostics-oms-setup.md) pour vous assurer que les données circulent dans Log Analytics.

Après réception des données par Log Analytics, Azure dispose de plusieurs *solutions de gestion* qui sont des solutions prépackagées permettant de surveiller les données entrantes, personnalisées pour plusieurs scénarios. Ces solutions incluent une solution *Service Fabric Analytics* et une solution *Conteneurs*, qui sont les deux solutions les plus appropriées pour les diagnostics et le suivi lors de l’utilisation des clusters Service Fabric. Cet article décrit comment utiliser la solution Service Fabric Analytics, qui est créée avec l’espace de travail.

## <a name="access-the-service-fabric-analytics-solution"></a>Accéder à la solution Service Fabric Analytics dans le portail

1. Dans le portail Azure, accédez au groupe de ressources dans lequel vous avez créé la solution Service Fabric Analytics.

2. Sélectionnez la ressource **ServiceFabric\<nameOfOMSWorkspace\>**.

2. Dans le résumé, des vignettes représentant un graphique s’affichent pour chacune des solutions activées, y compris pour Service Fabric. Cliquez sur le graphique **Service Fabric** (première image ci-dessous) pour accéder à la solution Service Fabric Analytics (deuxième image ci-dessous).

    ![Solution Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

    ![Solution Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

L’image ci-dessus est la page d’accueil de la solution Service Fabric Analytics. Il s’agit d’une capture instantanée de ce qui se passe dans votre cluster. Si vous avez activé des diagnostics lors de la création du cluster, vous pouvez voir les événements pour le 

* [canal opérationnel](service-fabric-diagnostics-event-generation-operational.md) : opérations de niveau supérieur effectuées par la plateforme Service Fabric (collection de services système).
* [Événements du modèle de programmation Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Événements du modèle de programmation Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Outre le canal opérationnel, des événements système plus détaillés peuvent être collectés en [mettant à jour la configuration de votre extension de diagnostics](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Afficher les événements Service Fabric, y compris les actions sur les nœuds

1. Sur la page Service Fabric Analytics, cliquez sur le graphique de **Service Fabric Events**.

    ![Canal opérationnel de la solution Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

2. Cliquez sur **Liste** pour afficher les événements dans une liste. Vous verrez alors tous les événements système qui ont été collectés. Pour référence, il s’agit de la table WADServiceFabricSystemEventsTable dans le compte de stockage Azure. De même, les services fiables et les événements acteurs que vous voyez ensuite proviennent de ces tables respectives.
    
    ![Canal opérationnel de requêtes](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Vous pouvez également cliquer sur la loupe à gauche et utiliser le langage de requête Kusto pour trouver ce que vous cherchez. Par exemple, pour rechercher toutes les actions effectuées sur des nœuds du cluster, vous pouvez utiliser la requête suivante. Les ID d’événement ci-dessous se trouvent dans la [référence des événements du canal opérationnel](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Vous pouvez effectuer des requêtes sur beaucoup plus de champs, tels que les nœuds spécifiques (ordinateur) et le service système (TaskName).

### <a name="view-service-fabric-reliable-service-and-actor-events"></a>Afficher le service Service Fabric Reliable et des événements d’acteurs

1. Sur la page Service Fabric Analytics, cliquez sur le graphique de **Reliable Services**.

    ![Modèle Reliable Services de la solution Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

2. Cliquez sur **Liste** pour afficher les événements dans une liste. Ici, vous pouvez voir les événements des services fiables. Vous pouvez voir différents événements qui surviennent lorsque le service runasync est démarré et terminé, ce qui est généralement le cas lors de déploiements et de mises à niveau. 

    ![Reliable Services pour les requêtes](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Les événements d’acteurs fiables peuvent être affichés de manière similaire. Pour configurer des événements plus détaillés pour des acteurs fiables, vous devez modifier le `scheduledTransferKeywordFilter` dans le fichier config pour l’extension de diagnostic (voir ci-dessous). Vous trouverez plus d’informations à ce sujet dans la [référence des événements des acteurs fiables](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

Le langage de requête Kusto est puissant. Une autre requête précieuse que vous pouvez exécuter consiste à savoir quels nœuds génèrent la plupart des événements. La requête dans la capture d’écran ci-dessous montre des événements opérationnels Service Fabric agrégés avec le service et le nœud spécifiques.

![Événements de requête par nœud](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour activer la surveillance de l’infrastructure, par exemple, les compteurs de performances, rendez-vous sur [Ajout de l’agent Log Analytics](service-fabric-diagnostics-oms-agent.md). L’agent collecte des compteurs de performances et les ajoute à votre espace de travail existant.
* Pour les clusters locaux, Log Analytics propose une passerelle (proxy de transfert HTTP) qui peut être utilisée pour envoyer des données à Log Analytics. Pour plus d’informations à ce sujet, consultez [Connexion d’ordinateurs à Log Analytics sans accès Internet à l’aide de la passerelle OMS](../log-analytics/log-analytics-oms-gateway.md)
* Configurez [l’alerte automatisée](../log-analytics/log-analytics-alerts.md) afin de faciliter la détection et les diagnostics
* Familiarisez-vous avec les fonctionnalités de [requêtes et recherches dans les journaux](../log-analytics/log-analytics-log-searches.md) offertes dans le cadre de Log Analytics
* Pour obtenir une présentation plus détaillée de Log Analytics et de ce qu’il propose, lisez [Qu’est-ce que Log Analytics ?](../operations-management-suite/operations-management-suite-overview.md)
