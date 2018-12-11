---
title: Utiliser PHP pour interroger Azure SQL Database | Microsoft Docs
description: Explique comment utiliser PHP pour créer un programme qui se connecte à une base de données SQL Azure et pour l’interroger à l’aide d’instructions T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/28/2018
ms.openlocfilehash: be3ac9fab6c89c65ad9673811e108cefe2c80d00
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724245"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Démarrage rapide : Utiliser PHP pour interroger une base de données Azure SQL

Cet article explique comment utiliser [PHP](http://php.net/manual/en/intro-whatis.php) pour se connecter à une base de données SQL Azure. Vous pouvez ensuite utiliser les instructions T-SQL pour interroger des données.

## <a name="prerequisites"></a>Prérequis

Pour suivre cet exemple, vérifiez que les conditions préalables ci-dessous sont bien remplies :

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Une [règle de pare-feu au niveau du serveur](sql-database-get-started-portal-firewall.md) pour l’adresse IP publique de l’ordinateur que vous utilisez

- Le logiciel associé à PHP installé pour votre système d’exploitation :

    - **MacOS** : Installez Homebrew et PHP, le pilote ODBC et SQLCMD, puis le pilote PHP pour SQL Server. Consultez les [étapes 1.2, 1.3 et 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/mac/).

    - **Ubuntu** : Installez PHP et les autres packages requis, puis installez le pilote PHP pour SQL Server. Consultez les [étapes 1.2 et 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/).

    - **Windows** : Installez PHP pour IIS Express et Chocolatey, puis installez le pilote ODBC et SQLCMD. Consultez les [étapes 1.2 et 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-database-connection"></a>Obtenir la connexion de base de données

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="add-code-to-query-database"></a>Ajouter du code pour interroger la base de données

1. Dans votre éditeur de texte favori, créez un nouveau fichier nommé *sqltest.php*.  

1. Remplacez son contenu par le code ci-dessous. Ensuite, ajoutez les valeurs appropriées pour vos serveur, base de données, utilisateur et mot de passe.

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>Exécuter le code

1. À l’invite de commande, exécutez l’application.

   ```bash
   php sqltest.php
   ```

1. Vérifiez que les 20 premières lignes ont été retournées et fermez la fenêtre d’application.

## <a name="next-steps"></a>Étapes suivantes

- [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md)

- [Microsoft PHP Drivers for SQL Server](https://github.com/Microsoft/msphpsql/) (Pilotes PHP Microsoft pour SQL Server)

- [Signaler des problèmes ou poser des questions](https://github.com/Microsoft/msphpsql/issues)

- [Exemple de logique de nouvelle tentative : Connexion résiliente à SQL avec PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
