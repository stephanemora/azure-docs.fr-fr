---
title: Utiliser PHP pour interroger
description: Explique comment utiliser PHP pour créer un programme qui se connecte à une base de données Azure SQL et pour l’interroger à l’aide d’instructions T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 02/12/2019
ms.openlocfilehash: ae119dd23da670f16c0239b14119519c431e6326
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "73827011"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Démarrage rapide : Utiliser PHP pour interroger une base de données Azure SQL

Cet article explique comment utiliser [PHP](https://php.net/manual/en/intro-whatis.php) pour se connecter à une base de données Azure SQL. Vous pouvez ensuite utiliser les instructions T-SQL pour interroger des données.

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre cet exemple, vérifiez que les conditions préalables ci-dessous sont bien remplies :

- base de données Azure SQL. Utilisez l’un de ces guides de démarrage rapide pour créer et configurer une base de données dans Azure SQL Database :

  || Base de données unique | Instance gérée |
  |:--- |:--- |:---|
  | Créer| [Portail](sql-database-single-database-get-started.md) | [Portail](sql-database-managed-instance-get-started.md) |
  || [INTERFACE DE LIGNE DE COMMANDE](scripts/sql-database-create-and-configure-database-cli.md) | [INTERFACE DE LIGNE DE COMMANDE](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurer | [Règle de pare-feu IP au niveau du serveur](sql-database-server-level-firewall-rule.md)| [Connectivité à partir d’une machine virtuelle](sql-database-managed-instance-configure-vm.md)|
  |||[Connectivité à partir d’une machine locale](sql-database-managed-instance-configure-p2s.md)
  |Charger les données|Adventure Works chargé dans le cadre du guide de démarrage rapide|[Restaurer Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Restaurer ou importer Adventure Works à partir du fichier [BACPAC](sql-database-import.md) disponible sur [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Les scripts fournis dans cet article utilisent la base de données Adventure Works. Avec une instance managée, vous devez importer la base de données Adventure Works dans une base de données d’instance, ou modifier les scripts fournis dans cet article pour utiliser la base de données Wide World Importers.

- Le logiciel associé à PHP installé pour votre système d’exploitation :

  - **MacOS** : Installez PHP, le pilote ODBC, puis le pilote PHP pour SQL Server. Consultez [Étape 1, 2 et 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux** : Installez PHP, le pilote ODBC, puis le pilote PHP pour SQL Server. Consultez [Étape 1, 2 et 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows** : Installez PHP pour IIS Express et Chocolatey, puis installez le pilote ODBC et SQLCMD. Consultez les [étapes 1.2 et 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-sql-server-connection-information"></a>Obtenir des informations de connexion SQL Server

Procurez-vous les informations de connexion dont vous avez besoin pour vous connecter à la base de données Azure SQL. Vous aurez besoin du nom complet du serveur ou de l’hôte, du nom de la base de données et des informations de connexion pour les procédures suivantes.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Accédez à la page **Bases de données SQL** ou **Instances managées SQL**.

3. Dans la page **Vue d’ensemble**, vérifiez le nom complet du serveur à côté de **Nom du serveur** pour une base de données, ou le nom complet du serveur à côté de **Hôte** pour une instance managée. Pour copier le nom du serveur ou de l’hôte, pointez dessus et sélectionnez l’icône **Copier**.

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

- [Concevoir votre première base de données Azure SQL](sql-database-design-first-database.md)

- [Microsoft PHP Drivers for SQL Server](https://github.com/Microsoft/msphpsql/) (Pilotes PHP Microsoft pour SQL Server)

- [Signaler des problèmes ou poser des questions](https://github.com/Microsoft/msphpsql/issues)

- [Exemple de logique de nouvelle tentative : Connexion résiliente à SQL avec PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
