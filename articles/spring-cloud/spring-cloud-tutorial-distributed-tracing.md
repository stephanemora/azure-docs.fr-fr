---
title: Tutoriel - Utiliser le traçage distribué avec Azure Spring Cloud
description: Ce tutoriel montre comment utiliser le traçage distribué de Spring Cloud via Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 0815aa084462d1b829d64cd7c5d6fa7cebf534fc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78273206"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Utiliser le suivi distribué avec Azure Spring Cloud

Avec les outils de suivi distribué d’Azure Spring Cloud, vous pouvez facilement déboguer et superviser les problèmes complexes. Azure Spring Cloud intègre Azure [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) au service [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) d’Azure. Cette intégration fournit une fonctionnalité puissante de suivi distribué qui est disponible dans le portail Azure.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Activer le suivi distribué dans le portail Azure
> * Ajouter Spring Cloud Sleuth à votre application.
> * Afficher des cartes de dépendances pour vos applications de microservices
> * Lancer des recherches dans des données de suivi avec différents filtres

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous avez besoin d’un service Azure Spring Cloud déjà provisionné et en cours d’exécution. Suivez le [guide de démarrage rapide concernant le déploiement d’une application via Azure CLI](spring-cloud-quickstart-launch-app-cli.md) afin de provisionner et d’exécuter un service Azure Spring Cloud.
    
## <a name="add-dependencies"></a>Ajout de dépendances

1. Ajoutez la ligne suivante au fichier application.properties :

   ```xml
   spring.zipkin.sender.type = web
   ```

   Après cette modification, l’expéditeur Zipkin peut envoyer des données vers le Web.

1. Vous pouvez ignorer l’étape suivante si vous avez suivi notre [guide de préparation d’une application Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md). Sinon, accédez à votre environnement de développement local et modifiez votre fichier pom.xml en y ajoutant la dépendance Spring Cloud Sleuth suivante :

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-sleuth</artifactId>
                <version>${spring-cloud-sleuth.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
    </dependencies>
    ```

1. Générez et déployez à nouveau votre service Azure Spring Cloud pour refléter ces changements.

## <a name="modify-the-sample-rate"></a>Modifier le taux d’échantillonnage

Vous pouvez changer la fréquence de collecte de vos données de télémétrie en modifiant le taux d’échantillonnage. Par exemple, si vous souhaitez échantillonner deux fois moins souvent, ouvrez le fichier application.properties et modifiez la ligne suivante :

```xml
spring.sleuth.sampler.probability=0.5
```

Si vous avez déjà créé et déployé une application, vous pouvez modifier le taux d’échantillonnage. Pour ce faire, ajoutez la ligne précédente en tant que variable d’environnement dans Azure CLI ou dans le portail Azure.

## <a name="enable-application-insights"></a>Activer Application Insights

1. Accédez à la page du service Azure Spring Cloud dans le portail Azure.
1. Dans la section **Supervision**, sélectionnez **Suivi distribué**.
1. Sélectionnez **Modifier le paramètre** pour modifier ou ajouter un nouveau paramètre.
1. Créez une ressource Application Insights ou sélectionnez-en une existante.
1. Choisissez la catégorie de journalisation à superviser, puis spécifiez la durée de conservation (en jours).
1. Sélectionnez **Appliquer** pour appliquer le nouveau suivi.

## <a name="view-the-application-map"></a>Ouvrir la Cartographie d’application

Revenez à la page **Suivi distribué** et sélectionnez **Afficher la cartographie d’application**. Passez en revue la représentation visuelle de votre application et les paramètres de supervision. Pour savoir comment utiliser la cartographie d’application, consultez [Cartographie d’application : trier des applications distribuées](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="use-search"></a>Utiliser la recherche

Utilisez la fonction de recherche pour interroger certaines données de télémétrie. Dans la page **Suivi distribué**, sélectionnez **Rechercher**. Pour plus d’informations sur l’utilisation de la fonction de recherche, consultez [Utilisation de la recherche dans Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="use-application-insights"></a>Utiliser Application Insights

En plus des fonctionnalités de cartographie d’application et de recherche, Application Insights fournit des fonctions de supervision. Recherchez le nom de votre application dans le portail Azure, puis ouvrez une page Application Insights pour accéder aux informations de supervision. Pour plus d’informations sur l’utilisation de ces outils, consultez [Requêtes de journal Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

## <a name="disable-application-insights"></a>Désactiver Application Insights

1. Accédez à la page du service Azure Spring Cloud dans le portail Azure.
1. Dans la section **Supervision**, sélectionnez **Suivi distribué**.
1. Sélectionnez **Désactiver** pour désactiver Application Insights.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris ce qu’est le suivi distribué dans Azure Spring Cloud et vous avez vu comment l’activer. Pour savoir comment lier votre application à une base de données Azure Cosmos DB, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [En savoir plus sur la liaison à une base de données Azure Cosmos DB](spring-cloud-tutorial-bind-cosmos.md)
