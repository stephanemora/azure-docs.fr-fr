---
title: Créer et utiliser des tables de registre pouvant être mises à jour
description: Comment créer et utiliser des tables de registre pouvant être mises à jour dans Azure SQL Database
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 43af922a23fa4e48a9b9efa7d292b4f03b06b94d
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386316"
---
# <a name="create-and-use-updatable-ledger-tables"></a>Créer et utiliser des tables de registre pouvant être mises à jour

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le Registre Azure SQL Database est actuellement en **préversion publique**.

Cet article explique comment créer une [table de registre pouvant être mise à jour](ledger-updatable-ledger-tables.md) dans Azure SQL Database, insérer des valeurs dans votre table de registre pouvant être mise à jour, effectuer des mises à jour des données et afficher les résultats à l’aide de la vue du registre. Nous allons utiliser un exemple d’application bancaire qui suit le solde de ses clients. Notre exemple vous donne un aperçu pratique de la relation entre la table de registre pouvant être mise à jour et la table d’historique correspondante et la vue du registre correspondantes.

## <a name="prerequisite"></a>Prérequis

- Avoir une base de données Azure SQL avec le registre activé. Consultez [Démarrage rapide : Créer une base de données Azure SQL avec le registre activé](ledger-create-a-single-database-with-ledger-enabled.md) si vous n’avez pas encore créé de base de données Azure SQL.
- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) ou [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)

## <a name="creating-an-updatable-ledger-table"></a>Création d’une table de registre pouvant être mise à jour

Nous allons créer une table de solde de compte avec le schéma suivant.  

| Nom de la colonne | Type de données      | Description                         |
| ----------- | -------------- | ----------------------------------- |
| CustomerID  | int            | ID client - Clé primaire en cluster |
| LastName    | varchar (50)   | Nom du client                  |
| FirstName   | varchar (50)   | Prénom du client                 |
| Balance     | decimal (10,2) | Solde du compte                     |

> [!IMPORTANT]
> La création de tables de registre pouvant être mises à jour nécessite l’autorisation **ENABLE LEDGER**. Pour plus d’informations sur les autorisations associées aux tables du registre, consultez [Autorisations](/sql/relational-databases/security/permissions-database-engine#asdbpermissions). 

1. À l’aide de [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) ou d’[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), créez un schéma et une table appelés `[Account].[Balance]`.

   ```sql
   CREATE SCHEMA [Account]
   GO
   
   CREATE TABLE [Account].[Balance]
   (
       [CustomerID] INT NOT NULL PRIMARY KEY CLUSTERED,
       [LastName] VARCHAR (50) NOT NULL,
       [FirstName] VARCHAR (50) NOT NULL,
       [Balance] DECIMAL (10,2) NOT NULL
   )
   WITH 
   (
    SYSTEM_VERSIONING = ON,
    LEDGER = ON
   );
   GO
   ```

    > [!NOTE]
    > La spécification de l’argument `LEDGER = ON` est facultative si vous avez activé la base de données du registre lorsque vous avez créé votre instance Azure SQL Database. 
    >
    > Dans l’exemple ci-dessus, le système génère les noms des colonnes [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) dans la table, le nom de la [vue du registre](ledger-updatable-ledger-tables.md#ledger-view) et les noms des [colonnes de la vue du registre](ledger-updatable-ledger-tables.md#ledger-view-schema).
    >
    > Les noms des colonnes de la vue de registre peuvent être personnalisés au moment de la création de la table à l’aide du paramètre `<ledger_view_option>` avec l’instruction [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true). Les colonnes `GENERATED ALWAYS`, ainsi que le nom de la [table d’historique](ledger-updatable-ledger-tables.md#history-table), peuvent être personnalisés. Pour plus d’informations, consultez [Options de la vue de registre](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#ledger-view-options) et les exemples correspondants dans [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true##x-creating-a-updatable-ledger-table).

1. Lors de la création de la [table de registre pouvant être mise à jour](ledger-updatable-ledger-tables.md), la table d’historique et la vue du registre correspondantes sont également créées. Exécutez le code T-SQL suivant pour afficher la nouvelle table et la nouvelle vue.

   ```sql
   SELECT 
   ts.[name] + '.' + t.[name] AS [ledger_table_name]
   , hs.[name] + '.' + h.[name] AS [history_table_name]
   , vs.[name] + '.' + v.[name] AS [ledger_view_name]
   FROM sys.tables AS t
   JOIN sys.tables AS h ON (h.[object_id] = t.[history_table_id])
   JOIN sys.views v ON (v.[object_id] = t.[ledger_view_id])
   JOIN sys.schemas ts ON (ts.[schema_id] = t.[schema_id])
   JOIN sys.schemas hs ON (hs.[schema_id] = h.[schema_id])
   JOIN sys.schemas vs ON (vs.[schema_id] = v.[schema_id])
   ```

   :::image type="content" source="media/ledger/ledger-updatable-how-to-new-tables.png" alt-text="Interroger les nouvelles tables du registre":::

1. Insérez un client, `Nick Jones` en tant que nouveau client avec un solde d’ouverture de 50 $.

   ```sql
   INSERT INTO [Account].[Balance]
   VALUES (1, 'Jones', 'Nick', 50)
   ```

1. Insérez trois nouveaux clients, `John Smith`, `Joe Smith` et `Mary Michaels` en tant que nouveaux clients avec des soldes ouvrants de respectivement 500, 30 et 200 $.

   ```sql
   INSERT INTO [Account].[Balance]
   VALUES (2, 'Smith', 'John', 500),
   (3, 'Smith', 'Joe', 30),
   (4, 'Michaels', 'Mary', 200)
   ```

1. Affichez la table de registre pouvant être mise à jour `[Account].[Balance]`, en spécifiant les colonnes [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) ajoutées à la table.

   ```sql
   SELECT * 
         ,[ledger_start_transaction_id]
         ,[ledger_end_transaction_id]
         ,[ledger_start_sequence_number]
         ,[ledger_end_sequence_number]
   FROM [Account].[Balance] 
   ```

   Dans la fenêtre de résultats, vous verrez d’abord les valeurs insérées par vos commandes T-SQL, ainsi que les métadonnées système utilisées pour la traçabilité des données.

   - `ledger_start_transaction_id` indique l’ID de transaction unique associé à la transaction qui a inséré les données. Étant donné que `John`, `Joe` et `Mary` ont été insérés lors de la même transaction, ils partagent le même ID de transaction.
   - `ledger_start_sequence_number` indique l’ordre dans lequel les valeurs ont été insérées par la transaction.

   :::image type="content" source="media/ledger/sql-updatable-how-to-1.png" alt-text="exemple de table de registre 1":::

1. Mettez à jour le solde de `Nick` de `50` à `100`.

   ```sql
   UPDATE [Account].[Balance] SET [Balance] = 100
   WHERE [CustomerID] = 1
   ```

1. Copiez le nom unique de votre table d’historique. Vous en aurez besoin à l’étape suivante.

   ```sql
   SELECT 
   ts.[name] + '.' + t.[name] AS [ledger_table_name]
   , hs.[name] + '.' + h.[name] AS [history_table_name]
   , vs.[name] + '.' + v.[name] AS [ledger_view_name]
   FROM sys.tables AS t
   JOIN sys.tables AS h ON (h.[object_id] = t.[history_table_id])
   JOIN sys.views v ON (v.[object_id] = t.[ledger_view_id])
   JOIN sys.schemas ts ON (ts.[schema_id] = t.[schema_id])
   JOIN sys.schemas hs ON (hs.[schema_id] = h.[schema_id])
   JOIN sys.schemas vs ON (vs.[schema_id] = v.[schema_id])
   ```

   :::image type="content" source="media/ledger/sql-updatable-how-to-2.png" alt-text="exemple de table de registre 2":::

1. Affichez la table de registre pouvant être mise à jour `[Account].[Balance]`, ainsi que la table d’historique et la vue du registre correspondantes.

   > [!IMPORTANT]
   > Remplacez `<history_table_name>` par la valeur que vous avez copiée à l’étape précédente.

   ```sql
   SELECT * 
         ,[ledger_start_transaction_id]
         ,[ledger_end_transaction_id]
         ,[ledger_start_sequence_number]
         ,[ledger_end_sequence_number]
   FROM [Account].[Balance] 
   GO
   
   SELECT * FROM <Your unique history table name>
   GO 
   
   SELECT * FROM Account.Balance_Ledger
   ORDER BY ledger_transaction_id
   GO
   ```

   > [!TIP]
   > Nous vous recommandons d’interroger l’historique des modifications à l’aide de la [vue du registre](ledger-updatable-ledger-tables.md#ledger-view), et non de la [table d’historique](ledger-updatable-ledger-tables.md#history-table).

1. Le solde du compte `Nick` a été mis à jour avec succès dans la table de registre pouvant être mise à jour avec la valeur `100`.
1. La table d’historique affiche maintenant le solde précédent de `50` pour `Nick`.
1. L’affichage du registre indique que la mise à jour de la table de registre est une opération `DELETE` de la ligne d’origine avec `50`, le solde correspondant à une opération `INSERT` pour une nouvelle ligne avec `100` comme nouveau solde pour `Nick`.

   :::image type="content" source="media/ledger/sql-updatable-how-to-3.png" alt-text="exemple de table de registre 3":::


## <a name="next-steps"></a>Étapes suivantes

- [Registre de base de données](ledger-database-ledger.md)  
- [Gestion des synthèses et vérification de base de données](ledger-digest-management-and-database-verification.md)   
- [Tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md)
- [Tables de registre avec ajout uniquement](ledger-append-only-ledger-tables.md) 
- [Créer et utiliser des tables de registre d’ajout uniquement](ledger-how-to-append-only-ledger-tables.md) 
- [Comment accéder aux synthèses stockées dans le Registre confidentiel Azure (ACL)](ledger-how-to-access-acl-digest.md)
- [Comment vérifier une table du registre pour détecter la falsification](ledger-verify-database.md)
