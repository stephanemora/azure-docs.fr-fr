---
title: Scénarios et conception d’applications | Microsoft Docs
description: Vue d'ensemble des catégories d'applications cloud dans Service Fabric. Traite de la conception d’applications à l’aide de services avec ou sans état
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/24/2019
ms.author: atsenthi
ms.openlocfilehash: f7ad295eb30d257cd195ae02d5a5b1d85de76bda
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228501"
---
# <a name="service-fabric-application-scenarios"></a>Scénarios d'applications Service Fabric
Azure Service Fabric offre une plateforme d’infrastructure fiable et flexible qui vous permet d’écrire et d’exécuter de nombreux types de services et d’applications d’entreprise. Ces applications et microservices peuvent être sans état ou avec état, et font l’objet d’un équilibrage des ressources entre les machines virtuelles pour optimiser l’efficacité. L'architecture unique de Service Fabric vous permet d'exécuter dans vos applications des traitements d'événement, des transactions parallèles, des calculs en mémoire et des analyses de données pratiquement en temps réel. Vous pouvez facilement augmenter ou réduire l’échelle de vos applications (vraiment dehors ou dedans), en fonction de vos besoins en ressources.

Pour obtenir des conseils de conception sur la création d’applications, consultez [architecture de microservices sur Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) et [meilleures pratiques de conception de l’application à l’aide de Service Fabric](service-fabric-best-practices-applications.md)

La plateforme Service Fabric est idéale pour les types d’applications suivants :

* **Collecte des données, le traitement et IoT**: Dans la mesure où Service Fabric gère à grande échelle et possède une faible latence ses services avec état, il est idéal pour le traitement des données sur des millions d’appareils où les données de l’appareil et le calcul sont COLOCALISÉES.

    Les clients ont établi des services IoT à l’aide de Service Fabric : [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [ Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric), et [Mesh Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Jeux basés sur des sessions et des applications interactives**: Service Fabric est idéal si votre application nécessite des lectures à faible latence et les écritures, comme dans les jeux en ligne ou de la messagerie instantanée. Service Fabric vous permet de créer ces applications interactives avec état sans avoir à créer un magasin distinct ou un cache. Visitez [solutions de gaming Azure](https://azure.microsoft.com/solutions/gaming/) pour obtenir des conseils de conception sur [à l’aide de Service Fabric en services de gaming](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    Les clients ont établi des services de gaming inclure [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) et [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Les clients ont établi des sessions interactives : [Honeywell avec Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)

* **Analytique de données et de traitement de flux de travail**: Applications qui doivent traiter de manière fiable les événements ou des flux de données également tirer avantage des optimisé de lectures et écritures dans Service Fabric. En outre, Service Fabric prend en charge les pipelines de traitement d’application, où les résultats doivent être fiables et transmis à l’étape de traitement sans perte. Ceci s’applique en particulier aux systèmes transactionnels et financiers, qui imposent une garantie de calcul et de cohérence des données.

    Les clients ont établi des services de flux de travail métier : [Zeiss groupe](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) et [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)

* **Calcul sur des données**: Service Fabric vous permet de créer des applications avec état de données calcul intensif. Service Fabric permet la colocalisation du traitement (calcul) et les données dans les applications. Normalement, lorsque votre application requiert un accès aux données, latence réseau associée à un niveau de cache ou le stockage des données externes limite le temps de calcul. Avec les services Service Fabric avec état, cette latence est éliminée, l’activation plus optimisée lit et écrit. Par exemple, considérez une application qui effectue près de sélections de recommandation en temps réel pour les clients, avec une exigence de temps d’aller-retour de moins de 100 millisecondes. Les caractéristiques de latence et les performances des services Service Fabric (où le calcul de sélection de la recommandation est colocalisé avec les données et les règles) offrent une expérience réactive à l’utilisateur par rapport au modèle d’implémentation standard de avoir à extraire les données nécessaires à partir du stockage à distance.

    Les clients ont établi des services de calcul incluent [Solidsoft réponse](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) et [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)

* **Services hautement disponibles** : Service Fabric fournit un basculement rapide en créant plusieurs réplicas de service secondaire. Si un nœud, un processus ou un service individuel tombe en panne en raison d’une défaillance matérielle ou autre, un des réplicas secondaires est promu réplica principal avec une perte de service minimale.

* **Services évolutifs** : les différents services peuvent être partitionnés pour permettre une montée en charge de l'état à l'ensemble du cluster. En outre, les services individuels peuvent être créés et supprimés à la volée. Les services peuvent être rapidement et facilement augmentée à partir de plusieurs instances sur quelques nœuds à des milliers d’instances sur un grand nombre de nœuds et puis mis à l’échelle dans une nouvelle fois, selon vos besoins en ressources. Vous pouvez utiliser Service Fabric pour générer ces services et de gérer leur cycle de vie complet.

## <a name="application-design-case-studies"></a>Études de cas de conception d’applications
Plusieurs études de cas illustrant comment Service Fabric permet de concevoir des applications sont publiées sur [témoignages de clients](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) et [site des solutions microservices](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Conception d’applications composées de microservices avec et sans état
La création d’applications avec des rôles de travail Azure Cloud Service est un exemple de service sans état. À l’opposé, les microservices avec état maintiennent leur état faisant autorité au-delà de la demande et de la réponse correspondante. Cette fonctionnalité fournit une haute disponibilité et cohérence de l’état par le biais des API simples qui offrent des garanties transactionnelles soutenues par la réplication. Les services avec état de Service Fabric démocratisent la haute disponibilité sur tous les types d’applications, sans se limiter aux bases de données et autres banques de données. Il s’agit d’une évolution naturelle. Les applications sont déjà passées d’une simple utilisation de bases de données purement relationnelles pour la haute disponibilité à l’emploi de bases de données NoSQL. Les applications elles-mêmes peuvent maintenant avoir un état « actif » et bénéficier d’une gestion des données en interne pour optimiser leurs performances, sans pour autant sacrifier la fiabilité, la cohérence ou la disponibilité des données.

Quand vous créez des applications comprenant des microservices, vous avez généralement une combinaison d’applications web sans état (ASP.NET, Node.js, etc.) qui appellent des services de couche intermédiaire métier sans et avec état, tous déployés dans le même cluster Service Fabric au moyen des commandes de déploiement Service Fabric. Chacun de ces services est indépendant en ce qui concerne la mise à l’échelle, la fiabilité et l’utilisation des ressources, ce qui améliore considérablement agilité et la flexibilité dans le développement et la gestion du cycle de vie.

Les microservices avec état simplifient les conceptions d’applications, car ils suppriment les caches et files d’attente supplémentaires, jusque-là indispensables pour répondre aux exigences de disponibilité et de latence d’une application purement sans état. Dans la mesure où les services avec état ont naturellement la haute disponibilité et une faible latence, il existe moins d’éléments mobiles à gérer dans votre application dans son ensemble. Les schémas ci-dessous comparent la conception d’une application sans état à la conception d’une application avec état. Grâce aux modèles de programmation [Reliable Services](service-fabric-reliable-services-introduction.md) et [Reliable Actors](service-fabric-reliable-actors-introduction.md), les services avec état réduisent la complexité de l’application, tout en atteignant un débit élevé et une faible latence.

## <a name="an-application-built-using-stateless-services"></a>Application créée à l'aide de services sans état
![Application utilisant un service sans état][Image1]

## <a name="an-application-built-using-stateful-services"></a>Application créée à l'aide de services avec état
![Application utilisant un service sans état][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

* Lire les [études de cas clients](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc)
* En savoir plus sur les [modèles et scénarios](service-fabric-patterns-and-scenarios.md)

* Commencez à créer des services sans et avec état au moyen des modèles de programmation [Reliable Services](service-fabric-reliable-services-quick-start.md) et [Reliable Actors](service-fabric-reliable-actors-get-started.md) de Service Fabric.
* Visitez le centre d’architecture Azure pour obtenir des conseils sur [création de microservices sur Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* Accédez à [application Azure Service Fabric et le cluster des meilleures pratiques](service-fabric-best-practices-overview.md) pour obtenir des conseils de conception application.

* Consultez également les rubriques suivantes :
  * [En savoir plus sur les microservices](service-fabric-overview-microservices.md)
  * [Définition et gestion de l’état du service](service-fabric-concepts-state.md)
  * [Disponibilité des services Service Fabric](service-fabric-availability-services.md)
  * [Mise à l’échelle des applications Service Fabric](service-fabric-concepts-scalability.md)
  * [Partitionnement des services Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
