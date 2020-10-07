---
title: Introduction à Azure Spring Cloud
description: Découvrez les fonctionnalités et les avantages d’Azure Spring Cloud pour déployer et gérer des applications Spring Java dans Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b03bedd9abbe59ed3bc1b0ec1439aa27af57e6da
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758895"
---
# <a name="what-is-azure-spring-cloud"></a>Qu’est-ce qu’Azure Spring Cloud ?

Azure Spring Cloud permet de déployer facilement des applications de microservices basées sur Spring Boot sur Azure sans aucune modification du code.  Azure Spring Cloud gère l’infrastructure des applications Spring Cloud, ce qui permet aux développeurs de se concentrer sur leur code.  Spring Cloud fournit une gestion du cycle de vie avec une supervision et des diagnostics complets, la gestion des configurations, la découverte de services, l’intégration CI/CD, les déploiements bleus-verts, etc.

Azure Spring Cloud prend en charge les applications Java [Spring Boot](https://spring.io/projects/spring-boot) et ASP.NET Core [Steeltoe](https://steeltoe.io/). La prise en charge de Steeltoe est actuellement en préversion publique. Les offres en préversion publique vous permettent de tester les nouvelles fonctionnalités avant leur publication officielle.  Les fonctionnalités et services en préversion publique ne sont pas destinés à une utilisation en contexte de production.  Pour plus d’informations, consultez la [FAQ](https://azure.microsoft.com/support/faq/) ou faites une [demande de support](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

Composant de l’écosystème Azure, Azure Spring Cloud permet une liaison facile avec d’autres services Azure, notamment le stockage, les bases de données, la supervision, etc.

Cette introduction décrit les fonctionnalités suivantes d’Azure Spring Cloud :

* Serveur de configuration
* Déploiements bleus/verts
* Mise à l’échelle des applications
* Intégration avec Azure DevOps
* Monitoring des applications

## <a name="spring-cloud-config-server"></a>Serveur de configuration Spring Cloud

Le serveur de configuration Azure Spring Cloud fournit une configuration externalisée dans un système distribué avec une prise en charge côté serveur et côté client.  Le serveur de configuration Azure Spring Cloud est un emplacement central pour gérer les propriétés d’une application dans tous les environnements. Pour plus d’informations, consultez [Informations de référence sur le serveur de configuration Spring Cloud](https://spring.io/projects/spring-cloud-config). 

## <a name="bluegreen-deployments"></a>Déploiements bleus/verts

Azure Spring Cloud prend en charge les déploiements bleus/verts pour la publication et la mise à jour du code dans les environnements de production.  Ce modèle de gestion des modifications permet aux développeurs d’implémenter des fonctionnalités et des modifications du code avec la sécurité d’une solution de secours immédiate quand c’est nécessaire.  Les développeurs peuvent se concentrer sur l’écriture du code avec plusieurs environnements de production pour mettre à jour ou annuler des modifications du code sans interrompre l’exécution de l’application.  Pour plus d’informations sur les environnements de préproduction et les déploiements bleus/verts, consultez ce [guide pratique](spring-cloud-howto-staging-environment.md).

## <a name="cicd-pipeline-automation"></a>Automatisation des pipelines CI/CD

Azure Spring Cloud fournit une intégration à Azure DevOps avec Azure CLI.  Avec Azure DevOps, vous pouvez automatiser l’intégration et le déploiement du code sur votre application Spring.  Pour plus d’informations, consultez cet [article](spring-cloud-howto-cicd.md).

## <a name="application-scaling"></a>Mise à l’échelle des applications

Azure Spring Cloud vous permet de mettre à l’échelle facilement les microservices dans votre tableau de bord Azure Spring Cloud.  Le nombre de processeurs virtuels et la quantité de mémoire disponible pour vos microservices peuvent faire l’objet d’un scale-up ou d’un scale-down en fonction de vos besoins.  La mise à l’échelle prend effet en quelques secondes et ne nécessite pas de modification du code ni de redéploiement.  Pour plus d’informations, suivez ce [tutoriel](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Monitoring des applications

Les outils de suivi distribué de Spring Cloud permettent aux développeurs de déboguer et de superviser les interconnexions complexes entre les microservices dans une application.  En intégrant [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) au service [Application Insights](../azure-monitor/insights/insights-overview.md) d’Azure, Azure fournit de puissantes fonctionnalités de suivi distribué à partir du portail Azure.  Pour plus d’informations, suivez ce [tutoriel](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Étapes suivantes

Pour commencer, suivez le [guide de démarrage rapide Spring Cloud](spring-cloud-quickstart.md).

Des exemples sont disponibles sur GitHub. [Exemples Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).
