---
title: Guide pratique pour lier Azure Database pour MySQL à votre application Azure Spring Cloud | Microsoft Docs
description: Cet article vous montre comment lier Azure MySQL à votre application Azure Spring Cloud.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: b6de5bb3b25c111d1b7775ea9570a4ae2cf45042
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607583"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>Didacticiel : Lier des services Azure à votre application Azure Spring Cloud : Azure Database pour MySQL

Azure Spring Cloud vous permet de lier automatiquement certains services Azure à vos applications au lieu de configurer manuellement votre application Spring Boot. Ce tutoriel vous montre comment lier votre application à Azure MySQL.

## <a name="prerequisites"></a>Prérequis

* Une instance Azure Spring Cloud déployée
* Un compte Azure Database pour MySQL
* D’Azure CLI

Si vous n’avez pas encore déployé d’instance Azure Spring Cloud, suivez les étapes décrites dans ce [démarrage rapide](spring-cloud-quickstart-launch-app-portal.md) pour déployer votre première application Spring Cloud.

## <a name="bind-azure-database-for-mysql"></a>Lier Azure Database pour MySQL

1. Notez le nom d’utilisateur et le mot de passe d’administrateur de votre compte Azure MySQL. Connectez-vous au serveur et créez une base de données nommée `testdb` à partir d’un client MySQL. Créez un compte non-administrateur.

1. Ajoutez la dépendance suivante dans le fichier `pom.xml` de votre projet.

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Supprimez les propriétés `spring.datasource.*`, le cas échéant, du fichier `application.properties`.

1. Mettez à jour le déploiement actuel avec `az spring-cloud app update` ou créez un déploiement pour ce changement avec `az spring-cloud app deployment create`.  Ces commandes mettent à jour ou créent l’application avec la nouvelle dépendance.

1. Accédez à la page du service Azure Spring Cloud dans le Portail Azure. Recherchez le **Tableau de bord de l’application** et sélectionnez l’application à lier à Azure MySQL.  Il s’agit de la même application que celle que vous avez mise à jour ou déployée à l’étape précédente. Sélectionnez `Service binding`, puis le bouton `Create service binding`. Remplissez le formulaire en veillant à sélectionner comme **Type de liaison** `Azure MySQL` (il s’agit du nom de base de données que vous avez utilisé précédemment). Sélectionnez également le nom d’utilisateur et le mot de passe que vous avez notés à la première étape.

1. Redémarrez l’application. La liaison doit alors fonctionner.

1. Pour vérifier que la liaison de service est correcte, sélectionnez le nom de la liaison et examinez ses détails. Le champ `property` doit ressembler à ceci :
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à lier votre application Azure Spring Cloud à une base de données MySQL.  Pour en savoir plus sur la gestion de votre service Azure Spring Cloud, lisez la suite consacrée à la découverte et à l’inscription des services.

> [!div class="nextstepaction"]
> [Découvrez comment activer la découverte et l’inscription de services à l’aide du registre de services Spring Cloud](spring-cloud-service-registration.md).

