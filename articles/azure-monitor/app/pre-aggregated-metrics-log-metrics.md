---
title: Métriques basées sur le journal et pré-agrégées dans Azure Application Insights | Microsoft Docs
description: Pourquoi utiliser des métriques basées sur le journal et pré-agrégées dans Azure Application Insights
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 65abc9c7153aaf2973d5927400e27467066098f9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669758"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Métriques basées sur le journal et pré-agrégées dans Application Insights

Cet article explique la différence entre les métriques Application Insights « traditionnelles » basées sur les journaux d’activité et les métriques pré-agrégées actuellement en préversion publique. Les deux types de métriques sont à la disposition des utilisateurs d’Application Insights et chacun présente des atouts uniques pour la surveillance de l’intégrité des applications, des diagnostics et des analyses. Les développeurs qui appliquent l’instrumentation aux applications décident quel type de métrique convient le mieux à un scénario donné, en fonction de la taille de l’application, du volume attendu de télémétrie et des besoins de l’entreprise en matière de précision des métriques et de génération d’alertes.

## <a name="log-based-metrics"></a>Métriques basées sur le journal

Jusqu’à récemment, le modèle de données de télémétrie de surveillance des applications dans Application Insights était uniquement basé sur un petit nombre de types prédéfinis d’événements, comme les requêtes, les exceptions, les appels de dépendance, les vues de page, etc. Les développeurs peuvent utiliser le Kit de développement logiciel (SDK) pour émettre ces événements manuellement (en écrivant du code qui appelle explicitement le SDK), ou bien ils peuvent se reposer sur la collecte automatique des événements assurée par l’instrumentation automatique. Dans les deux cas, le serveur principal d’Application Insights stocke tous les événements collectés sous forme de journaux d’activité. Dans le portail Azure, les panneaux Application Insights servent d’outil d’analyse et de diagnostic et permettent de visualiser les données basées sur les événements à partir des journaux d’activité.

L’utilisation de journaux d’activité pour conserver un ensemble complet d’événements présente un grand avantage en termes d’analyse et de diagnostic. Vous pouvez par exemple obtenir le nombre exact de requêtes envoyées vers une URL donnée avec le nombre d’utilisateurs distincts ayant effectué ces appels. Ou bien vous pouvez obtenir des rapports d’appels de procédures détaillés pour le diagnostic, notamment les exceptions et les appels de dépendance de n’importe quelle session utilisateur. Ce type d’information améliore considérablement la visibilité sur l’intégrité et l’utilisation des applications, diminuant le temps nécessaire pour diagnostiquer les problèmes avec une application. 

Dans le même temps, collecter un ensemble complet d’événements peut s’avérer difficile (voire impossible) pour les applications qui génèrent un volume important de données de télémétrie. Dans les situations où le volume d’événements est trop élevé, Application Insights implémente plusieurs techniques de réduction du volume de données de télémétrie, comme [l’échantillonnage](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) et le [filtrage](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) qui diminuent le nombre d’événements collectés et stockés. Malheureusement, la diminution du nombre d’événements stockés fait également baisser la précision des métriques qui, en arrière-plan, doivent effectuer des agrégations de requêtes des événements stockés dans les journaux d’activité.

> [!NOTE]
> Dans Application Insights, les métriques basées sur l’agrégation des requêtes d’événements et les mesures stockées dans les journaux d’activité sont appelées des métriques basées sur le journal. Ces métriques comportent généralement de nombreuses dimensions de propriétés d’événements, ce qui les rend particulièrement intéressantes pour l’analyse. Toutefois, leur précision pâtit de l’échantillonnage et du filtrage.

## <a name="pre-aggregated-metrics"></a>Métriques pré-agrégées

Outre les métriques basées sur le journal, à l’automne 2018, l’équipe Application Insights a livré une préversion publique des métriques stockées dans un référentiel spécialisé optimisé pour la série chronologique. Les nouvelles métriques ne sont plus conservées sous la forme d’événements individuels avec un grand nombre de propriétés. À la place, elles sont stockées sous la forme de séries chronologiques pré-agrégées et seulement avec les principales dimensions. Les métriques sont ainsi de meilleure qualité au moment de la requête : l’extraction des données s’effectue beaucoup plus rapidement et nécessite moins de puissance de calcul. Cela rend possible de nouveaux scénarios comme [la génération d’alertes quasiment en temps réel sur les dimensions des métriques](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts), des [tableaux de bord](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard) plus réactifs et bien plus encore.

> [!IMPORTANT]
> Les métriques basées sur le journal et pré-agrégées coexistent dans Application Insights. Pour différencier les deux, dans l’expérience utilisateur Application Insights, les métriques pré-agrégées sont maintenant appelées « Métriques standard (préversion) », tandis que les métriques traditionnelles des événements ont été renommées « métriques basées sur le journal ».

Les kits de développement logiciel les plus récents (SDK [Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) ou version ultérieure pour .NET) pré-agrègent les métriques durant la collecte, avant que les techniques de réduction du volume de données de télémétrie entrent en action. Ainsi, la précision des nouvelles métriques n’est pas affectée par l’échantillonnage et le filtrage lors de l’utilisation des SDK Application Insights les plus récents.

Lorsque les SDK n’implémentent pas la pré-agrégation (ce qui est le cas avec les anciennes versions des SDK Application Insights ou pour l’instrumentation du navigateur), le serveur principal d’Application Insights continue à renseigner les nouvelles métriques en agrégeant les événements reçus par le point de terminaison de collecte des événements d’Application Insights. Ainsi, même si vous ne bénéficiez pas de la réduction du volume de données transmises sur le réseau, vous pouvez utiliser les métriques pré-agrégées et profiter de performances et d’une prise en charge améliorées de la génération d’alertes dimensionnelles quasiment en temps réel avec des kits de développement logiciel qui ne pré-agrègent pas les métriques pendant la collecte.

Notez par ailleurs que le point de terminaison de collecte pré-agrège les événements avant l’échantillonnage d’ingestion, ce qui signifie que [l’échantillonnage d’ingestion](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) n’a pas d’impact sur la précision des métriques pré-agrégées, quelle que soit la version du SDK que vous utilisez pour votre application.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Utilisation de la pré-agrégation avec des métriques personnalisées Application Insights

Vous pouvez utiliser la pré-agrégation avec des métriques personnalisées. Les deux principaux avantages sont : la possibilité de configurer et d’alerter sur la dimension d’une métrique personnalisée et la réduction du volume de données envoyées à partir du SDK vers le point de terminaison de collecte d’Application Insights.

Il existe plusieurs [méthodes pour envoyer des métriques personnalisées à partir du SDK d’Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Si votre version du SDK propose les méthodes [GetMetric et TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric), ces méthodes sont à privilégier pour l’envoi de mesures personnalisées, car dans ce cas la pré-agrégation est effectuée au sein du SDK, réduisant non seulement le volume de données stockées dans Azure, mais également le volume de données transmises à partir du SDK vers Application Insights. Sinon, utilisez la méthode [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric), qui pré-agrège les événements liés aux métriques pendant l’ingestion de données.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Dimensions des métriques personnalisées et pré-agrégation

Toutes les métriques que vous envoyez en utilisant des appels d’API [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) ou [GetMetric et TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) sont automatiquement stockées dans des journaux d’activité et des magasins de métriques. Toutefois, bien que la version basée sur le journal de votre métrique personnalisée conserve toujours toutes les dimensions, la version pré-agrégée de la métrique est stockée par défaut sans aucune dimension. Vous pouvez activer la collecte des dimensions des métriques personnalisées sur l’onglet [d’utilisation et d’estimation des coûts](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) en cochant « Enable alerting on custom metric dimensions » (Activer la génération d’alertes sur les dimensions des métriques personnalisées) : 

![Utilisation et estimation des coûts](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Pourquoi la collecte des dimensions des métriques personnalisées est-elle désactivée par défaut ?

La collecte des dimensions des métriques personnalisées est désactivée par défaut, car à l’avenir, le stockage des métriques personnalisées avec des dimensions sera facturé indépendamment d’Application Insights, tandis que le stockage des métriques personnalisées non dimensionnelles restera gratuit (jusqu'à un quota donné). Pour en savoir plus sur les modifications à venir de notre modèle de tarification, consultez notre [page officielle de tarification](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Création de graphiques et exploration des métriques pré-agrégées standard et basées sur le journal

Utilisez [Azure Monitor Metrics Explorer](../platform/metrics-getting-started.md) pour tracer des graphiques à partir des métriques pré-agrégées et basées sur le journal et créez des tableaux de bord avec des graphiques. Après avoir sélectionné la ressource Application Insights souhaitée, utilisez le sélecteur d’espace de noms pour basculer entre les métriques standard (préversion) et basées sur le journal, ou sélectionnez un espace de noms de métrique personnalisée :

![Espace de noms de la métrique](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="next-steps"></a>Étapes suivantes

* [Génération d’alertes quasiment en temps réel](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric et TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)
