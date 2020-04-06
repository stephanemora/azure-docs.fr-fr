---
title: Traçage distribué dans Azure Application Insights | Microsoft Docs
description: Obtenez des informations sur la prise en charge Microsoft du traçage distribué par le biais de notre projet de partenariat OpenCensus
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: c2f384370c3ceaf24164e4a27adc05b1a1e1ddf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294957"
---
# <a name="what-is-distributed-tracing"></a>Présentation du traçage distribué

L’avènement du cloud moderne et des architectures de [microservices](https://azure.com/microservices) a donné naissance à des services simples qui peuvent être déployés de façon indépendante et qui participent à réduire les coûts, tout en améliorant la disponibilité et le débit. Bien que ces mouvements aient facilité la compréhension des services individuels, ils rendent les systèmes globaux plus difficiles à analyser et à déboguer.

Dans les architectures monolithiques, nous sommes habitués à résoudre les problèmes à l’aide de piles des appels. Les piles des appels sont des outils parfaits pour afficher le flux d’exécution (la méthode A a appelé la méthode B, qui a appelé la méthode C) ainsi que les détails et paramètres de chacun de ces appels. Cette méthodologie est tout à fait adaptée aux monolithes ou aux services qui s’exécutent sur un processus unique. Mais comment déboguer lorsque l’appel se trouve à la limite d’un processus, et qu’il n’est pas simplement une référence sur la pile locale ? 

C’est alors qu’intervient le traçage distribué.  

Le traçage distribué est l’équivalent des piles des appels pour les architectures de microservices et de cloud modernes, avec l’ajout d’un profileur de performances très simple. Dans Azure Monitor, nous proposons deux expériences pour consommer des données de trace distribuée. Tout d’abord, nous proposons la vue des [diagnostics de transaction](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics), qui ressemble à une pile des appels à laquelle est ajoutée une dimension temporelle. La vue des diagnostics de transaction offre une visibilité sur une seule transaction/requête. Elle est utile pour rechercher la cause racine des problèmes de fiabilité et des goulots d’étranglement des performances sur la base de chaque requête.

Azure Monitor offre également une vue de [mise en correspondance d’applications](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) qui regroupe de nombreuses transactions afin d’afficher une vue topologique des interactions entre les systèmes et des taux moyens de performance et d’erreur. 

## <a name="how-to-enable-distributed-tracing"></a>Activer le traçage distribué

L’activation du traçage distribué entre les services d’une application est aussi simple que l’ajout du SDK ou de la bibliothèque adéquats à chaque service, en fonction du langage d’implémentation du service.

## <a name="enabling-via-application-insights-sdks"></a>Activation via les SDK Application Insights

Les SDK Application Insights pour .NET, .NET Core, Java, Node.js et JavaScript prennent tous en charge le traçage distribué en mode natif. Les instructions permettant d’installer et de configurer chaque SDK Application Insights sont disponibles ci-dessous :

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.JS](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)
* [Python](opencensus-python.md)

Lorsque le SDK Application Insights adéquat est installé et configuré, les collecteurs automatiques des dépendances des SDK recueillent les informations de traçage automatiquement pour les infrastructures, les bibliothèques et les technologies populaires. La liste complète des technologies prises en charge est disponible dans [la documentation de la collecte automatique de la dépendance](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies).

 De plus, toute technologie peut être pistée manuellement par un appel [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) sur [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Activation via OpenCensus

Outre les SDK, Application Insights prend également en charge le traçage distribué via [OpenCensus](https://opencensus.io/). OpenCensus est une distribution unique de bibliothèques (Open Source et indépendante des fournisseurs) offrant la collecte de métriques et le traçage distribué pour les services. Cet outil permet également à la communauté Open Source d’activer le traçage distribué avec des technologies populaires comme Redis, Memcached ou MongoDB. [Microsoft collabore sur OpenCensus avec plusieurs partenaires de surveillance et de cloud](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

[Python](opencensus-python.md) 

Le site web OpenCensus fournit de la documentation de référence sur l’API pour [Python](https://opencensus.io/api/python/trace/usage.html) et [Go](https://godoc.org/go.opencensus.io), ainsi que divers guides sur l’utilisation d’OpenCensus. 

## <a name="next-steps"></a>Étapes suivantes

* [Guide d’utilisation Python et OpenCensus](https://opencensus.io/api/python/trace/usage.html)
* [Plan de l’application](./../../azure-monitor/app/app-map.md)
* [Analyse des performances de bout en bout](./../../azure-monitor/learn/tutorial-performance.md)
