---
title: Utiliser Ruby pour les requêtes
description: Cette rubrique vous explique comment utiliser Ruby pour créer un programme qui se connecte à une base de données Azure SQL et l’interroger à l’aide d’instructions Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 4bef55b049ee542efdb9d72d13fa196c989c75ec
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826928"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Démarrage rapide : Utilisation de Ruby pour interroger une base de données Azure SQL

Ce guide de démarrage rapide explique comment utiliser [Ruby](https://www.ruby-lang.org) pour se connecter à une base de données Azure SQL et interroger les données à l’aide d’instructions Transact-SQL.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, vous avez besoin de ce qui suit :

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
  
- Ruby et les logiciels connexes pour votre système d’exploitation :
  
  - **MacOS** : Installez Homebrew, rbenv et ruby-build, Ruby, FreeTDS et TinyTDS. Consultez les étapes 1.2, 1.3, 1.4, 1.5 et 2.1 dans [Créer des applications Ruby à l’aide de SQL Server sur macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu** : Installez les prérequis pour Ruby, rbenv et ruby-build, Ruby, FreeTDS et TinyTDS. Consultez les étapes 1.2, 1.3, 1.4, 1.5 et 2.1 dans [Créer des applications Ruby à l’aide de SQL Server sur Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows** : Installez Ruby, Ruby Devkit et TinyTDS. Voir [Configurer l’environnement de développement pour le développement Ruby](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Obtenir des informations de connexion SQL Server

Procurez-vous les informations de connexion dont vous avez besoin pour vous connecter à la base de données Azure SQL. Vous aurez besoin du nom complet du serveur ou de l’hôte, du nom de la base de données et des informations de connexion pour les procédures suivantes.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Accédez à la page **Bases de données SQL** ou **Instances managées SQL**.

3. Dans la page **Vue d’ensemble**, vérifiez le nom complet du serveur à côté de **Nom du serveur** pour une base de données, ou le nom complet du serveur à côté de **Hôte** pour une instance managée. Pour copier le nom du serveur ou de l’hôte, pointez dessus et sélectionnez l’icône **Copier**. 

## <a name="create-code-to-query-your-sql-database"></a>Créer du code pour interroger votre base de données SQL

1. Créez un nouveau fichier nommé *sqltest.py* dans un éditeur de texte ou de code.
   
1. Ajoutez le code ci-dessous. Remplacez les valeurs `<server>`, `<database>`, `<username>` et `<password>` de votre base de données Azure SQL.
   
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
- [Concevoir votre première base de données Azure SQL](sql-database-design-first-database.md).
- [Référentiel GitHub pour TinyTDS](https://github.com/rails-sqlserver/tiny_tds).
- [Signaler des problèmes ou poser des questions sur TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Pilote Ruby pour SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
