---
title: 'Tutoriel : Requête élastique avec Azure SQL Data Warehouse | Microsoft Docs'
description: Ce tutoriel utilise la fonctionnalité de requête élastique pour exécuter une requête dans Azure SQL Data Warehouse depuis Azure SQL Database.
services: sql-data-warehouse
author: hirokib
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/14/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: 355ae1c27d0af8f77c2c9bda61c3581562050fc4
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307090"
---
# <a name="tutorial-use-elastic-query-to-access-data-in-azure-sql-data-warehouse-from-azure-sql-database"></a>Tutoriel : Utiliser la requête élastique pour accéder à des données dans Azure SQL Data Warehouse Azure à partir d’Azure SQL Database

Ce tutoriel utilise la fonctionnalité de requête élastique pour exécuter une requête dans Azure SQL Data Warehouse depuis Azure SQL Database. 

## <a name="prerequisites-for-the-tutorial"></a>Configuration requise pour le didacticiel

Avant de commencer le didacticiel, vous devez satisfaire aux prérequis suivants :

1. Vous avez installé SQL Server Management Studio (SSMS).
2. Vous avez créé un serveur SQL Azure comportant une base de données et un entrepôt de données.
3. Vous avez configuré des règles de pare-feu pour accéder au serveur SQL Azure.

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>Configurer la connexion entre les instances SQL Data Warehouse et SQL Database 

1. À l’aide de SSMS ou d’un autre client de requête, ouvrez une nouvelle requête pour la base de données **master** sur votre serveur logique.

2. Créez une connexion et un utilisateur représentant la connexion entre la base de données SQL et l’entrepôt de données.

   ```sql
   CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
   ```

3. À l’aide de SSMS ou d’un autre client de requête, ouvrez une nouvelle requête pour **l’instance SQL Data Warehouse** sur votre serveur logique.

4. Créez un utilisateur sur l’instance de l’entrepôt de données avec la connexion que vous avez créée à l’étape 2.

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. Accordez des autorisations à l’utilisateur créé à l’étape 4 destinées à être exécutées par la base de données SQL. Dans cet exemple, l’autorisation est accordée uniquement pour une opération SELECT sur un schéma spécifique, illustrant la possibilité de limiter les requêtes à partir de la base de données SQL à un domaine spécifique. 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. À l’aide de SSMS ou d’un autre client de requête, ouvrez une nouvelle requête pour **l’instance de la base de données SQL** sur votre serveur logique.

7. Créez une clé principale si vous n’en avez pas déjà une. 

   ```sql
   CREATE MASTER KEY; 
   ```

8. Créez un fichier d’informations d’identification de niveau base de données en utilisant les informations d’identification que vous avez créées à l’étape 2.

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL SalesDBElasticCredential
   WITH IDENTITY = 'SalesDBLogin',
   SECRET = 'aReallyStrongPassword@#!';
   ```

9. Créez une source de données externe qui pointe vers l’instance de l’entrepôt de données.

   ```sql
   CREATE EXTERNAL DATA SOURCE EnterpriseDwSrc WITH 
       (TYPE = RDBMS, 
       LOCATION = '<SERVER NAME>.database.windows.net', 
       DATABASE_NAME = '<SQL DATA WAREHOUSE NAME>', 
       CREDENTIAL = SalesDBElasticCredential, 
   ) ;
   ```

10. Vous pouvez à présent créer des tables externes qui référencent cette source de données externe. Les requêtes qui utilisent ces tables sont envoyées à l’instance de l’entrepôt de données afin d’être traitées, puis elles sont renvoyées à l’instance de la base de données.


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>Requête élastique entre la base de données SQL et l’entrepôt de données SQL

Dans les étapes suivantes, nous allons créer une table dans notre instance de l’entrepôt de données avec plusieurs valeurs. Ensuite, nous allons montrer comment configurer une table externe pour interroger l’instance de l’entrepôt de données à partir de l’instance de base de données.

1. À l’aide de SSMS ou d’un autre client de requête, ouvrez une nouvelle requête pour **SQL Data Warehouse** sur votre serveur logique.

2. Exécutez la requête suivante pour créer une table **OrdersInformation** dans l’instance de l’entrepôt de données.

   ```sql
   CREATE TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL 
   ,   [CustomerID] [int] NOT NULL 
   ) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
   ```

3. À l’aide de SSMS ou d’un autre client de requête, ouvrez une nouvelle requête pour **la base de données SQL** sur votre serveur logique.

4. Exécutez la requête suivante pour créer une définition de table externe pointant vers la table **OrdersInformation** dans l’instance de l’entrepôt de données.

   ```sql
   CREATE EXTERNAL TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL
   ,   [CustomerID] [int] NOT NULL 
   ) 
   WITH 
   (
        DATA_SOURCE = EnterpriseDwSrc
   ,    SCHEMA_NAME = N'dbo'
   ,    OBJECT_NAME = N'OrderInformation'
   )
   ```

5. Comme vous pouvez le constater, vous disposez maintenant d’une définition de table externe dans votre **instance de base de données SQL**.

   ![Définition de table externe avec une requête élastique](media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. Exécutez la requête suivante, qui interroge l’instance de l’entrepôt de données. Vous devez obtenir les cinq valeurs que vous avez insérées à l’étape 2. 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> Notez que, malgré le peu de valeurs, l’exécution de cette requête prend beaucoup de temps. Quand vous utilisez une requête élastique avec un entrepôt de données, vous devez prendre en considération les coûts de traitement des requêtes et de déplacement sur le réseau. Utilisez l’exécution distante de requêtes élastiques quand la priorité est la puissance de calcul, et non la latence.

Félicitations, les notions de base de la fonctionnalité de requête élastique n’ont plus de secrets pour vous. 

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des recommandations, consultez [Meilleures pratiques pour l’utilisation de la requête élastique avec Azure SQL Data Warehouse](how-to-use-elastic-query-with-sql-data-warehouse.md).