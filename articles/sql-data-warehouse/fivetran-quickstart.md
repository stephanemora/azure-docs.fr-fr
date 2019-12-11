---
title: Démarrage rapide de Fivetran
description: Devenez rapidement opérationnel avec Fivetran et Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c9b4a15cd6cbae80d80407ba929bfbfa1402eeb5
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689232"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Devenir rapidement opérationnel avec Fivetran et SQL Data Warehouse

Ce guide de démarrage rapide décrit comment configurer un nouvel utilisateur Fivetran pour travailler avec Azure SQL Data Warehouse. Cet article suppose que vous disposez d’une instance de SQL Data Warehouse.

## <a name="set-up-a-connection"></a>Configurer une connexion

1. Recherchez le nom complet du serveur et le nom de la base de données utilisés pour vous connecter à Azure SQL Data Warehouse.
    
    Si vous avez besoin d’aide pour trouver ces informations, consultez [Se connecter à Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md).

2. Dans l’Assistant Installation, indiquez si vous souhaitez vous connecter directement à votre base de données ou en utilisant un tunnel SSH.

   Si vous choisissez de vous connecter directement à votre base de données, vous devez créer une règle de pare-feu pour autoriser l’accès. Cette méthode est la plus simple et la plus sécurisée.

   Si vous choisissez de vous connecter en utilisant un tunnel SSH, Fivetran se connecte à un serveur distinct sur votre réseau. Le serveur fournit un tunnel SSH vers votre base de données. Vous devez utiliser cette méthode si votre base de données se trouve dans un sous-réseau inaccessible d’un réseau virtuel.

3. Ajoutez l’adresse IP **52.0.2.4** à votre pare-feu au niveau du serveur pour autoriser les connexions entrantes à votre instance SQL Data Warehouse à partir de Fivetran.

   Pour plus d’informations, consultez [Créer une règle de pare-feu au niveau du serveur](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Configurer les informations d’identification de l’utilisateur

1. Connectez-vous à votre instance Azure SQL Data Warehouse à l’aide de SQL Server Management Studio ou de l’outil que vous préférez. Connectez-vous en tant qu’un utilisateur administrateur du serveur. Ensuite, exécutez les commandes SQL suivantes afin de créer un utilisateur pour Fivetran :
    - Dans la base de données master : 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - Dans la base de données SQL Data Warehouse :

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Accordez à l’utilisateur Fivetran les autorisations suivantes sur votre entrepôt :

    ```
    GRANT CONTROL to fivetran;
    ```

    L’autorisation CONTROL est requise pour créer des informations d’identification incluses dans l’étendue de la base de données qui sont utilisées lorsqu’un utilisateur charge des fichiers à partir du Stockage Blob Azure à l’aide de PolyBase.

3. Ajoutez une classe de ressources appropriée à l’utilisateur Fivetran. La classe de ressources que vous utilisez dépend de la mémoire qui est requise pour créer un index columnstore. Par exemple, les intégrations avec des produits telles que Marketo et Salesforce nécessitent une classe de ressources supérieure en raison du grand nombre de colonnes et du plus grand volume de données utilisées par les produits. Une classe de ressources supérieure nécessite davantage de mémoire pour créer des index columnstore.

    Nous vous recommandons d’utiliser les classes de ressources statiques. Vous pouvez démarrer avec la classe de ressources `staticrc20`. La classe de ressources `staticrc20` alloue 200 Mo alloue pour chaque utilisateur, quel que soit le niveau de performance que vous utilisez. Si l’indexation columnstore échoue au niveau de classe de ressources actuelle, augmentez la classe de ressources.

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Pour plus d’informations, consultez les documents relatifs aux [limites de mémoire et de concurrence](memory-concurrency-limits.md) et aux [classes de ressources](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Se connecter à Fivetran

Pour vous connecter à Fivetran, entrez les informations d’identification que vous utilisez pour accéder à SQL Data Warehouse : 

* Hôte (nom de votre serveur).
* Port.
* Base de données.
* Utilisateur (le nom d’utilisateur doit être **fivetran\@_nom_serveur_** où *nom_serveur* fait partie de votre URI d’hôte Azure : **_nom\_serveur_.database.windows.net**).
* Password.
