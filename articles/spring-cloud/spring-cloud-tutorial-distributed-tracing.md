---
title: Tutoriel - Utiliser le traçage distribué avec Azure Spring Cloud
description: Ce tutoriel montre comment utiliser le traçage distribué de Spring Cloud via Azure Application Insights
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 9c049ecbea3c630e0f7d08e4a42bd441ba3f5cfa
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708770"
---
# <a name="tutorial-using-distributed-tracing-with-azure-spring-cloud"></a>Didacticiel : Utilisation du suivi distribué avec Azure Spring Cloud

Les outils de suivi distribué de Spring Cloud facilitent le débogage et la supervision de problèmes complexes. Azure Spring Cloud intègre [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) au service [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) d’Azure pour fournir de puissantes fonctionnalités de suivi distribué à partir du portail Azure.

Dans cet article, vous allez apprendre à :

> [!div class="checklist"]
> * Activer le suivi distribué dans le portail Azure
> * Ajouter Spring Cloud Sleuth à votre application
> * Afficher des cartes de dépendances pour vos applications de microservices
> * Lancer des recherches dans des données de suivi avec des filtres différents

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel :

* Un service Azure Spring Cloud déjà provisionné et en cours d’exécution.  Suivez ce [guide de démarrage rapide](spring-cloud-quickstart-launch-app-cli.md) pour provisionner et lancer un service Azure Spring Cloud.
    
## <a name="add-dependencies"></a>Ajout de dépendances

Ajoutez la ligne suivante au fichier application.properties pour autoriser l’expéditeur zipkin à envoyer des données au web :

```xml
spring.zipkin.sender.type = web
```

Vous pouvez ignorer l’étape suivante si vous avez suivi notre [guide de préparation d’une application Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md). Sinon, accédez à votre environnement de développement local et modifiez votre fichier `pom.xml` en y ajoutant la dépendance Spring Cloud Sleuth :

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

* Générez et déployez à nouveau votre service Azure Spring Cloud pour refléter ces changements. 

## <a name="modify-the-sample-rate"></a>Modifier le taux d’échantillonnage
Vous pouvez changer la fréquence de collecte de vos données de télémétrie en modifiant le taux d’échantillonnage. Par exemple, si vous souhaitez échantillonner deux fois moins souvent, accédez à votre fichier `application.properties` et changez la ligne suivante :

```xml
spring.sleuth.sampler.probability=0.5
```

Si vous avez déjà généré et déployé une application, vous pouvez modifier le taux d’échantillonnage en ajoutant la ligne ci-dessus comme variable d’environnement dans Azure CLI ou le portail. 

## <a name="enable-application-insights"></a>Activer Application Insights

1. Accédez à la page du service Azure Spring Cloud dans le portail Azure.
1. Dans la section Supervision, sélectionnez **Suivi distribué**.
1. Sélectionnez **Modifier le paramètre** pour modifier ou ajouter un nouveau paramètre.
1. Créez une requête Application Insight ou sélectionnez une requête existante.
1. Choisissez la catégorie de journalisation à superviser, puis spécifiez la durée de conservation (en jours).
1. Sélectionnez **Appliquer** pour appliquer le nouveau suivi.

## <a name="view-application-map"></a>Voir la cartographie d’application

Revenez à la page de suivi distribué et sélectionnez **Afficher la cartographie d’application**. Passez en revue la représentation visuelle de votre application et les paramètres de supervision. Pour savoir comment utiliser la cartographie d’application, lisez [cet article](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="search"></a>Recherche

Utilisez la fonction de recherche pour interroger d’autres éléments de télémétrie spécifiques. Dans la page **Suivi distribué**, sélectionnez **Rechercher**. Pour plus d’informations sur l’utilisation de la fonctionnalité de recherche, lisez [cet article](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="application-insights-page"></a>Page Application Insights

En plus des fonctionnalités de cartographie d’application et de recherche, Application Insights fournit des fonctions de supervision. Recherchez le nom de votre application dans le portail Azure, puis lancez une page Application Insights pour en savoir plus. Pour plus d’informations sur l’utilisation de ces outils, [lisez la documentation](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).


## <a name="disable-application-insights"></a>Désactiver Application Insights

1. Accédez à la page du service Azure Spring Cloud dans le portail Azure.
1. Dans la section Supervision, cliquez sur **Suivi distribué**.
1. Cliquez sur **Désactiver** pour désactiver Application Insights.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris ce qu’est le suivi distribué dans Azure Spring Cloud et avez vu comment l’activer. Pour savoir comment lier votre application à une base de données Azure CosmosDB, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Découvrir comment lier votre application à une base de données Azure CosmosDB](spring-cloud-tutorial-bind-cosmos.md)
