---
title: Diagnostiquer des scénarios courants avec Azure Service Fabric | Microsoft Docs
description: Découvrez comment résoudre les problèmes de scénarios courants avec Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/16/2018
ms.author: srrengar
ms.openlocfilehash: bd7a7e0288ced0219a0600034b273d1acba6b09b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659640"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnostiquer des scénarios courants avec Service Fabric

Cet article décrit les scénarios courants que les utilisateurs ont rencontrés dans le domaine du monitoring et des diagnostics avec Service Fabric. Les scénarios présentés couvrent les 3 couches de Service Fabric : Application, Cluster et Infrastructure. Chaque solution utilise Application Insights et Log Analytics, outils de monitoring Azure, pour chaque scénario. Les étapes de chaque solution offrent aux utilisateurs une introduction à l’utilisation d’Application Insights et de Log Analytics dans le contexte de Service Fabric.

## <a name="prerequisites-and-recommendations"></a>Prérequis et recommandations

Les solutions de cet article utilisent les outils suivants. Nous vous recommandons de les installer et de les configurer :

* [Application Insights avec Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Activer Azure Diagnostics sur votre cluster](service-fabric-diagnostics-event-aggregation-wad.md)
* [Configurer un espace de travail OMS Log Analytics](service-fabric-diagnostics-oms-setup.md)
* [Agent OMS pour suivre les compteurs de performances](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Comment voir les exceptions non gérées dans mon application ?

1. Accédez à votre ressource Application Insights avec laquelle est configurée votre application.
2. Cliquez sur *Rechercher* en haut à gauche. Cliquez ensuite sur Filtrer dans le panneau suivant.

    ![Vue d’ensemble d’Application Insights](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Vous voyez un grand nombre de types d’événements (traces, demandes, événements personnalisés). Choisissez « Exception » comme filtre.

    ![Liste de filtres Application Insights](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    En cliquant sur une exception dans la liste, vous pouvez voir plus de détails, notamment le contexte du service si vous utilisez le kit SDK de Service Fabric Application Insights.

    ![Exception Application Insights](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Comment voir les appels HTTP qui ont été utilisés dans mes services ?

1. Dans la même ressource Application Insights, vous pouvez filtrer sur « demandes » au lieu des exceptions et voir ainsi toutes les demandes effectuées.
2. Si vous utilisez le kit SDK Service Fabric Application Insights, vous pouvez voir une représentation visuelle de vos services connectés les uns aux autres et le nombre de demandes ayant réussi et échoué. Sur la gauche, cliquez sur « Cartographie d’application ».

    ![Panneau de cartographie d’application AI](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![Cartographie d’application AI](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Pour plus d’informations sur la cartographie d’application, consultez la [documentation sur la cartographie d’application](../application-insights/app-insights-app-map.md).

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Comment créer une alerte quand un nœud est en baisse ?

1. Les événements de nœud sont suivis par votre cluster Service Fabric. Accédez à la ressource de la solution Service Fabric Analytics nommée **ServiceFabric(NameofResourceGroup)**.
2. Cliquez sur le graphe en bas du panneau intitulé « Résumé ».

    ![Solution OMS](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Ici, vous avez un grand nombre de graphes et de vignettes affichant diverses métriques. Cliquez sur l’un des graphes, ce qui vous dirigera vers la recherche dans les journaux. Ici, vous pouvez créer des requêtes sur des événements de cluster ou des compteurs de performances.
4. Entrez la requête suivante. Ces ID d’événement se trouvent dans la [référence des événements de nœud](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId >= 25623 or EventId <= 25626
    ```

5. Cliquez sur « Nouvelle règle d’alerte » en haut. À partir de maintenant, chaque fois qu’un événement arrive en fonction de cette requête, vous recevez une alerte dans le mode de communication de votre choix.

    ![Nouvelle alerte OMS](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Comment être averti de restaurations de mise à niveau d’application ?

1. Dans la même fenêtre de recherche dans les journaux qu’avant, entrez la requête suivante pour les restaurations de mise à niveau. Ces ID d’événements se trouvent sous la [référence des événements d’application](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId == 29623 or EventId == 29624
    ```

2. Cliquez sur « Nouvelle règle d’alerte » en haut. À partir de maintenant, chaque fois qu’un événement arrive en fonction de cette requête, vous recevez une alerte.

## <a name="how-do-i-see-container-metrics"></a>Comment voir les métriques de conteneur ?

Dans la même vue où se trouvent tous les graphes, vous pouvez voir quelques vignettes pour les performances de vos conteneurs. Vous avez besoin de l’Agent OMS et de la [solution Container Monitoring](service-fabric-diagnostics-oms-containers.md) pour que ces vignettes soient renseignées.

![Métriques des conteneurs OMS](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Pour utiliser les données de télémétrie **à l’intérieur** de votre conteneur, vous devez ajouter le [package nuget Application Insights pour les conteneurs](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Comment suivre les compteurs de performances ?

1. Une fois que vous avez ajouté l’Agent OMS à votre cluster, vous devez ajouter les compteurs de performances spécifiques que vous voulez suivre. Accédez à la page de l’espace de travail OMS dans le portail. Dans la page de la solution, l’onglet de l’espace de travail se trouve dans le menu de gauche.

    ![Onglet de l’espace de travail OMS](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Une fois que vous êtes dans la page de l’espace de travail, cliquez sur « Paramètres avancés » dans le même menu de gauche.

    ![Paramètres avancés OMS](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Cliquez sur Données > Compteurs de performances Windows (Données > Compteurs de performances Linux pour les machines Linux) pour démarrer la collecte des compteurs spécifiques de vos nœuds via l’Agent OMS. Voici des exemples du format des compteurs à ajouter

    * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
    * `Processor(_Total)\\% Processor Time`
    * `Service Fabric Service(*)\\Average milliseconds per request`

    Dans le guide de démarrage rapide, VotingData et VotingWeb sont les noms des processus utilisés ; voici à quoi devrait ressembler le suivi de ces compteurs

    * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
    * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

    ![Compteurs de performances OMS](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Ils vous permettent de voir comment votre infrastructure gère vos charges de travail et de définir des alertes pertinentes en fonction de l’utilisation des ressources. Par exemple, vous souhaiterez probablement définir une alerte si l’utilisation totale du processeur est supérieure à 90 % ou inférieure à 5 %. Le nom de compteur que vous utiliseriez pour cela serait « % temps processeur ». Vous pourriez alors créer une règle d’alerte pour la requête suivante :

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Comment suivre les performances de mes événements Reliable Services et Actors ?

Pour le suivi des performances des événements Reliable Services ou Actors dans vos applications, vous devez aussi ajouter les compteurs Service Fabric Actor, Actor Method, Service et Service Method. Vous pouvez ajouter ces compteurs de la même manière que le scénario ci-dessus. Voici des exemples de compteurs de performances Reliable Service et Actor à ajouter dans OMS.

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Consultez ces liens pour obtenir la liste complète des compteurs de performances sur Reliable [Services](service-fabric-reliable-serviceremoting-diagnostics.md) et [Actors](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Étapes suivantes

* [Configurer des alertes dans AI](../application-insights/app-insights-alerts.md) pour être averti des changements de performances ou d’utilisation
* La [détection intelligente dans Application Insights](../application-insights/app-insights-proactive-diagnostics.md) effectue une analyse proactive de la télémétrie envoyée à AI pour vous avertir d’éventuels problèmes de performances
* Découvrez plus en détail la [création d’alertes](../log-analytics/log-analytics-alerts.md) dans OMS Log Analytics pour faciliter la détection et les diagnostics.
* Pour les clusters locaux, OMS propose une passerelle (proxy de transfert HTTP) qui peut être utilisée pour envoyer des données à OMS. Pour plus d’informations à ce sujet, consultez [Connexion d’ordinateurs à OMS sans accès Internet à l’aide de la passerelle OMS](../log-analytics/log-analytics-oms-gateway.md)
* Familiarisez-vous avec les fonctionnalités de [requêtes et recherches dans les journaux](../log-analytics/log-analytics-log-searches.md) offertes dans le cadre de Log Analytics
* Pour obtenir une présentation plus détaillée de Log Analytics et de ce qu’il propose, lisez [Qu’est-ce que Log Analytics ?](../operations-management-suite/operations-management-suite-overview.md)
