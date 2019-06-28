---
title: Meilleures pratiques de conception d’application Azure Service Fabric | Microsoft Docs
description: Meilleures pratiques pour le développement d’applications Service Fabric.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65237746"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Meilleures pratiques de conception d’application Azure Service Fabric

Cet article fournit des conseils sur les meilleures pratiques de création d’applications et de services sur Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Se familiariser avec Service Fabric
* [Vous voulez en savoir plus sur Service Fabric ?](service-fabric-content-roadmap.md)
* Lire [Scénarios d’applications Service Fabric](service-fabric-application-scenarios.md)
* Comprendre ensuite les choix de modèle de programmation avec [Vue d’ensemble des modèles de programmation Service Fabric](service-fabric-choose-framework.md)



## <a name="application-design-guidance"></a>Guide de conception d’application
Familiarisez-vous avec l’[architecture générale](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) d’une application Service Fabric et son [considérations relatives à la conception](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Choisir une passerelle d’API
Utilisez un service de passerelle d’API qui communique avec les services principaux qui peuvent ensuite être montés en charge. Les services de passerelle d’API les plus couramment utilisés sont :

- [Gestion des API Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), qui est [intégrée à Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)
- [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) ou [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) à l’aide de [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) pour lire à partir de partitions Event Hub
- [Proxy inverse Træfik](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/) à l’aide du [fournisseur Azure Service Fabric](https://docs.traefik.io/configuration/backends/servicefabric/)
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/). Remarque : il n’est pas directement intégré à Service Fabric et Gestion des API Azure est généralement le choix privilégié
- Créer votre propre passerelle d’application web [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore)

### <a name="choose-stateless-services"></a>Choisir des services sans état
Nous vous recommandons de toujours commencer par créer des services sans état à l’aide de [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) qui stocke l’état dans une base de données Azure, Azure CosmosDB ou le Stockage Azure. L’externalisation de l’état est l’approche la plus connue de la plupart des développeurs et elle vous permet également de tirer parti des fonctionnalités de requête sur le magasin.  

### <a name="when-to-choose-stateful-services"></a>Quand choisir des services avec état
Envisagez des services avec état lorsque votre scénario implique une faible latence et que vous devez conserver les données à proximité des ressources de calcul. Des exemples incluent des appareils jumeaux numériques IoT, l’état du jeu, l’état de la session, la mise en cache de données à partir d’une base de données et les flux de travail durables pour suivre les appels d’autres services.

Choisissez la période de conservation des données :

- Données mises en cache : vous utilisez la mise en cache lorsque la latence vers des magasins externes est un problème. Utilisez un service avec état comme cache de données ou envisagez d’utiliser le [cache distribué SoCreate Service Fabric open source](https://github.com/SoCreate/service-fabric-distributed-cache). Dans ce scénario, vous risquez de perdre toutes les données dans le cache, mais cela n’a pas d’importance.
- Données à durée limitée : vous devez conserver les données à proximité des ressources de calcul pour une latence sur une période de temps, mais ces données peuvent être perdues dans un scénario de *sinistre*. Par exemple, dans de nombreuses solutions IoT, les données ne doivent pas être proches des ressources de calcul (pour calculer la température moyenne sur les derniers jours par exemple). Toutefois, si ces données sont perdues, les points de données spécifiques enregistrés n’est pas si importants. Dans ce scénario également, vous vous souciez généralement peu de la sauvegarde des points de données individuels, mais seulement des valeurs moyennes calculées qui sont écrites régulièrement dans le stockage externe.  
- Données à long terme : des collections fiables peuvent stocker définitivement vos données. Dans ce cas, vous devez toutefois vous [préparer à la récupération d’urgence](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery), notamment à la [configuration de stratégies de sauvegarde périodiques](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) pour vos clusters. Dans les faits, vous configurez ce qu’il se passe si votre cluster est détruit dans un scénario de sinistre, auquel cas vous devez créer un nouveau cluster, et comment déployer de nouvelles instances d’application et récupérer à partir de la dernière sauvegarde.

Réduire les coûts et améliorer la disponibilité :
- Vous pouvez réduire les coûts grâce à des services avec état dans la mesure où vous n’engagez pas de coûts d’accès aux données et de transactions à partir du magasin distant. Vous ne devez donc pas nécessairement utiliser un autre service tel que Redis Azure.
- L’utilisation de services avec état principalement pour le stockage et non pour le calcul est coûteuse et non recommandée. Envisagez des services avec état pour le calcul avec un stockage local économique.
- En supprimant les dépendances à d’autres services, vous pouvez améliorer la disponibilité de votre service. Le recours à un état managé avec haute disponibilité dans le cluster permet de vous isoler d’autres temps d’arrêt du service ou problèmes de latence.

## <a name="how-to-properly-work-with-reliable-services"></a>Comment utiliser correctement Reliable Services
Reliable Services vous permet de créer facilement des services sans et avec état. Lire la [Présentation de Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)
- Utilisez toujours le [jeton d’annulation](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) de la méthode `RunAsync()` pour les services sans et avec état et de la méthode `ChangeRole()` pour les services avec état. Sans cela, Service Fabric ne sait pas si votre service peut être fermé. Le fait de ne pas utiliser le jeton d’annulation peut, par exemple, entraîner des temps de mise à niveau d’applications beaucoup plus longs.
-   Ouvrez et fermez les [écouteurs de communications](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) aux moments opportuns et utilisez les jetons d’annulation.
-   Ne combinez jamais du code synchrone et du code asynchrone. Par exemple, n’utilisez pas `.GetAwaiter().GetResult()` dans vos appels asynchrones. Il doit être *entièrement* asynchrone sur l’ensemble de pile des appels.

## <a name="how-to-properly-work-with-reliable-actors"></a>Comment utiliser correctement Reliable Actors
Reliable Actors vous permet de créer facilement des acteurs virtuels avec état. Lire la [Présentation de Reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)

- Envisagez d’utiliser la messagerie de publication/d’abonnement entre vos acteurs pour la mise à l’échelle de l’application. Par exemple, la [messagerie de publication/d’abonnement SoCreate open source](https://service-fabric-pub-sub.socreate.it/) ou [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Rendez l’état d’acteur aussi [précis que possible](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Gérez le [cycle de vie de l’acteur](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Supprimez des acteurs si vous ne prévoyez pas de les réutiliser. Cela est particulièrement vrai lorsque vous utilisez le [fournisseur VolatileState](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication) car tout l’état est stocké en mémoire.
- En raison de leur [concurrence alternée](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency), il est préférable d’utiliser les acteurs comme des objets indépendants. Ne créez pas de graphiques d’appels de méthode synchrones à plusieurs acteurs (chacun d’entre eux constituera probablement un appel réseau distinct) ni de requêtes d’acteur circulaires, celles-ci affectent considérablement les performances et la mise à l’échelle.
- Ne combinez pas du code synchrone et du code asynchrone ; il doit être entièrement asynchrone pour éviter les problèmes de performances.
- N’effectuez pas d’appels longs dans les acteurs, ceux-ci bloquent les autres appels vers le même acteur en raison d’une concurrence alternée.
- En cas de communication avec d’autres services à l’aide d’un [communication à distance Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) et si vous créez un `ServiceProxyFactory`, puis créez la fabrique au niveau de [service d’acteur](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) et *non* au niveau de l’acteur.


## <a name="application-diagnostics"></a>Diagnostic d'application
- Veillez à ajouter la [journalisation des applications](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) dans des appels de service. Elle permet de diagnostiquer les scénarios dans lesquels des services s’appellent mutuellement. Par exemple, dans A -> B -> C -> D, l’appel peut échouer n’importe où. Si la journalisation n’est pas suffisante, difficile de la diagnostiquer. Si la journalisation des services est trop importante en raison des volumes d’appel, veillez à consigner au minimum les erreurs et avertissements.

## <a name="iot-and-messaging-applications"></a>Applications IoT et de messagerie
Lors de la lecture de messages d’[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) ou d’[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/), utilisez [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) qui s’intègre à Service Fabric Reliable Services pour maintenir l’état de lecture des partitions Event Hub et d’envois (push) de nouveaux messages à vos services via la méthode `IEventProcessor::ProcessEventsAsync()`.


## <a name="design-guidance-on-azure"></a>Guide de conception sur Azure
* Rendez-vous sur le [centre des architectures Azure](https://docs.microsoft.com/azure/architecture/microservices/) pour obtenir un guide sur la [création de microservices sur Azure](https://docs.microsoft.com/azure/architecture/microservices/)

* Consultez la section de [prise en main Azure pour les jeux](https://docs.microsoft.com/gaming/azure/) pour obtenir un guide sur l’[utilisation de Service Fabric dans des services de gaming](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
