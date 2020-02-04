---
title: Utiliser Python pour interroger une base de données
description: Cette rubrique vous explique comment utiliser Python pour créer un programme qui se connecte à une base de données Azure SQL et l’interroger à l’aide d’instructions Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: e82f8feae0096202e48a58296dd2e9d21bb61885
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768573"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Démarrage rapide : Utilisation de Python pour interroger une base de données Azure SQL

Dans ce guide de démarrage rapide, vous utilisez Python pour vous connecter à une base de données Azure SQL et vous utilisez des instructions T-SQL pour interroger les données.

## <a name="prerequisites"></a>Conditions préalables requises

- Un compte Azure avec un abonnement actif. [Créez gratuitement un compte](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Une [base de données Azure SQL](sql-database-single-database-get-started.md)
- [Python](https://python.org/downloads) 3 et les logiciels associés

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  Pour installer Homebrew et Python, le pilote ODBC et SQLCMD, ainsi que le pilote Python pour SQL Server, utilisez les étapes **1.2**, **1.3** et **2.1** dans [Créer des applications Python à l’aide de SQL Server sur macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Pour plus d’informations, consultez [Pilote Microsoft ODBC sur macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  Pour installer Python et d’autres packages nécessaires, utilisez `sudo apt-get install python python-pip gcc g++ build-essential`.

  Pour installer le pilote ODBC, SQLCMD et le pilote Python pour SQL Server, consultez [Configurer un environnement pour le développement Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  Pour plus d’informations, consultez [Pilote Microsoft ODBC sur Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  Pour installer Python, le pilote ODBC et SQLCMD, ainsi que le pilote Python pour SQL Server, consultez [Configurer un environnement pour le développement Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

  Pour plus d’informations, consultez [Pilote Microsoft ODBC](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---

> [!IMPORTANT]
> Les scripts fournis dans cet article utilisent la base de données **Adventure Works**.

> [!NOTE]
> Vous pouvez éventuellement choisir d’utiliser une instance gérée d’Azure SQL.
>
> Pour créer et configurer, utilisez le [portail Azure](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) ou [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), puis configurez [sur site](sql-database-managed-instance-configure-p2s.md) ou par le biais d’une connexion aux [machines virtuelles](sql-database-managed-instance-configure-vm.md).
>
> Pour charger des données, consultez [Restaurer avec BACPAC](sql-database-import.md) avec le fichier [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works), ou consultez [Restaurer la base de données Wide World Importers](sql-database-managed-instance-get-started-restore.md).

Pour approfondir l’exploration de Python et de la base de données Azure SQL, consultez [Bibliothèques de base de données Azure SQL pour Python](/python/api/overview/azure/sql), le [référentiel pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) et un [exemple pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-sql-server-connection-information"></a>Obtenir des informations de connexion SQL Server

Procurez-vous les informations de connexion dont vous avez besoin pour vous connecter à la base de données Azure SQL. Vous aurez besoin du nom complet du serveur ou de l’hôte, du nom de la base de données et des informations de connexion pour les procédures suivantes.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Accédez à la page **Bases de données SQL** ou **Instances managées SQL**.

3. Dans la page **Vue d’ensemble**, vérifiez le nom complet du serveur à côté de **Nom du serveur** pour une base de données, ou le nom complet du serveur à côté de **Hôte** pour une instance managée. Pour copier le nom du serveur ou de l’hôte, pointez dessus et sélectionnez l’icône **Copier**.

## <a name="create-code-to-query-your-sql-database"></a>Créer du code pour interroger votre base de données SQL 

1. Créez un nouveau fichier nommé *sqltest.py* dans un éditeur de texte.  
   
1. Ajoutez le code ci-dessous. Utilisez vos propres valeurs pour \<serveur >, \<base de données >, \<nom d’utilisateur >, et \<mot de passe >.
   
   >[!IMPORTANT]
   >Dans cet exemple, le code utilise les exemples de données AdventureWorksLT que vous pouvez choisir comme source lors de la création de votre base de données. Si votre base de données comporte des données différentes, utilisez des tables de votre propre base de données dans la requête SELECT. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
   row = cursor.fetchone()
   while row:
       print (str(row[0]) + " " + str(row[1]))
       row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Exécuter le code

1. Depuis une invite de commandes, exécutez la commande suivante :

   ```cmd
   python sqltest.py
   ```

1. Vérifiez que les 20 premières lignes catégorie/produit sont retournées, puis fermez la fenêtre de commande.

## <a name="next-steps"></a>Étapes suivantes

- [Concevoir votre première base de données Azure SQL](sql-database-design-first-database.md)
- [Pilote Microsoft Python pour SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Centre de développement Python](https://azure.microsoft.com/develop/python/?v=17.23h)

