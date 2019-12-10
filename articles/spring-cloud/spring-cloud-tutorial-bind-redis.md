---
title: Tutoriel - Guide pratique pour lier Azure Cache pour Redis à votre application Azure Spring Cloud
description: Ce tutoriel vous montre comment lier Azure Cache pour Redis à votre application Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 1653db3619fd569238872ca1fcfd6d0c439e84c9
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708782"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Didacticiel : Lier des services Azure à votre application Azure Spring Cloud : Cache Azure pour Redis

Azure Spring Cloud vous permet de lier automatiquement certains services Azure à vos applications, au lieu de configurer manuellement votre application Spring Boot. Cet article explique comment lier votre application à Cache Azure pour Redis.

## <a name="prerequisites"></a>Prérequis

* Une instance Azure Spring Cloud déployée
* Une instance du service Cache Azure pour Redis
* L’extension Azure Spring Cloud pour Azure CLI

Si vous n’avez pas encore déployé d’instance Azure Spring Cloud, suivez les étapes décrites dans ce [guide de démarrage rapide](spring-cloud-quickstart-launch-app-portal.md) pour déployer votre première application Spring Cloud.

## <a name="bind-azure-cache-for-redis"></a>Lier Cache Azure pour Redis

1. Ajoutez la dépendance suivante dans le fichier `pom.xml` de votre projet.

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Supprimez les éventuelles propriétés `spring.redis.*` du fichier `application.properties`.

1. Mettez à jour le déploiement actuel avec `az spring-cloud app update` ou créez un déploiement avec `az spring-cloud app deployment create`.

1. Accédez à la page du service Azure Spring Cloud dans le portail Azure. Recherchez le **Tableau de bord de l’application** et sélectionnez l’application à lier à Cache Azure pour Redis.  Il s’agit de la même application que celle que vous avez mise à jour ou déployée à l’étape précédente. Ensuite, choisissez `Service binding`, puis sélectionnez le bouton `Create service binding`. Remplissez le formulaire, en veillant à sélectionner **Type de liaison** `Azure Cache for Redis`, votre serveur Redis et l’option Clé principale. 

1. Redémarrez l’application : cette liaison doit maintenant fonctionner.

1. Pour vérifier que la liaison de service est correcte, sélectionnez le nom de la liaison et examinez ses détails. Le champ `property` doit se présenter comme ceci :
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment lier votre application Azure Spring Cloud à un cache Redis Azure.  Pour découvrir plus d’informations sur la liaison de services à votre application, continuez avec le tutoriel sur la liaison d’une application à une base de données MySQL.

> [!div class="nextstepaction"]
> [Découvrez comment lier un service Azure MySQL à votre service Azure Spring Cloud](spring-cloud-tutorial-bind-mysql.md).