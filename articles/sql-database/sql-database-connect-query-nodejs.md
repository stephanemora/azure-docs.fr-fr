---
title: Utiliser Node.js pour interroger une base de données
description: Utilisation de Node.js pour créer un programme qui se connecte à une base de données Azure SQL et pour l’interroger à l’aide d’instructions T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: c0da38a41bf613237ea3b164d70e4729a7284ca7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768602"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Démarrage rapide : Utilisation de Node.js pour interroger une base de données Azure SQL

Dans ce guide de démarrage rapide, vous utilisez Node.js pour vous connecter à une base de données Azure SQL et vous utilisez des instructions T-SQL pour interroger les données.

## <a name="prerequisites"></a>Conditions préalables requises

- Compte Azure avec un abonnement actif. [Créez gratuitement un compte](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Une [base de données Azure SQL](sql-database-single-database-get-started.md)
- Logiciels associés à [Node.js](https://nodejs.org)

  # <a name="macos"></a>[macOS](#tab/macos)

  Installez Homebrew et Node.js, puis installez le pilote ODBC et SQLCMD à l’aide des étapes **1.2** et **1.3** dans [Créer des applications Node.js à l’aide de SQL Server sur macOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Installez Node.js, puis le pilote ODBC et SQLCMD à l’aide des étapes **1.2** et **1.3** dans [Créer des applications Node.js à l’aide de SQL Server sur Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Installez Chocolatey et Node.js, puis installez le pilote ODBC et SQLCMD à l’aide des étapes **1.2** et **1.3** dans [Créer des applications Node.js à l’aide de SQL Server sur Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> Les scripts fournis dans cet article utilisent la base de données **Adventure Works**.

> [!NOTE]
> Vous pouvez éventuellement choisir d’utiliser une instance gérée d’Azure SQL.
>
> Pour créer et configurer, utilisez le [portail Azure](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) ou [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), puis configurez [sur site](sql-database-managed-instance-configure-p2s.md) ou par le biais d’une connexion aux [machines virtuelles](sql-database-managed-instance-configure-vm.md).
>
> Pour charger des données, consultez [Restaurer avec BACPAC](sql-database-import.md) avec le fichier [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works), ou consultez [Restaurer la base de données Wide World Importers](sql-database-managed-instance-get-started-restore.md).

## <a name="get-sql-server-connection-information"></a>Obtenir des informations de connexion SQL Server

Procurez-vous les informations de connexion dont vous avez besoin pour vous connecter à la base de données Azure SQL. Vous aurez besoin du nom complet du serveur ou de l’hôte, du nom de la base de données et des informations de connexion pour les procédures suivantes.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Accédez à la page **Bases de données SQL** ou **Instances managées SQL**.

3. Dans la page **Vue d’ensemble**, vérifiez le nom complet du serveur à côté de **Nom du serveur** pour une base de données, ou le nom complet du serveur à côté de **Hôte** pour une instance managée. Pour copier le nom du serveur ou de l’hôte, pointez dessus et sélectionnez l’icône **Copier**. 

## <a name="create-the-project"></a>Créer le projet

Ouvrez une invite de commandes et créez un dossier nommé *sqltest*. Ouvrez le dossier que vous avez créé et exécutez la commande suivante :

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>Ajouter du code pour interroger la base de données

1. Dans votre éditeur de texte favori, créez un nouveau fichier, *sqltest.js*.

1. Remplacez son contenu par le code ci-dessous. Ensuite, ajoutez les valeurs appropriées pour vos serveur, base de données, utilisateur et mot de passe.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
          console.log("%s\t%s", column.metadata.colName, column.value);
        });
      });
      
      connection.execSql(request);
    }
    ```

> [!NOTE]
> Cet exemple de code utilise l’exemple de base de données **AdventureWorksLT** pour SQL Azure.

## <a name="run-the-code"></a>Exécuter le code

1. À l’invite de commande, exécutez ce programme.

    ```bash
    node sqltest.js
    ```

1. Vérifiez que les 20 premières lignes ont été renvoyées et fermez la fenêtre d’application.

## <a name="next-steps"></a>Étapes suivantes

- [Node.js Driver for SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server) (Pilote Node.js pour SQL Server)

- Se connecter et exécuter des requêtes sur Windows/Linux/macOS avec [.NET core](sql-database-connect-query-dotnet-core.md), [Visual Studio Code](sql-database-connect-query-vscode.md) ou [SSMS](sql-database-connect-query-ssms.md) (Windows uniquement)

- [Prise en main de .NET Core sur Windows/Linux/macOS à l’aide de la ligne de commande](/dotnet/core/tutorials/using-with-xplat-cli)

- Concevoir votre première base de données Azure SQL à l’aide de [.NET](sql-database-design-first-database-csharp.md) ou de [SSMS](sql-database-design-first-database.md)
