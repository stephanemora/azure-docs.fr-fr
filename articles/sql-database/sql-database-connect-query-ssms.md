---
title: 'SSMS : Se connecter et interroger des données dans Azure SQL Database | Microsoft Docs'
description: Découvrez comment vous connecter à Base de données SQL sur Azure avec SQL Server Management Studio (SSMS). Ensuite, exécutez des instructions Transact-SQL (T-SQL) pour interroger et modifier des données.
keywords: connexion à une base de données sql, sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 636adcf15479fe8e072b833b59ddc78b5f820550
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53745072"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Démarrage rapide : Utilisez SQL Server Management Studio pour se connecter et interroger une base de données Azure SQL

Dans ce guide de démarrage rapide, vous allez utiliser [SQL Server Management Studio][ssms-install-latest-84g] (SSMS) pour établir la connexion à une base de données Azure SQL. Vous allez ensuite utiliser les instructions Transact-SQL pour interroger les données, les mettre à jour et en supprimer. Vous pouvez utiliser (SSMS) pour gérer les infrastructures SQL, de SQL Server à SQL Database pour Microsoft Windows.  

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

* règle de pare-feu au niveau du serveur. Pour plus d’informations, consultez [Créer une règle de pare-feu au niveau du serveur](sql-database-get-started-portal-firewall.md).

#### <a name="install-the-latest-ssms"></a>Installer la dernière version de SSMS

Avant de commencer, assurez-vous d'avoir installé la dernière version de [SSMS][ssms-install-latest-84g]. 

## <a name="sql-server-connection-information"></a>Informations de connexion SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="connect-to-your-database"></a>Connectez-vous à votre base de données

Dans SMSS, connectez-vous à votre serveur Azure SQL Database. 

> [!IMPORTANT]
> Un serveur logique Azure SQL Database écoute sur le port 1433. Pour vous connecter à un serveur logique derrière un pare-feu d’entreprise, le port de ce dernier doit être ouvert.
>

1. Ouvrez SSMS. La boîte de dialogue **Se connecter au serveur** s’affiche.

2. Entrez les informations suivantes :

   | Paramètre      | Valeur suggérée    | Description | 
   | ------------ | ------------------ | ----------- | 
   | **Type de serveur** | Moteur de base de données | Valeur requise |
   | **Nom du serveur** | Nom complet du serveur | Similaire à ce qui suit : **mynewserver20170313.database.windows.net**. |
   | **Authentification** | l’authentification SQL Server | Ce tutoriel utilise l’authentification SQL. |
   | **Connexion** | ID utilisateur du compte d'administrateur de serveur | ID utilisateur du compte d'administrateur de serveur utilisé pour créer le serveur. |
   | **Mot de passe** | Mot de passe du compte d'administrateur de serveur | Mot de passe du compte d'administrateur de serveur utilisé pour créer le serveur. |
   ||||

   ![connect to server](./media/sql-database-connect-query-ssms/connect.png)  

3. Sélectionnez **Options** dans la boîte de dialogue **Se connecter au serveur**. Dans le menu déroulant **Se connecter à une base de données**, sélectionnez **mySampleDatabase**.

   ![connexion à la base de données sur le serveur](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Sélectionnez **Connecter**. La fenêtre Explorateur d’objets s’ouvre. 

5. Pour afficher les objets de la base de données, développez **Bases de données**, puis **mySampleDatabase**.

   ![afficher les objets de base de données](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Données de requête

Exécutez ce code Transact-SQL [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) pour rechercher les 20 premiers produits par catégorie.

1. Dans l’Explorateur d’objets, cliquez avec le bouton droit sur **mySampleDatabase**, puis sélectionnez **Nouvelle requête**. Une nouvelle fenêtre de requête connectée à votre base de données s’ouvre.

2. Dans la fenêtre de requête, collez cette requête SQL.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Dans la barre d’outils, sélectionnez **Exécuter** pour récupérer des données à partir des tables `Product` et `ProductCategory`.

    ![Requête pour récupérer des données à partir de 2 tables](./media/sql-database-connect-query-ssms/query2.png)

## <a name="insert-data"></a>Insertion des données

Exécutez ce code Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) pour créer un produit dans la table `SalesLT.Product`.

1. Remplacez la requête précédente par celle-ci.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Sélectionnez **Exécuter** pour insérer une nouvelle ligne dans la table `Product`. Le volet **Messages** affiche **(1 ligne affectée)**.

## <a name="view-the-result"></a>Afficher le résultat

1. Remplacez la requête précédente par celle-ci.

   ```sql
   SELECT * FROM [SalesLT].[Product] 
   WHERE Name='myNewProduct' 

2. Select **Execute**. The following result appears. 

   ![result](./media/sql-database-connect-query-ssms/result.png)

 
## Update data

Run this [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL code to modify your new product.

1. Replace the previous query with this one.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Sélectionnez **Exécuter** pour mettre à jour la ligne spécifiée dans la table `Product`. Le volet **Messages** affiche **(1 ligne affectée)**.

## <a name="delete-data"></a>Suppression de données

Exécutez ce code Transact-SQL [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) pour supprimer votre nouveau produit.

1. Remplacez la requête précédente par celle-ci.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Sélectionnez **Exécuter** pour supprimer la ligne spécifiée dans la table `Product`. Le volet **Messages** affiche **(1 ligne affectée)**.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur SSMS, consultez [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Pour vous connecter et exécuter des requêtes via le portail Azure, consultez [Se connecter et effectuer des requêtes avec l’éditeur de requêtes SQL du portail Azure](sql-database-connect-query-portal.md).
- Pour vous connecter et exécuter des requêtes à l’aide de Visual Studio Code, consultez l’article décrivant comment [se connecter et effectuer des requêtes avec Visual Studio Code](sql-database-connect-query-vscode.md).
- Pour vous connecter et exécuter des requêtes à l’aide de .NET, consultez l’article décrivant comment [se connecter et effectuer des requêtes avec .NET](sql-database-connect-query-dotnet.md).
- Pour vous connecter et exécuter des requêtes avec PHP, consultez la page [se connecter et effectuer des requêtes avec PHP](sql-database-connect-query-php.md).
- Pour vous connecter et exécuter des requêtes avec Node.js, consultez la page [se connecter et effectuer des requêtes avec Node.js](sql-database-connect-query-nodejs.md).
- Pour vous connecter et exécuter des requêtes avec Java, consultez la page [se connecter et effectuer des requêtes avec Java](sql-database-connect-query-java.md).
- Pour vous connecter et exécuter des requêtes avec Python, consultez la page [se connecter et effectuer des requêtes avec Python](sql-database-connect-query-python.md).
- Pour vous connecter et exécuter des requêtes avec Ruby, consultez la page [se connecter et effectuer des requêtes avec Ruby](sql-database-connect-query-ruby.md).


<!-- Article link references. -->

[ssms-install-latest-84g]: https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms

