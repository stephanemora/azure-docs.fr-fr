---
title: 'SSMS : Connecter et interroger des données'
description: Découvrez comment vous connecter à Base de données SQL sur Azure avec SQL Server Management Studio (SSMS). Ensuite, exécutez des instructions Transact-SQL (T-SQL) pour interroger et modifier des données.
keywords: connexion à une base de données sql, sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/10/2020
ms.openlocfilehash: 31bd47128a272e75d7021180b536fe6bf7420f55
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79299292"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Démarrage rapide : Utilisez SQL Server Management Studio pour se connecter et interroger une base de données Azure SQL

Dans ce guide de démarrage rapide, vous allez voir comment utiliser SQL Server Management Studio (SSMS) pour vous connecter à une base de données Azure SQL et exécuter des requêtes.

## <a name="prerequisites"></a>Prérequis

Ce guide de démarrage rapide nécessite les éléments suivants :

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).
- L’exemple de base de données AdventureWorksLT. Si vous avez besoin d’une copie de travail de la base de données AdventureWorksLT, vous pouvez en créer une en suivant les instructions du guide de démarrage rapide [Créer une base de données Azure SQL](sql-database-single-database-get-started.md).
    - Les scripts fournis dans cet article utilisent la base de données AdventureWorksLT. Si vous utilisez une instance managée, vous devez importer la base de données AdventureWorks dans une base de données d’instance, ou modifier les scripts fournis dans cet article pour utiliser la base de données Wide World Importers.

Si vous souhaitez simplement exécuter des requêtes ad hoc sans installer SSMS, consultez [Démarrage rapide : Utiliser l’éditeur de requête du portail Azure pour interroger une base de données SQL](sql-database-connect-query-portal.md).

## <a name="get-sql-server-connection-information"></a>Obtenir des informations de connexion SQL Server

Procurez-vous les informations de connexion dont vous avez besoin pour vous connecter à la base de données. Pour suivre ce guide, vous aurez besoin du nom complet du serveur ou de l’hôte, du nom de la base de données et des informations de connexion.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Accédez à la **base de données SQL** ou à l’**instance managée SQL** que vous souhaitez interroger.

3. Dans la page **Vue d’ensemble**, copiez le nom complet du serveur. Celui-ci se trouve en regard de **Nom du serveur** pour une base de données, ou en regard de **Hôte** pour une instance managée. Le nom complet ressemble à ceci : *nomserveur.database.windows.net*, où « nomserveur » correspond au nom de votre serveur.

## <a name="connect-to-your-database"></a>Connectez-vous à votre base de données

Dans SSMS, connectez-vous à votre serveur Azure SQL Database.

> [!IMPORTANT]
> Un serveur Azure SQL Database écoute sur le port 1433. Pour vous connecter à un serveur SQL Database derrière un pare-feu d’entreprise, le port de ce dernier doit être ouvert.

1. Ouvrez SSMS.

2. La boîte de dialogue **Se connecter au serveur** s’affiche. Entrez les informations suivantes :

   | Paramètre      | Valeur suggérée    | Description |
   | ------------ | ------------------ | ----------- |
   | **Type de serveur** | Moteur de base de données | Valeur requise |
   | **Nom du serveur** | Nom complet du serveur | Par exemple : **servername.database.windows.net**. |
   | **Authentification** | l’authentification SQL Server | Ce didacticiel utilise l’authentification SQL. |
   | **Connexion** | ID utilisateur du compte d'administrateur de serveur | ID utilisateur du compte d'administrateur de serveur utilisé pour créer le serveur. |
   | **Mot de passe** | Mot de passe du compte d'administrateur de serveur | Mot de passe du compte d'administrateur de serveur utilisé pour créer le serveur. |
   ||||

   ![connect to server](./media/sql-database-connect-query-ssms/connect.png)  

3. Sélectionnez **Options** dans la boîte de dialogue **Se connecter au serveur**. Dans le menu déroulant **Se connecter à la base de données**, sélectionnez **mySampleDatabase**. Lorsque vous suivez le guide de démarrage rapide dans la [section Prérequis](#prerequisites), vous créez une base de données AdventureWorksLT nommée mySampleDatabase. Si votre copie de travail de la base de données AdventureWorks porte un nom différent de mySampleDatabase, sélectionnez-la à la place de celle-ci.

   ![connexion à la base de données sur le serveur](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Sélectionnez **Connecter**. La fenêtre Explorateur d’objets s’ouvre.

5. Pour afficher les objets de la base de données, développez **Bases de données**, puis développez le nœud de votre base de données.

   ![Objets mySampleDatabase](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Interroger des données

Exécutez ce code Transact-SQL [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) pour rechercher les 20 premiers produits par catégorie.

1. Dans l’Explorateur d’objets, cliquez avec le bouton droit sur **mySampleDatabase**, puis sélectionnez **Nouvelle requête**. Une nouvelle fenêtre de requête connectée à votre base de données s’ouvre.

2. Dans la fenêtre de requête, collez la requête SQL suivante :

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Dans la barre d’outils, sélectionnez **Exécuter** pour exécuter la requête et récupérer les données des tables `Product` et `ProductCategory`.

    ![requête pour récupérer des données à partir de la table Product et ProductCategory](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>Insertion des données

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

2. Sélectionnez **Exécuter** pour insérer une nouvelle ligne dans la table `Product`. Le volet **Messages** affiche **(1 ligne affectée)** .

#### <a name="view-the-result"></a>Afficher le résultat

1. Remplacez la requête précédente par celle-ci.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Sélectionnez **Exécuter**. Le résultat suivant s’affiche.

   ![résultat de requête sur la table Product](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>Mettre à jour des données

Exécutez ce code Transact-SQL [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) pour modifier votre nouveau produit.

1. Remplacez la requête précédente par celle qui retourne le nouvel enregistrement créé précédemment :

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Sélectionnez **Exécuter** pour mettre à jour la ligne spécifiée dans la table `Product`. Le volet **Messages** affiche **(1 ligne affectée)** .

### <a name="delete-data"></a>Suppression de données

Exécutez ce code Transact-SQL [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) pour supprimer votre nouveau produit.

1. Remplacez la requête précédente par celle-ci.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Sélectionnez **Exécuter** pour supprimer la ligne spécifiée dans la table `Product`. Le volet **Messages** affiche **(1 ligne affectée)** .

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
