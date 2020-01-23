---
title: Tutoriel - Guide pratique pour lier une instance d’Azure Database pour MySQL à votre application Azure Spring Cloud
description: Ce tutoriel vous montre comment lier une instance d’Azure Database pour MySQL à votre application Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 657aa70d77fd1af9fd2121a3e98ea3aca7773642
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277539"
---
# <a name="tutorial-bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Tutoriel : Lier une instance Azure Database pour MySQL à votre application Azure Spring Cloud 

Azure Spring Cloud vous permet de lier automatiquement certains services Azure à vos applications au lieu de configurer manuellement votre application Spring Boot. Ce tutoriel vous montre comment lier votre application à votre instance Azure Database pour MySQL.

## <a name="prerequisites"></a>Conditions préalables requises

* Une instance Azure Spring Cloud déployée
* Un compte Azure Database pour MySQL
* Azure CLI

Si vous n’avez pas déployé d’instance Azure Spring Cloud, suivez les instructions du [Démarrage rapide : Lancer une application Azure Spring Cloud à l’aide du portail Azure](spring-cloud-quickstart-launch-app-portal.md) pour déployer votre première application Cloud Spring.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Lier votre application à votre instance Azure Database pour MySQL

1. Notez le nom d’utilisateur et le mot de passe de l’administrateur de votre compte Azure Database pour MySQL. 

1. Connectez-vous au serveur, créez une base de données nommée **testdb** à partir d’un client MySQL, puis créez un nouveau compte non-administrateur.

1. Dans le fichier *pom.xml* de votre projet, ajoutez la dépendance suivante :

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Dans le fichier *application.properties*, supprimez toutes les propriétés `spring.datasource.*`.

1. Mettez à jour le déploiement actuel en exécutant `az spring-cloud app update` ou créez un déploiement pour ce changement en exécutant `az spring-cloud app deployment create`.  Ces commandes mettent à jour ou créent l’application avec la nouvelle dépendance.

1. Dans le portail Azure, dans la page de votre service **Azure Spring Cloud**, recherchez le **Tableau de bord d’application**, puis sélectionnez l’application à lier à votre instance Azure Database pour MySQL.  Il s’agit de la même application que celle que vous avez mise à jour ou déployée à l’étape précédente. 

1. Sélectionnez **Liaison de service**, puis sélectionnez le bouton **Créer une liaison de service**. 

1. Remplissez le formulaire en sélectionnant **Azure MySQL** comme **Type de liaison** et en utilisant le même nom de base de données que celui que vous avez utilisé précédemment, et les mêmes nom d’utilisateur et mot de passe que ceux que vous avez notés à la première étape.

1. Redémarrez l’application. La liaison doit maintenant fonctionner.

1. Pour vérifier que la liaison de service est correcte, sélectionnez le nom de liaison et examinez ses détails. Le champ `property` doit se présenter comme ceci :
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à lier votre application Azure Spring Cloud à une instance Azure Database pour MySQL.  Pour en savoir plus sur la gestion de votre service Azure Spring Cloud, lisez l’article sur la découverte et l’inscription des services.

> [!div class="nextstepaction"]
> [Activer la découverte et l’inscription de services à l’aide du registre de service Spring Cloud](spring-cloud-service-registration.md)
