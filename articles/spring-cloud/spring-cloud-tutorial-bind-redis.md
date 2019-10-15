---
title: Guide pratique pour lier Cache Azure pour Redis à votre application Azure Spring Cloud | Microsoft Docs
description: Découvrez comment lier Cache Azure pour Redis à votre application Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d8fa31207baecc80674fb11b492927800676b8a2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038238"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Didacticiel : Lier des services Azure à votre application Azure Spring Cloud : Cache Azure pour Redis

Azure Spring Cloud vous permet de lier automatiquement certains services Azure à vos applications, au lieu de configurer manuellement votre application Spring Boot. Cet article explique comment lier votre application à Cache Azure pour Redis.

## <a name="prerequisites"></a>Prérequis

* Une instance Azure Spring Cloud déployée
* Une instance du service Cache Azure pour Redis
* L’extension Azure Spring Cloud pour Azure CLI

Si nécessaire, installez l’extension Azure Spring Cloud pour Azure CLI avec la commande suivante :

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article.  Les outils Azure les plus courants sont préinstallés, notamment les dernières versions de Git, JDK, Maven et Azure CLI. Si vous êtes connecté à votre abonnement Azure, lancez votre [Azure Cloud Shell](https://shell.azure.com) à partir de shell.azure.com.  Vous pouvez découvrir plus d’informations sur Azure Cloud Shell [en lisant notre documentation](../cloud-shell/overview.md).

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