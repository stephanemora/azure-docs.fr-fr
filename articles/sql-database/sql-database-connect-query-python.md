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
ms.date: 07/02/2018
ms.openlocfilehash: b5b0c1dd6f241d9b76ff766adc221c3fcb36ee1a
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47062702"
---
# <a name="use-python-to-query-an-azure-sql-database"></a>Utilisation de Python pour interroger une base de données SQL Azure

 Ce didacticiel de démarrage rapide montre comment utiliser [Python](https://python.org) pour se connecter à une base de données SQL Azure et utiliser des instructions Transact-SQL pour interroger des données. Pour plus d’informations de Kit de développement logiciel (SDK), consultez notre documentation de [référence](https://docs.microsoft.com/python/api/overview/azure/sql), un [exemple](https://github.com/mkleehammer/pyodbc/wiki/Getting-started) pyodbc et le référentiel GitHub [pyodbc](https://github.com/mkleehammer/pyodbc/wiki/).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce démarrage rapide, vérifiez que vous avez :

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Une [règle de pare-feu au niveau du serveur](sql-database-get-started-portal-firewall.md) pour l’adresse IP publique de l’ordinateur que vous utilisez pour ce démarrage rapide.

- Installé Python et les logiciels connexes sur votre système d’exploitation :

    - **MacOS** : installez Homebrew et Python, installez le pilote ODBC et SQLCMD, puis installez le pilote Python pour SQL Server. Consultez les [étapes 1.2, 1.3 et 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).
    - **Ubuntu** : installez Python et les autres packages requis, puis installez le pilote Python pour SQL Server. Consultez les [étapes 1.2, 1.3 et 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu/).
    - **Windows** : installez la version la plus récente de Python (la variable d’environnement est désormais configurée pour vous), installez le pilote ODBC et SQLCMD, puis installez le pilote Python pour SQL Server. Consultez les [étapes 1.2, 1.3 et 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/windows/). 

## <a name="sql-server-connection-information"></a>Informations de connexion SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="insert-code-to-query-sql-database"></a>Insertion du code pour interroger la base de données SQL 

1. Dans votre éditeur de texte favori, créez un nouveau fichier nommé **sqltest.py**.  

2. Remplacez le contenu par le code suivant et ajoutez les valeurs appropriées pour votre serveur, base de données, utilisateur et mot de passe.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print (str(row[0]) + " " + str(row[1]))
    row = cursor.fetchone()
```

## <a name="run-the-code"></a>Exécuter le code

1. Exécutez ensuite les commandes suivantes dans l’invite de commandes :

   ```Python
   python sqltest.py
   ```

2. Vérifiez que les 20 premières lignes sont renvoyées, puis fermez la fenêtre d’application.

## <a name="next-steps"></a>Étapes suivantes

- [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md)
- [Python SQL Driver](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/) (Pilote SQL Python)
- [Centre de développement Python](https://azure.microsoft.com/develop/python/?v=17.23h)

