---
title: Utilisation de Python pour interroger Azure SQL Database | Microsoft Docs
description: Cette rubrique vous explique comment utiliser Python pour créer un programme qui se connecte à une base de données SQL Azure et l’interroger à l’aide d’instructions Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: b9c33da4f002504a55802e4253d648ff87847d92
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271825"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Démarrage rapide : Utilisation de Python pour interroger une base de données SQL Azure

 Ce didacticiel de démarrage rapide montre comment utiliser [Python](https://python.org) pour se connecter à une base de données SQL Azure et utiliser des instructions Transact-SQL pour interroger des données. Pour plus d’informations concernant le kit de développement logiciel (SDK), consultez notre documentation de [référence](https://docs.microsoft.com/python/api/overview/azure/sql), le [référentiel GitHub pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) et un [exemple pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce démarrage rapide, vérifiez que vous avez :

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- Une [règle de pare-feu au niveau du serveur](sql-database-get-started-portal-firewall.md) pour l’adresse IP publique de l’ordinateur que vous utilisez pour ce démarrage rapide.
  
- Python et les logiciels connexes pour votre système d’exploitation :
  
  - **macOS** : Installez Homebrew et Python, installez le pilote ODBC et SQLCMD, puis installez le pilote Python pour SQL Server. Consultez les étapes 1.2, 1.3 et 2.1 dans [Créer des applications Python à l’aide de SQL Server sur macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Pour plus d’informations, consultez [Installer le pilote ODBC Microsoft sur Linux et macOS](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).
    
  - **Ubuntu** : Installez Python et les autres packages requis à l’aide de `sudo apt-get install python python-pip gcc g++ build-essential`. Téléchargez et installez le pilote ODBC, SQLCMD ainsi que le pilote Python pour SQL Server. Pour obtenir des instructions, consultez [Configurer un environnement de développement pour le développement Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).
    
  - **Windows** : Installez Python, le pilote ODBC, SQLCMD ainsi que le pilote Python pour SQL Server. Pour obtenir des instructions, consultez [Configurer un environnement de développement pour le développement Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

## <a name="get-sql-server-connection-information"></a>Obtenir des informations de connexion SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="create-code-to-query-your-sql-database"></a>Créer du code pour interroger votre base de données SQL 

1. Créez un nouveau fichier nommé *sqltest.py* dans un éditeur de texte.  
   
1. Ajoutez le code suivant. Utilisez vos propres valeurs pour \<serveur >, \<base de données >, \<nom d’utilisateur >, et \<mot de passe >.
   
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

- [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md)
- [Pilote Microsoft Python pour SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Centre de développement Python](https://azure.microsoft.com/develop/python/?v=17.23h)

