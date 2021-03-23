---
title: Utiliser Python pour interroger une base de données
description: Cette rubrique montre comment utiliser Python pour créer un programme qui se connecte à une base de données Azure SQL Database et l’interroger à l’aide d’instructions Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/19/2020
ms.openlocfilehash: f21e11e33d3ddf1489dba3419766a8adaa878d5f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491960"
---
# <a name="quickstart-use-python-to-query-a-database"></a>Démarrage rapide : Utiliser Python pour interroger une base de données

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Dans ce guide de démarrage rapide, vous utilisez Python pour vous connecter à Azure SQL Database, Azure SQL Managed Instance ou une base de données Synapse SQL, puis utilisez des instructions T-SQL pour interroger les données.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Base de données dans laquelle vous allez exécuter une requête.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [Python](https://python.org/downloads) 3 et les logiciels associés
    

    |**Action**|**macOS**|**Ubuntu**|**Windows**|
    |----------|-----------|------------|---------|
    |Installer le pilote ODBC, SQLCMD ainsi que le pilote Python pour SQL Server|Consultez les étapes **1.2**, **1.3** et **2.1** de [Créer des applications Python à l’aide de SQL Server sur macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Cette opération installe également Homebrew et Python.       |[Configurer un environnement Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)|[Configurer un environnement Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).|
    |Installer Python et les autres packages requis|    |Utiliser `sudo apt-get install python python-pip gcc g++ build-essential`.|    |
    |Informations supplémentaires|[Pilote Microsoft ODBC sur macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)  |[Pilote Microsoft ODBC sur Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|[Pilote Microsoft ODBC sur Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|



Pour approfondir l’exploration de Python et de la base de données dans Azure SQL Database, consultez [Bibliothèques Azure SQL Database pour Python](/python/api/overview/azure/sql), le [dépôt pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) et un [exemple pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="create-code-to-query-your-database"></a>Créer du code pour interroger votre base de données 

1. Créez un nouveau fichier nommé *sqltest.py* dans un éditeur de texte.  
   
1. Ajoutez le code ci-dessous. Récupérez les informations de connexion dans la section des prérequis et remplacez \<server>, \<database>, \<username> et \<password> par vos propres valeurs.
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
           cursor.execute("SELECT TOP 3 name, collation_name FROM sys.databases")
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

1. Vérifiez que les bases de données et leurs classements sont retournés, puis fermez la fenêtre Commande.

## <a name="next-steps"></a>Étapes suivantes

- [Concevoir votre première base de données dans Azure SQL Database](design-first-database-tutorial.md)
- [Pilote Microsoft Python pour SQL Server](/sql/connect/python/python-driver-for-sql-server/)
- [Centre de développement Python](https://azure.microsoft.com/develop/python/?v=17.23h)
