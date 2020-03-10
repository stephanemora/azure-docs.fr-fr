---
title: Introduction à Azure Spring Cloud
description: Découvrez les fonctionnalités et les avantages d’Azure Spring Cloud pour déployer et gérer des applications Spring Java dans Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.openlocfilehash: 4426044b3608be0ded378f4f56cbec6bc1948d75
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273256"
---
# <a name="what-is-azure-spring-cloud"></a>Qu’est-ce qu’Azure Spring Cloud ?

Azure Spring Cloud permet de déployer facilement des applications de microservices basées sur Spring Boot sur Azure sans aucune modification du code.  Azure Spring Cloud gère le cycle de vie des applications Spring Cloud, ce qui permet aux développeurs de se concentrer sur leur code.  Spring Cloud fournit une gestion du cycle de vie avec une supervision et des diagnostics complets, la gestion des configurations, la découverte de services, l’intégration CI/CD, les déploiements bleus-verts, etc.

Composant de l’écosystème Azure, Azure Spring Cloud permet une liaison facile avec d’autres services Azure, notamment le stockage, les bases de données, la supervision, etc.

Azure Spring Cloud est actuellement disponible en préversion publique. Les offres en préversion publique permettent aux clients de tester les nouvelles fonctionnalités avant leur publication officielle.  Les fonctionnalités et services en préversion publique ne sont pas destinés à une utilisation en contexte de production.  Pour en savoir plus sur le support offert avec les préversions, consultez notre [FAQ](https://azure.microsoft.com/support/faq/) ou soumettez une [demande de support](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

Pour commencer, suivez le guide de démarrage rapide de Spring Cloud en utilisant [Azure CLI](spring-cloud-quickstart-launch-app-cli.md), le [portail Azure](spring-cloud-quickstart-launch-app-portal.md) ou [Maven](spring-cloud-quickstart-launch-app-maven.md).

D’autres exemples sont disponibles sur GitHub : [Exemples Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

## <a name="application-configuration"></a>Configuration de l’application

### <a name="spring-cloud-config-server"></a>Serveur de configuration Spring Cloud

Le serveur de configuration Azure Spring Cloud fournit une configuration externalisée dans un système distribué avec une prise en charge côté serveur et côté client.  Le serveur de configuration fournit un emplacement central pour gérer les propriétés d’une application dans tous les environnements.  Pour plus d’informations, consultez les [informations de référence sur le serveur de configuration Spring Cloud](https://spring.io/projects/spring-cloud-config.md) et effectuez le tutoriel.

### <a name="enable-bluegreen-deployments"></a>Activer les déploiements bleus/verts

Azure Spring Cloud prend en charge les déploiements bleus/verts pour la publication et la mise à jour du code dans les environnements de production.  Tirer parti de ce modèle de gestion des modifications permet aux développeurs d’implémenter des fonctionnalités et des modifications du code avec la sécurité d’une solution de secours immédiate quand c’est nécessaire.  Azure permet aux développeurs de se concentrer sur l’écriture du code en gérant plusieurs environnements de production, et en facilitant la mise à jour ou l’annulation des modifications du code sans interrompre l’exécution de l’application.  Pour plus d’informations sur les environnements de préproduction et les déploiements bleus/verts, consultez ce [guide pratique](spring-cloud-howto-staging-environment.md).

### <a name="automate-cicd-pipelines"></a>Automatiser les pipelines CI/CD

Azure Spring Cloud fournit une intégration à Azure DevOps avec Azure CLI.  Avec Azure DevOps, vous pouvez automatiser l’intégration et le déploiement du code sur votre application Spring.  Pour plus d’informations, consultez cet [article](spring-cloud-howto-cicd.md).

### <a name="scale-your-application"></a>Mettre à l’échelle votre application

Azure Spring Cloud vous permet de mettre à l’échelle facilement les microservices dans votre tableau de bord Azure Spring Cloud.  Le nombre de processeurs virtuels et la quantité de mémoire disponible pour vos microservices peuvent faire l’objet d’un scale-up ou d’un scale-down en fonction de vos besoins.  La mise à l’échelle prend effet en quelques secondes et ne nécessite pas de modification du code ni de redéploiement.  Pour plus d’informations, suivez ce [tutoriel](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Supervision des applications

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Superviser votre application avec un suivi distribué et Azure Application Insights

Les outils de suivi distribué de Spring Cloud permettent aux développeurs de déboguer et de superviser les interconnexions complexes entre les microservices dans une application.  En intégrant [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) au service [Application Insights](../azure-monitor/insights/insights-overview.md) d’Azure, Azure fournit de puissantes fonctionnalités de suivi distribué à partir du portail Azure.  Pour plus d’informations, suivez ce [tutoriel](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Étapes suivantes

- [Lancer votre application Spring Cloud depuis l’interface CLI](spring-cloud-quickstart-launch-app-cli.md)
