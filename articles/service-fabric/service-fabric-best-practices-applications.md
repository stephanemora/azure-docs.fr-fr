---
title: Azure Service Fabric application bonnes pratiques de conception | Microsoft Docs
description: Meilleures pratiques pour développer des applications de Service Fabric.
services: service-fabric
documentationcenter: .net
author: markfussell
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/26/2019
ms.author: msfussell
ms.openlocfilehash: 55f043effc7cdb102acea856e89c58f660d0cde5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237746"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric application bonnes pratiques de conception

Cet article fournit des méthodes recommandées pour la création d’applications et services sur Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Vous familiariser avec Service Fabric
* [Par conséquent, vous souhaitez en savoir plus sur Service Fabric ?](service-fabric-content-roadmap.md)
* En savoir plus sur [scénarios d’application Service Fabric](service-fabric-application-scenarios.md)
* Puis comprendre les choix de modèle de programmation avec [vue d’ensemble du modèle de programmation Service Fabric](service-fabric-choose-framework.md)



## <a name="application-design-guidance"></a>Guide de conception d’application
Vous familiariser avec la [architecture générale](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) d’une application Service Fabric et son [considérations conceptuelles](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Choisissez une passerelle d’API
Utiliser un service de passerelle d’API qui communique avec les services principaux qui peuvent ensuite être montés en charge. Les services de passerelle API courants utilisés sont :

- [Gestion des API Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), c'est-à-dire [intégré avec Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)
- [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) ou [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) à l’aide de la [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) pour lire à partir de partitions du concentrateur d’événements
- [Proxy inverse de Træfik](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/) à l’aide de le [fournisseur Azure Service Fabric](https://docs.traefik.io/configuration/backends/servicefabric/)
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/) Remarque : cela n’est pas directement intégré à Service Fabric et gestion des API Azure est généralement un choix par défaut
- Créer votre propre [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) passerelle d’application web

### <a name="choose-stateless-services"></a>Choisissez les services sans état
Nous vous recommandons de toujours commencer par la création de services sans état à l’aide de [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) stocker l’état dans une base de données Azure, Azure COSMOS DB ou le stockage Azure. État externalisé est l’approche plus familier pour la plupart des développeurs et vous permet également tirer parti des fonctionnalités de requête sur le magasin.  

### <a name="when-to-choose-stateful-services"></a>Quand choisir les services avec état
Prendre en compte des services avec état lorsque vous avez un scénario pour une latence faible et que vous avez besoin de conserver les données proches les ressources de calcul. Voici quelques exemples des appareils IoT double numérique, état du jeu, l’état de session, la mise en cache des données à partir d’une base de données et de longs flux de travail pour le suivi des appels à d’autres services.

Décider de la période de rétention de données :

- Les données mises en cache - vous utilisez la mise en cache lorsque la latence vers des magasins externes est un problème. Utiliser un service avec état, comme vos propres données en cache ou envisagez d’utiliser le [open source SoCreate service-fabric-cache distribué de-](https://github.com/SoCreate/service-fabric-distributed-cache). Dans ce scénario, vous risquez de perdre toutes les données dans le cache, et il n’a pas d’importance.
- Pendant une durée limitée, données - vous devez conserver les données proches de calcul pour une latence pendant une période de temps, mais ces données peuvent se permettre de se perdre dans une *après sinistre* scénario. Par exemple, dans de nombreuses solutions IoT donnée ne doit être proche de calcul, par exemple calculer la température moyenne sur les quelques derniers jours, toutefois, si ces données soient perdues, les données spécifiques pointant enregistrées n’est pas particulièrement important. Également dans ce scénario vous généralement peu sur la sauvegarde de points de données individuels, uniquement des valeurs moyenne calculées qui sont écrites dans le stockage externe régulièrement.  
- Les données à long terme - collections fiables peuvent stocker vos données définitivement. Toutefois, dans ce cas vous devez [préparer pour la récupération d’urgence](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery) notamment [configuration des stratégies de sauvegarde périodiques](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) pour vos clusters. En effet, vous configurez que se passe-t-il si votre cluster est détruit dans un scénario d’urgence, où vous devez créer un nouveau cluster et comment déployer des instances d’application et de récupérer à partir de la dernière sauvegarde.

Réduire les coûts et améliorer la disponibilité :
- Vous pouvez réduire les coûts grâce à des services avec état dans la mesure où vous n’entraînent pas de coûts d’accès et les transactions de données à partir du magasin distant et il est inutile d’utiliser un autre service comme Redis Azure.
- À l’aide des services avec état principalement pour le stockage et pas pour le calcul est coûteux et non recommandée. Prendre en compte des services avec état en tant que calcul avec un stockage local peu coûteux.
- En supprimant les dépendances sur d’autres services, vous pouvez améliorer la disponibilité de votre service. Ayant l’état géré avec haute disponibilité dans le cluster isole vous autres périodes d’indisponibilité du service ou les problèmes de latence.

## <a name="how-to-properly-work-with-reliable-services"></a>Comment fonctionne correctement avec Reliable Services
Reliable Services permettent de créer facilement des services avec et sans état. Lire le [présentation de Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)
- Respectent toujours la [jeton d’annulation](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) dans le `RunAsync()` méthode pour les services avec et sans état et la `ChangeRole()` méthode des services avec état. Sans cela, Service Fabric ne sait pas si votre service peut être fermé. Par exemple, le ne respecte ne pas le jeton d’annulation peut entraîner des temps de mise à niveau des applications beaucoup plus de temps.
-   Open et close [écouteurs de Communication](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) en temps voulu et respectent les jetons d’annulation.
-   Ne mélangez jamais synchroniser le code avec le code asynchrone. Par exemple, n’utilisez pas `.GetAwaiter().GetResult()` vos appels asynchrones ; il doit être asynchrone *jusque* via la pile des appels.

## <a name="how-to-properly-work-with-reliable-actors"></a>Comment fonctionne correctement avec les acteurs fiables
Reliable Actors vous permet de créer facilement des acteurs avec état et virtuels. Lire le [présentation des acteurs fiables](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)

- Envisagez d’utiliser la messagerie entre vos acteurs pour la mise à l’échelle de l’application pub/sub. Par exemple, le [open source SoCreate pub/sub](https://service-fabric-pub-sub.socreate.it/) ou [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Rendre l’état d’acteur en tant que [précis que possible](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Gérer le [cycle de vie de l’acteur](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Supprimer des acteurs si vous ne prévoyez pas de les réutiliser jamais. Cela est particulièrement vrai lorsque vous utilisez le [VolatileState fournisseur](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication) comme ensemble de l’état est stocké en mémoire.
- En raison de leur [tour basé concurrentiel](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency) acteurs sont mieux utilisés comme des objets indépendants. Ne créer des graphiques multi acteur, synchrone d’appels de méthode (chacun d’eux probablement devient un appel réseau distinct) ou demandes d’acteur circulaire ; Ces seront considérablement affecter les performances et mise à l’échelle.
- Ne mélangez pas code de synchronisation avec le code asynchrone ; Il doit être asynchrones complètement, afin d’éviter les problèmes de performances.
- N’effectuez pas les appels longues dans actors, il bloque les autres appels vers le même acteur, en raison de l’accès concurrentiel en alternance.
- Lors de la communication avec d’autres services à l’aide de [communication à distance Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) et que vous créez un `ServiceProxyFactory`, puis créer la fabrique à le [service d’acteur](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) niveau et *pas*au niveau de l’acteur.


## <a name="application-diagnostics"></a>Diagnostic d'application
- Vérifiez minutieusement ajout [journalisation de l’application](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) dans les appels de service. Il est utile dans les diagnostics des scénarios où les services appellent mutuellement. Par exemple, lorsque vous A -> B -> C -> D l’appel peut échouer à n’importe où ; Si ne comporte pas suffisamment de journalisation, il est difficile à diagnostiquer. Si les services sont connectent trop en raison de l’appel de volumes, au moins être vraiment à consigner les erreurs et avertissements.

## <a name="iot-and-messaging-applications"></a>IoT et les applications de messagerie
Lors de messages à partir de la lecture [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) ou [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) utiliser [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) qui s’intègre à Service Fabric Reliable Services pour maintenir le état de lecture à partir du Hub d’événements de partitions et exécute un push de nouveaux messages à vos services via la `IEventProcessor::ProcessEventsAsync()` (méthode).


## <a name="design-guidance-on-azure"></a>Guide de conception sur Azure
* Visitez le [centre des architectures Azure](https://docs.microsoft.com/azure/architecture/microservices/) pour obtenir des conseils de conception sur [création de microservices sur Azure](https://docs.microsoft.com/azure/architecture/microservices/)

* Visitez [prise en main Azure pour les jeux](https://docs.microsoft.com/gaming/azure/) pour obtenir des conseils de conception sur [à l’aide de Service Fabric en services de gaming](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
