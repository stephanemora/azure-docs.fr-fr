---
title: Tutoriel - Lier Azure Cache for Redis à votre application Azure Spring Cloud
description: Ce tutoriel vous montre comment lier Azure Cache pour Redis à votre application Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: 94f7b5a2363b7c53e0f70500e5a0a8cb6f64e611
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277516"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Lier Azure Cache for Redis à votre application Azure Spring Cloud 

Au lieu de configurer manuellement vos applications Spring Boot, vous pouvez lier automatiquement certains services Azure à vos applications à l’aide d’Azure Spring Cloud. Cet article explique comment lier votre application à Azure Cache for Redis.

## <a name="prerequisites"></a>Conditions préalables requises

* Une instance Azure Spring Cloud déployée
* Une instance du service Cache Azure pour Redis
* L’extension Azure Spring Cloud pour Azure CLI

Si vous n’avez pas encore déployé d’instance Azure Spring Cloud, suivez les étapes décrites dans ce [guide de démarrage rapide pour déployer une application Spring Cloud](spring-cloud-quickstart-launch-app-portal.md).

## <a name="bind-azure-cache-for-redis"></a>Lier Cache Azure pour Redis

1. Ajoutez la dépendance suivante au fichier pom.xml de votre projet :

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Supprimer les propriétés de `spring.redis.*` du fichier `application.properties`

1. Mettez à jour le déploiement actuel avec `az spring-cloud app update` ou créez un déploiement avec `az spring-cloud app deployment create`.

1. Accédez à la page du service Azure Spring Cloud dans le portail Azure. Accédez au **tableau de bord de l’application** et sélectionnez l’application à lier à Azure Cache for Redis. Il s’agit de l’application que vous avez mise à jour ou déployée à l’étape précédente.

1. Sélectionnez **Liaison de service**, puis sélectionnez **Créer une liaison de service**. Remplissez le formulaire en veillant à sélectionner la valeur **Azure Cache for Redis** pour le **type de liaison**, votre serveur Azure Cache for Redis, ainsi que l’option de clé **Primaire**.

1. Redémarrez l’application. La liaison doit maintenant fonctionner.

1. Pour vérifier que la liaison de service est correcte, sélectionnez le nom de la liaison et examinez ses détails. Le champ `property` doit se présenter comme ceci :
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment lier votre application Azure Spring Cloud à Azure Cache for Redis. Pour plus d’informations sur la liaison de services à votre application, passez au tutoriel concernant la liaison d’une application à une instance d’Azure Database pour MySQL.

> [!div class="nextstepaction"]
> [En savoir plus sur la liaison à une instance Azure Database pour MySQL](spring-cloud-tutorial-bind-mysql.md)
