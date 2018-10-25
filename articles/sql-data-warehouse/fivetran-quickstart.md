---
title: Démarrage rapide de Fivetran avec Azure SQL Data Warehouse | Microsoft Docs
description: Devenez rapidement opérationnel avec Fivetran et Azure SQL Data Warehouse.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 8e738becfe356908af5baffc0ebf225916b2616e
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49355205"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Devenir rapidement opérationnel avec Fivetran et SQL Data Warehouse

Ce guide de démarrage rapide part du principe que vous disposez d’une instance préexistante de SQL Data Warehouse.

## <a name="setup-connection"></a>Configurer la connexion

1. Recherchez le nom complet du serveur et le nom de la base de données pour la connexion à Azure SQL Data Warehouse.

   [Comment trouver le nom du serveur et le nom de la base de données à partir du portail ?](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connect-overview)

2. Dans l’Assistant Installation, indiquez si vous souhaitez vous connecter directement à votre base de données ou par le biais d’un tunnel SSH.

   Si vous décidez de vous connecter directement à votre base de données, vous devez créer une règle de pare-feu pour autoriser l’accès. Cette méthode est la plus simple et la plus sécurisée.

   Si vous décidez de vous connecter par le biais d’un tunnel SSH, Fivetran se connecte à un serveur distinct de votre réseau qui fournit un tunnel SSH vers votre base de données. Cette méthode est nécessaire si votre base de données se trouve dans un sous-réseau inaccessible d’un réseau virtuel.

3. Ajoutez l’adresse IP « 52.0.2.4 » à votre pare-feu au niveau du serveur pour autoriser les connexions entrantes à votre entrepôt Azure SQL Data Warehouse à partir de Fivetran.

   [Comment ajouter un pare-feu au niveau du serveur ?](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal#create-a-server-level-firewall-rule)

## <a name="setup-user-credentials"></a>Configurer les informations d’identification de l’utilisateur

Connectez-vous à votre entrepôt Azure SQL Data Warehouse à l’aide de SQL Server Management Studio ou de l’outil de votre choix en tant qu’utilisateur administrateur de serveur et exécutez les commandes SQL suivantes pour créer un utilisateur pour Fivetran :

Dans la base de données master : ` CREATE LOGIN fivetran WITH PASSWORD = '<password>'; `

Dans la base de données SQL Data Warehouse :

```
CREATE USER fivetran_user_without_login without login;
CREATE USER fivetran FOR LOGIN fivetran;
GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
```

Une fois l’utilisateur fivetran créé, accordez-lui les autorisations suivantes à votre entrepôt :

```
GRANT CONTROL to fivetran;
```

Ajoutez une classe de ressources appropriée à l’utilisateur créé selon la mémoire requise pour la création d’index columnstore. Par exemple, des intégrations comme Marketo et Salesforce nécessitent une classe de ressources plus élevée. En effet, il faut plus de mémoire pour créer des index columnstore compte tenu du nombre de colonnes et du volume de données.

L’utilisation de classes de ressources statiques est recommandée. Vous pouvez commencer par la classe de ressources `staticrc20`. Celle-ci alloue 200 Mo à l’utilisateur, quel que soit le niveau de performance que vous utilisez. Si l’indexation columnstore échoue avec la classe de ressources actuelle, vous devez augmenter cette classe.

```
EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
```

Pour plus d’informations, consultez les documents relatifs aux [limites de mémoire et de concurrence](https://docs.microsoft.com/azure/sql-data-warehouse/memory-and-concurrency-limits#data-warehouse-limits) et aux [classes de ressources](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression#ways-to-allocate-more-memory).

L’autorisation CONTROL est nécessaire pour créer des informations d’identification incluses dans l’étendue de la base de données qui seront utilisées lors du chargement de fichiers à partir de Stockage Blob à l’aide de PolyBase.

Entrez les informations d’identification pour accéder à Azure SQL Data Warehouse :

1. Hôte (nom de votre serveur)
2. Port
3. Base de données
4. Utilisateur (le nom d’utilisateur doit être `fivetran@<server_name>` où `<server_name>` fait partie de l’URI de votre hôte Azure : `<server_name>.database.windows.net`)
5. Mot de passe