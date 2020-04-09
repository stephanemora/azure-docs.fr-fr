---
title: Démarrages rapides Se connecter et interroger
description: Ces guides de démarrage rapide Azure SQL Database expliquent comment se connecter à une base de données Azure SQL et l’interroger.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 05ba54c10cc693f81115f364d7a400e2275a3875
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528233"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Guides de démarrage rapide : Se connecter à une base de données Azure SQL et l’interroger

Le document suivant contient des liens vers des exemples Azure expliquant comment se connecter à une base de données Azure SQL et l’interroger. Il contient également des recommandations pour la sécurité de niveau transport.

## <a name="quickstarts"></a>Démarrages rapides

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|Ce guide de démarrage rapide montre comment utiliser SSMS pour se connecter à une base de données Azure SQL, puis utiliser des instructions Transact-SQL pour interroger, insérer, mettre à jour et supprimer des données dans la base de données.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Ce guide de démarrage rapide explique comment utiliser Azure Data Studio pour se connecter à une base de données Azure SQL, puis recourir à des instructions T-SQL (Transact-SQL) pour créer le TutorialDB utilisé dans les tutoriels Azure Data Studio.|
|[Azure portal](sql-database-connect-query-portal.md)|Ce guide de démarrage rapide indique comment utiliser l’éditeur de requête pour se connecter à une base de données SQL, puis utiliser les instructions Transact-SQL pour interroger, insérer, mettre à jour et supprimer des données dans la base de données.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|Ce guide de démarrage rapide explique comment utiliser Visual Studio Code pour se connecter à une base de données Azure SQL, puis utiliser des instructions Transact-SQL pour interroger, insérer, mettre à jour et supprimer des données dans la base de données.|
|[.NET avec Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|Ce guide de démarrage rapide explique comment utiliser le .NET Framework pour créer un programme C# avec Visual Studio en vue de se connecter à une base de données Azure SQL et recourir à des instructions Transact-SQL pour interroger des données.|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|Ce guide de démarrage rapide explique comment utiliser .NET Core sur Windows/Linux/macOS pour créer un programme C# en vue de se connecter à une base de données Azure SQL et recourir à des instructions Transact-SQL pour interroger des données.|
|[Go](sql-database-connect-query-go.md)|Ce guide de démarrage rapide explique comment utiliser Go pour se connecter à une base de données Azure SQL. Les instructions Transact-SQL permettant d’interroger et de modifier des données sont également présentées.|
|[Java](sql-database-connect-query-java.md)|Ce guide de démarrage rapide explique comment utiliser Java pour se connecter à une base de données Azure SQL et se servir d’instructions Transact-SQL pour interroger des données.|
|[Node.JS](sql-database-connect-query-nodejs.md)|Ce guide de démarrage rapide explique comment utiliser Node.js pour créer un programme en vue de se connecter à une base de données Azure SQL et recourir à des instructions Transact-SQL pour interroger des données.|
|[PHP](sql-database-connect-query-php.md)|Ce guide de démarrage rapide explique comment utiliser PHP pour créer un programme en vue de se connecter à une base de données Azure SQL et recourir à des instructions Transact-SQL pour interroger des données.|
|[Python](sql-database-connect-query-python.md)|Ce guide de démarrage rapide explique comment utiliser Python pour se connecter à une base de données Azure SQL et se servir d’instructions Transact-SQL pour interroger des données. |
|[Ruby](sql-database-connect-query-ruby.md)|Ce guide de démarrage rapide explique comment utiliser Ruby pour créer un programme en vue de se connecter à une base de données Azure SQL et recourir à des instructions Transact-SQL pour interroger des données.|
|[R](sql-database-connect-query-r.md)|Ce guide de démarrage rapide explique comment utiliser R avec Machine Learning Services pour créer un programme afin de se connecter à une base de données Azure SQL et utiliser des instructions Transact-SQL pour interroger des données.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Considérations relatives au protocole TLS pour la connectivité de SQL Database
Le protocole TLS (Transport Layer Security) est utilisé par tous les pilotes fournis et pris en charge par Microsoft pour la connexion à Azure SQL Database. Aucune configuration spéciale n’est nécessaire. Pour l’ensemble des connexions à SQL Server ou à Azure SQL Database, nous recommandons que toutes les applications définissent les configurations suivantes ou leurs équivalents :

 - **Encrypt = On**
 - **TrustServerCertificate = Off**

Certains systèmes utilisent des mots clés différents, mais équivalents pour ces configurations. Ces configurations garantissent que le pilote du client vérifie l’identité du certificat TLS provenant du serveur.

Nous vous recommandons également de désactiver les protocoles TLS 1.1 et 1.0 sur le client si vous devez respecter la norme de sécurité des données de l’industrie des cartes de paiement (PCI - DSS).

Les pilotes autres que Microsoft peuvent ne pas utiliser le protocole TLS par défaut. Cela peut être un facteur à prendre en compte lors de la connexion à Azure SQL Database. Il se peut que les applications présentent des pilotes incorporés qui ne permettent pas de contrôler ces paramètres de connexion. Nous vous recommandons d’examiner la sécurité de ces pilotes et applications avant de les utiliser sur les systèmes qui interagissent avec des données sensibles.

## <a name="libraries"></a>Bibliothèques

Vous avez le choix entre plusieurs frameworks et bibliothèques pour vous connecter à Azure SQL Database. Découvrez nos [didacticiels de démarrage](https://aka.ms/sqldev) pour commencer rapidement à utiliser des langages de programmation comme C#, Java, Node.js, PHP et Python. Ensuite, créez une application à l’aide de SQL Server sur Linux, Windows ou Docker sur macOS.

Le tableau suivant répertorie les bibliothèques de connectivité ou *pilotes* que les applications clientes peuvent utiliser dans une variété de langages pour se connecter à SQL Server et l’utiliser localement ou dans le cloud. Vous pouvez les utiliser sur Linux, Windows ou Docker, pour vous connecter à Azure SQL Database et Azure SQL Data Warehouse. 

| Langage | Plateforme | Ressources supplémentaires | Téléchargement | Bien démarrer |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET pour SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Télécharger](https://www.microsoft.com/net/download/) | [Prise en main](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Pilote Microsoft JDBC pour SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Télécharger](https://go.microsoft.com/fwlink/?linkid=852460) |  [Prise en main](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Pilote PHP SQL pour SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Télécharger](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Prise en main](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Pilote Node.js pour SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Installer](https://msdn.microsoft.com/library/mt652094.aspx) |  [Prise en main](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Pilote Python SQL](https://msdn.microsoft.com/library/mt652092.aspx) | Choix d’installation : <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Prise en main](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Pilote Ruby pour SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Installer](https://msdn.microsoft.com/library/mt711041.aspx) | [Prise en main](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Pilote Microsoft ODBC pour SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Télécharger](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

Le tableau suivant répertorie des exemples de frameworks de mappage relationnel objet (ORM) et de frameworks web que les applications clientes peuvent utiliser avec SQL Server exécuté localement ou dans le cloud. Vous pouvez utiliser les frameworks sur Linux, Windows ou Docker, pour vous connecter à SQL Database et SQL Data Warehouse. 

| Langage | Plateforme | ORM |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Mettre en veille prolongée](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://sequelize.org/) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’architecture de connectivité, consultez [Architecture de connectivité Azure SQL Database](sql-database-connectivity-architecture.md).
- Trouvez les [pilotes SQL Server](https://msdn.microsoft.com/library/mt654049.aspx) utilisés pour établir une connexion à partir d’applications clientes.
- Se connecter à SQL Database :
  - [Connexion à SQL Database à l’aide de .NET (C#)](sql-database-connect-query-dotnet.md) 
  - [Connexion à SQL Database à l’aide de PHP sur Windows](sql-database-connect-query-php.md) 
  - [Connexion à SQL Database à l’aide de Node.js](sql-database-connect-query-nodejs.md) 
  - [Connexion à la base de données SQL à l’aide de Java avec JDBC sous Windows](sql-database-connect-query-java.md) 
  - [Connexion à SQL Database à l’aide de Python](sql-database-connect-query-python.md)
  - [Connexion à SQL Database à l’aide de Ruby](sql-database-connect-query-ruby.md)
- Exemples de code de logique de nouvelle tentative :
  - [Connexion résiliente à SQL avec ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Connexion résiliente à SQL avec PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
