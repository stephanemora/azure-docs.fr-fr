---
title: Vue d’ensemble d’Azure Service Fabric Mesh | Microsoft Docs
description: En savoir plus sur Azure Service Fabric Mesh. Avec Service Fabric Mesh, vous pouvez déployer et mettre à l’échelle votre application sans vous soucier des besoins d’infrastructure de votre application.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 10/1/2018
ms.topic: overview
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: f753d7273c685a7ec3e4e0877e171df04e69e5b4
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978313"
---
# <a name="what-is-service-fabric-mesh"></a>Qu’est-ce que Service Fabric Mesh ?

Cette vidéo fournit une vue d’ensemble rapide de Service Fabric Mesh.
> [!VIDEO https://www.youtube.com/embed/7qWeVGzAid0]

Azure Service Fabric mesh est un service entièrement géré qui permet aux développeurs de déployer des applications de microservices sans gestion de machines virtuelles, de stockage ou de mise en réseau. Les applications hébergées sur Service Fabric Mesh sont exécutées et mises à l’échelle sans que vous ayez à vous soucier de l’infrastructure sous-jacente.  Service Fabric Mesh se compose de clusters constitués de milliers de machines.  Toutes les opérations de cluster sont cachées au développeur. Chargez simplement votre code et spécifiez les ressources dont vous avez besoin, les exigences de disponibilité et les limites des ressources.  Service Fabric Mesh alloue automatiquement l’infrastructure et gère les défaillances d’infrastructure. Vous êtes ainsi assuré que vos applications sont hautement disponibles. Il vous suffit de veiller à l’intégrité et à la réactivité de votre application, mais pas à l’infrastructure.  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Cet article contient une vue d’ensemble des principaux avantages de Service Fabric Mesh.

## <a name="great-developer-experience"></a>Expérience de développement améliorée

Service Fabric Mesh prend en charge tout langage ou infrastructure de programmation pouvant être exécuté dans un conteneur. La prise en charge des outils de Visual Studio 2017 et Visual Studio Code assure une puissante expérience de modification et de débogage pour les applications .NET et .NET Core. 

Avec Service Fabric Mesh, vous pouvez :

- Effectuez une migration « lift-and-shift » des applications existantes dans des conteneurs pour moderniser et exécuter vos applications actuelles à grande échelle.
- Générer et déployer de nouvelles applications de microservices à grande échelle dans Azure.  Intégrez-les à d’autres services Azure ou applications existantes qui s’exécutent dans des conteneurs. Chaque microservice fait partie d’une application isolée du réseau sécurisée, avec des stratégies de gouvernance de ressources définies pour les cœurs de processeurs, la mémoire, l’espace disque, etc.
- Intégrer et étendre des applications existantes sans leur apporter de modifications. Utilisez votre propre réseau virtuel pour connecter une application existante à la nouvelle application.  
- Moderniser vos applications Services Cloud existantes en procédant à une migration vers Service Fabric Mesh.  

## <a name="simple-operational-lifecycle"></a>Cycle de vie opérationnel simple

Gérez facilement des applications en cours d’exécution, notamment leurs mises à niveau et contrôles de version, leur supervision ainsi que leur débogage dans des environnements de production. Ces applications peuvent contenir un seul microservice ou plusieurs microservices isolés dans leur propre réseau. Les applications sont exécutées efficacement avec des délais rapides de déploiement, de positionnement et de basculement.

Avec Service Fabric Mesh, vous pouvez :

- Déployer et gérer des applications sans devoir explicitement approvisionner et gérer l’infrastructure.  Service Fabric Mesh approvisionne, met à niveau, corrige et gère l’infrastructure sous-jacente à votre place.
- Configurer l’intégration continue en utilisant les outils intégrés pour empaqueter et déployer facilement des applications.
- Tirer parti de toutes les fonctionnalités des ressources Azure Resource Manager (par exemple, piste d’audit et [contrôle d’accès en fonction du rôle](/azure/role-based-access-control/overview)), car toutes les ressources (telles que les applications, les services, les secrets, etc.) que vous déployez vers le service Service Fabric Mesh dans Azure sont des ressources Azure Resource Manager.
- Déployer et gérer des ressources à l’aide du [portail Azure](https://portal.azure.com), des modèles Resource Manager ou des bibliothèques d’Azure CLI/PowerShell.
- Configurer la supervision opérationnelle et la génération d’alertes à l’aide [d’Application Insights](/azure/application-insights/) (ou de l’outil de votre choix) pour capturer des traces opérationnelles et de diagnostic à partir de la plateforme.
- Accéder aux informations de diagnostic des applications émises à partir du modèle d’application à l’aide [d’Application Insights](/azure/application-insights/) ou de l’outil de votre choix.

## <a name="mission-critical-platform-capabilities"></a>Fonctionnalités stratégiques de la plateforme

Service Fabric Mesh crée une collection de clusters qui s’étend sur les [Zones de disponibilité Azure](/azure/availability-zones/az-overview) et/ou les limites régionales géopolitiques. Les applications sont décrites avec un ensemble d’intentions comme les besoins de mise à l’échelle, la configuration matérielle requise, les spécifications de durabilité et les stratégies de sécurité.  Lorsque l’application est déployée, Service Fabric Mesh recherche l’emplacement optimal pour l’exécuter.

Avec Service Fabric Mesh, vous pouvez :

- Tirer parti des caractéristiques suivantes : haute disponibilité, diminution/augmentation du nombre d’instances, détectabilité, orchestration, routage des messages, messagerie fiable, mises à niveau sans temps d’arrêt, gestion de la sécurité/des secrets, récupération d’urgence, gestion d’état, gestion de la configuration, et transactions distribuées.
- Choisir entre plusieurs modèles d’application lors de la création d’applications.
- Utiliser les fonctionnalités de la plateforme exposées via des points de terminaison REST en consommant des liaisons propres au langage générées à l’aide de Swagger.
- Déployer des applications entre les [Zones de disponibilité](/azure/availability-zones/az-overview) et plusieurs régions pour la fiabilité géographique.
- Utiliser toutes les fonctionnalités de sécurité et de conformité fournies par Azure.

## <a name="next-steps"></a>Étapes suivantes

Le déploiement d’un exemple de projet avec Visual Studio ne prend que quelques étapes. Pour plus d’informations, consultez [Create an ASP.NET Core website](service-fabric-mesh-quickstart-dotnet-core.md) (Créer un site web ASP.NET Core). 

Lisez les réponses aux [questions fréquentes](service-fabric-mesh-faq.md).


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
