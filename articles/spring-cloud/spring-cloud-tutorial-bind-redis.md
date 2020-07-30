---
title: Lier Azure Cache for Redis à votre application Azure Spring Cloud
description: Découvrez comment lier Cache Azure pour Redis à votre application Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 6bbd54be46effe324199639477f9ca4ab31bea98
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091400"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Lier Azure Cache for Redis à votre application Azure Spring Cloud 

Au lieu de configurer manuellement vos applications Spring Boot, vous pouvez lier automatiquement certains services Azure à vos applications à l’aide d’Azure Spring Cloud. Cet article explique comment lier votre application à Azure Cache for Redis.

## <a name="prerequisites"></a>Prérequis

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

Dans cet article, vous avez découvert comment lier votre application Azure Spring Cloud à Azure Cache pour Redis. Pour en savoir plus sur la liaison de services à votre application, consultez [Lier à une instance Azure Database pour MySQL](spring-cloud-tutorial-bind-mysql.md).
