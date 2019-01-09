---
title: Utilisation de Ruby pour interroger Azure SQL Database | Microsoft Docs
description: Cette rubrique vous explique comment utiliser Ruby pour créer un programme qui se connecte à une base de données SQL Azure et l’interroger à l’aide d’instructions Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: 66819cbd65f6f044d0dac68326eb5890476964b6
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993907"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Démarrage rapide : Utilisation de Ruby pour interroger une base de données SQL Azure

Ce démarrage rapide explique comment utiliser [Ruby](https://www.ruby-lang.org) pour se connecter à une base de données SQL Azure et interroger les données à l’aide d’instructions Transact-SQL.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, vous avez besoin de ce qui suit :

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- Une [règle de pare-feu au niveau du serveur](sql-database-get-started-portal-firewall.md) pour l’adresse IP publique de l’ordinateur que vous utilisez pour ce démarrage rapide.
  
- Ruby et les logiciels connexes pour votre système d’exploitation :
  
  - **MacOS** : Installez Homebrew, rbenv et ruby-build, Ruby, FreeTDS et TinyTDS. Consultez les étapes 1.2, 1.3, 1.4, 1.5 et 2.1 dans [Créer des applications Ruby à l’aide de SQL Server sur macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu** : Installez les prérequis pour Ruby, rbenv et ruby-build, Ruby, FreeTDS et TinyTDS. Consultez les étapes 1.2, 1.3, 1.4, 1.5 et 2.1 dans [Créer des applications Ruby à l’aide de SQL Server sur Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows** : Installez Ruby, Ruby Devkit et TinyTDS. Voir [Configurer l’environnement de développement pour le développement Ruby](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Obtenir des informations de connexion SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-code-to-query-your-sql-database"></a>Créer du code pour interroger votre base de données SQL

1. Créez un nouveau fichier nommé *sqltest.py* dans un éditeur de texte ou de code.
   
1. Ajoutez le code suivant. Remplacez les valeurs `<server>`, `<database>`, `<username>` et `<password>` de votre base de données SQL Azure.
   
   >[!IMPORTANT]
   >Dans cet exemple, le code utilise les exemples de données AdventureWorksLT que vous pouvez choisir comme source lors de la création de votre base de données. Si votre base de données comporte des données différentes, utilisez des tables de votre propre base de données dans la requête SELECT. 
   
   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   client = TinyTds::Client.new username: username, password: password, 
       host: server, port: 1433, database: database, azure: true
   
   puts "Reading data from table"
   tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
           ON pc.productcategoryid = p.productcategoryid"
   result = client.execute(tsql)
   result.each do |row|
       puts row
   end
   ```

## <a name="run-the-code"></a>Exécuter le code

1. Depuis une invite de commandes, exécutez la commande suivante :

   ```bash
   ruby sqltest.rb
   ```
   
1. Vérifiez que les 20 premières lignes catégorie/produit de votre base de données sont retournées. 

## <a name="next-steps"></a>Étapes suivantes
- [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md).
- [Référentiel GitHub pour TinyTDS](https://github.com/rails-sqlserver/tiny_tds).
- [Signaler des problèmes ou poser des questions sur TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Pilote Ruby pour SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
