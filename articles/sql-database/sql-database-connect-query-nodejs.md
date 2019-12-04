---
title: 'Démarrage rapide : Utiliser Node.js pour interroger des données d’une base de données Azure SQL'
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
ms.openlocfilehash: 064baf0215a2eaf7b90b78716b87606990b8fd21
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279255"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Démarrage rapide : Utilisation de Node.js pour interroger une base de données Azure SQL

Ce guide de démarrage rapide explique comment utiliser [Node.js](https://nodejs.org) pour vous connecter à une base de données Azure SQL. Vous pouvez ensuite utiliser les instructions T-SQL pour interroger des données.

## <a name="prerequisites"></a>Prérequis

Pour suivre cet exemple, vérifiez que les conditions préalables ci-dessous sont bien remplies :

- base de données Azure SQL. Utilisez l’un de ces guides de démarrage rapide pour créer et configurer une base de données dans Azure SQL Database :

  || Base de données unique | Instance gérée |
  |:--- |:--- |:---|
  | Créer| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [INTERFACE DE LIGNE DE COMMANDE](scripts/sql-database-create-and-configure-database-cli.md) | [INTERFACE DE LIGNE DE COMMANDE](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configuration | [Règle de pare-feu IP au niveau du serveur](sql-database-server-level-firewall-rule.md)| [Connectivité à partir d’une machine virtuelle](sql-database-managed-instance-configure-vm.md)|
  |||[Connectivité à partir d’une machine locale](sql-database-managed-instance-configure-p2s.md)
  |Charger des données|Adventure Works chargé dans le cadre du guide de démarrage rapide|[Restaurer Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Restaurer ou importer Adventure Works à partir du fichier [BACPAC](sql-database-import.md) disponible sur [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Les scripts fournis dans cet article utilisent la base de données Adventure Works. Avec une instance managée, vous devez importer la base de données Adventure Works dans une base de données d’instance, ou modifier les scripts fournis dans cet article pour utiliser la base de données Wide World Importers.


- Le logiciel associé à Node.js pour votre système d’exploitation :

  - **MacOS**, installez Homebrew et Node.js, puis installez le pilote ODBC et SQLCMD. Consultez les [étapes 1.2 et 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
  
  - **Ubuntu**, installez Node.js, puis installez le pilote ODBC et SQLCMD. Consultez les [étapes 1.2 et 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
  
  - **Windows**, installez Chocolatey et Node.js, puis installez le pilote ODBC et SQLCMD. Consultez les [étapes 1.2 et 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="get-sql-server-connection-information"></a>Obtenir des informations de connexion SQL Server

Procurez-vous les informations de connexion dont vous avez besoin pour vous connecter à la base de données Azure SQL. Vous aurez besoin du nom complet du serveur ou de l’hôte, du nom de la base de données et des informations de connexion pour les procédures suivantes.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

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
