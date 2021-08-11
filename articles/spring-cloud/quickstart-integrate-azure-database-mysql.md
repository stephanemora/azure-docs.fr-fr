---
title: Démarrage rapide - Intégrer à Azure Database pour MySQL
description: Explique comment provisionner et préparer une instance Azure Database pour MySQL, puis comment configurer Pet Clinic sur Azure Spring Cloud pour l’utiliser comme base de données persistante avec une seule commande.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 05/13/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 223a3ba0fdb11824cd7c45218dc72271054db428
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114470674"
---
# <a name="quickstart-integrate-azure-spring-cloud-with-azure-database-for-mysql"></a>Démarrage rapide : Intégrer Azure Spring Cloud à Azure Database pour MySQL

Pet Clinic, tel que déployé dans la configuration par défaut dans [Démarrage rapide : Créer et déployer des applications sur Azure Spring Cloud](quickstart-deploy-apps.md), utilise une base de données en mémoire (HSQLDB) qui est remplie avec les données au démarrage. Ce guide de démarrage rapide explique comment provisionner et préparer une instance Azure Database pour MySQL, puis comment configurer Pet Clinic sur Azure Spring Cloud pour l’utiliser comme base de données persistante avec une seule commande.

## <a name="variables-preparation"></a>Préparation des variables

Nous allons utiliser les valeurs suivantes. Enregistrez-les dans un fichier texte ou des variables d’environnement pour éviter les erreurs.

```bash
export RESOURCE_GROUP=<resource-group-name> # customize this
export MYSQL_SERVER_NAME=<mysql-server-name> # customize this
export MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_NAME}.mysql.database.azure.com
export MYSQL_SERVER_ADMIN_NAME=<admin-name> # customize this
export MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_NAME}\@${MYSQL_SERVER_NAME}
export MYSQL_SERVER_ADMIN_PASSWORD=<password> # customize this
export MYSQL_DATABASE_NAME=petclinic
```

## <a name="prepare-an-azure-database-for-mysql-instance"></a>Préparer une instance Azure Database pour MySQL

1. Créez un serveur Azure Database pour MySQL.

    ```azcli
    az mysql server create --resource-group ${RESOURCE_GROUP} \
        --name ${MYSQL_SERVER_NAME} \
        --admin-user ${MYSQL_SERVER_ADMIN_NAME} \
        --admin-password ${MYSQL_SERVER_ADMIN_PASSWORD} \
        --sku-name GP_Gen5_2 \
        --ssl-enforcement Disabled \
        --version 5.7
    ```

1. Autorisez l’accès à partir des ressources Azure.

    ```azcli
    az mysql server firewall-rule create --name allAzureIPs \
     --server ${MYSQL_SERVER_NAME} \
     --resource-group ${RESOURCE_GROUP} \
     --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
    ```

1. Autorisez l’accès à partir de votre ordinateur de développement pour les tests.

    ```azcli
    az mysql server firewall-rule create --name devMachine \
     --server ${MYSQL_SERVER_NAME} \
     --resource-group ${RESOURCE_GROUP} \
     --start-ip-address <ip-address-of-your-dev-machine> \
     --end-ip-address <ip-address-of-your-dev-machine>
    ```

1. Prolongez le délai d’expiration de connexion.

    ```azcli
    az mysql server configuration set --name wait_timeout \
     --resource-group ${RESOURCE_GROUP} \
     --server ${MYSQL_SERVER_NAME} --value 2147483
    ```

1. Créez la base de données dans le serveur MySQL et définissez les paramètres correspondants.

    ```sql
    // SUBSTITUTE values
    mysql -u ${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
     -h ${MYSQL_SERVER_FULL_NAME} -P 3306 -p
    
    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 64379
    Server version: 5.6.39.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> CREATE DATABASE petclinic;
    Query OK, 1 row affected (0.10 sec)
    
    mysql> CREATE USER 'root' IDENTIFIED BY 'petclinic';
    Query OK, 0 rows affected (0.11 sec)
    
    mysql> GRANT ALL PRIVILEGES ON petclinic.* TO 'root';
    Query OK, 0 rows affected (1.29 sec)
    
    mysql> CALL mysql.az_load_timezone();
    Query OK, 3179 rows affected, 1 warning (6.34 sec)
    
    mysql> SELECT name FROM mysql.time_zone_name;
    ...
    
    mysql> quit
    Bye
    ```

1. Définissez le fuseau horaire.

    ```azcli
    az mysql server configuration set --name time_zone \
     --resource-group ${RESOURCE_GROUP} \
     --server ${MYSQL_SERVER_NAME} --value "US/Pacific"
    ```

## <a name="update-apps-to-use-mysql-database"></a>Mettre à jour les applications pour utiliser la base de données MySQL

Pour activer MySQL en tant que base de données pour l’exemple d’application, mettez simplement à jour l’application *customer-service* avec les informations d’identification de base de données et MySQL de profil actif en tant que variables d’environnement.

```azcli
az spring-cloud app update --name customers-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
```

## <a name="update-extra-apps"></a>Mettre à jour des applications supplémentaires

```azcli
az spring-cloud app update --name api-gateway \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql"
az spring-cloud app update --name admin-server \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql"
az spring-cloud app update --name customers-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
az spring-cloud app update --name vets-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
az spring-cloud app update --name visits-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
```

## <a name="next-steps"></a>Étapes suivantes
* [Lier une instance Azure Database pour MySQL à votre application Azure Spring Cloud](how-to-bind-mysql.md)
* [Utiliser une identité managée pour connecter Azure SQL Database à une application Azure Spring Cloud](./connect-managed-identity-to-azure-sql.md)