---
title: Sécuriser une base de données
description: Conseils relatifs à la sécurisation d'une base de données et au développement de solutions dans le pool SQL de SQL Analytics.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 26cdbb1fc2899d1b03fea6199074467623706c63
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153279"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Sécuriser une base de données dans SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Présentation de la sécurité](sql-data-warehouse-overview-manage-security.md)
> * [Authentification](sql-data-warehouse-authentication.md)
> * [Chiffrement (portail)](sql-data-warehouse-encryption-tde.md)
> * [Chiffrement (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Cet article présente les bases de la sécurisation de votre pool SQL dans SQL Analytics. Plus spécifiquement, cet article vous présente les ressources permettant de limiter l'accès, de protéger les données et de surveiller les activités sur une base de données approvisionnée par un pool SQL.

## <a name="connection-security"></a>Sécurité de la connexion
L’expression « sécurité de la connexion » fait référence au mode de restriction et de sécurisation appliqué aux connexions à votre base de données, au moyen de règles de pare-feu et d’une fonction de chiffrement des connexions.

Tant le serveur que la base de données utilisent des règles de pare-feu pour refuser toute tentative de connexion à partir d’adresses IP non mises explicitement en liste verte. Pour autoriser les connexions à partir de l’adresse IP publique de l’ordinateur client ou de votre application, vous devez d’abord créer une règle de pare-feu au niveau du serveur à l’aide du portail Azure, de l’API REST ou de PowerShell. 

Nous vous recommandons, à titre de meilleure pratique, de limiter autant que possible le nombre de plages d’adresses IP autorisées à traverser le pare-feu de votre serveur.  Pour accéder au pool SQL à partir de votre ordinateur local, vérifiez que le pare-feu de votre réseau et l'ordinateur local autorisent les communications sortantes sur le port TCP 1433.  

Azure Synapse Analytics utilise des règles de pare-feu IP au niveau serveur. Il ne prend pas en charge les règles de pare-feu IP au niveau de la base de données. Pour plus d’information, voir [Règles de pare-feu Azure SQL Database](../sql-database/sql-database-firewall-configure.md).

Par défaut, les connexions à votre pool SQL sont chiffrées.  La modification des paramètres de connexion pour désactiver le chiffrement est ignorée.

## <a name="authentication"></a>Authentication
Le terme « authentification » fait référence au processus de validation de votre identité lorsque vous vous connectez à la base de données. Le pool SQL prend actuellement en charge l'authentification SQL Server avec un nom d'utilisateur et un mot de passe, et avec Azure Active Directory. 

Lorsque vous avez créé un serveur logique pour votre base de données, vous avez spécifié un compte de connexion « Admin serveur », associé à un nom d’utilisateur et à un mot de passe. À l’aide de ces informations d’identification, vous pouvez vous authentifier auprès de n’importe quelle base de données sur ce serveur, en tant que propriétaire de la base de données, ou « dbo » via l’authentification SQL Server.

Toutefois, au titre de meilleure pratique, il est recommandé que les utilisateurs de votre organisation utilisent un compte différent pour s’authentifier. Cela vous permet de limiter les autorisations accordées à cette application et de réduire les risques d’activité malveillante, au cas où le code de votre application serait vulnérable à une attaque par injection de code SQL. 

Pour créer un utilisateur authentifié par SQL Server, connectez-vous à la base de données **master** sur votre serveur avec votre identifiant de connexion d’administrateur du serveur, et créez un nouvel identifiant de connexion au serveur.  Il est judicieux de créer également un utilisateur dans la base de données MASTER des utilisateurs d’Azure Synapse. La création d’un utilisateur dans la base de données master permet à un utilisateur de se connecter à l’aide d’outils tels que SSMS sans spécifier de nom de base de données.  Elle permet également d’utiliser l’Explorateur d’objets pour afficher toutes les bases de données sur un serveur SQL Server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Ensuite, connectez-vous à la **base de données de votre pool SQL** avec votre identifiant de connexion d'administrateur du serveur, puis créez un utilisateur de base de données basé sur l'identifiant de connexion au serveur que vous avez créé.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Pour autoriser un utilisateur à effectuer d’autres opérations telles que la création de connexions ou de bases de données, affectez-le aux rôles `Loginmanager` et `dbmanager` dans la base de données master. 

Pour en savoir plus sur ces rôles supplémentaires et sur l’authentification auprès d’une base de données SQL, consultez [Gestion des bases de données et connexions dans Azure SQL Database](../sql-database/sql-database-manage-logins.md).  Pour plus d’informations sur la connexion à l’aide d’Azure Active Directory, voir [Connexion à l’aide de l’authentification Azure Active Directory](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Autorisation
L’autorisation fait référence à ce que vous pouvez faire à l’intérieur d’une base de données une fois que vous êtes authentifié et connecté. Les privilèges d’autorisation sont déterminés par les autorisations et les appartenances aux rôles. Nous vous recommandons, à titre de meilleure pratique, d’accorder aux utilisateurs des privilèges aussi réduits que possible. Pour gérer les rôles, vous pouvez utiliser les procédures stockées suivantes :

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Le compte d’administrateur du serveur avec lequel vous vous connectez appartient au groupe « db_owner », qui est autorisé à effectuer tout type d’opérations dans la base de données. Enregistrez ce compte pour l’utiliser lors du déploiement des mises à niveau de schéma et d’autres opérations de gestion. Utilisez le compte « ApplicationUser », doté d’autorisations plus limitées, pour vous connecter à la base de données à partir de votre application, en bénéficiant du niveau de privilèges le moins élevé requis par cette dernière.

D’autres méthodes permettent de limiter ce qu’un utilisateur peut faire à l’intérieur d’un entrepôt de données :

* Des [autorisations](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine?view=sql-server-ver15) granulaires vous permettent de contrôler les opérations que vous pouvez exécuter sur des colonnes, des tables, des vues, des schémas, des procédures et d’autres objets individuels dans la base de données. Utilisez les autorisations granulaires pour avoir un contrôle optimal et accordez les autorisations minimales nécessaires. 
* Les [rôles de base de données](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-ver15) autres que « db_datareader » et « db_datawriter » peuvent être utilisés pour créer des comptes d’utilisateur plus puissants ou des comptes de gestion moins puissants pour votre application. Les rôles de base de données fixes intégrés offrent un moyen facile d'accorder des autorisations, mais peuvent entraîner l'octroi d'autorisations excessives.
* [procédures stockées](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine?redirectedfrom=MSDN&view=sql-server-ver15) , vous pouvez limiter le nombre d’actions susceptibles d’être exécutées sur la base de données.

L’exemple suivant accorde un accès en lecture à un schéma défini par l’utilisateur.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

La gestion des bases de données et serveurs logiques à partir du portail Azure et l’utilisation de l’API Azure Resource Manager sont contrôlées par les attributions de rôle de votre compte d’utilisateur sur le portail. Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle dans le Portail Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure).

## <a name="encryption"></a>Chiffrement
La technologie Transparent Data Encryption (TDE) vous aide à vous protéger contre des activités malveillantes en chiffrant et déchiffrant vos données au repos. Lorsque vous chiffrez votre base de données, les fichiers de sauvegardes et les journaux de transactions associés sont chiffrés, sans que cela ne nécessite de modifications de vos applications. Le chiffrement transparent des données chiffre le stockage d’une base de données entière à l’aide d’une clé symétrique appelée clé de chiffrement de base de données. 

Dans SQL Database, la clé de chiffrement de base de données est protégée par un certificat de serveur intégré. Le certificat de serveur intégré est unique pour chaque serveur SQL Database. Microsoft fait automatiquement alterner ces certificats au moins tous les 90 jours. L’algorithme de chiffrement utilisé est AES-256. Pour obtenir une description générale du chiffrement transparent des données, consultez la page [Chiffrement transparent des données (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15).

Vous pouvez chiffrer votre base de données à l’aide du [Portail Azure](sql-data-warehouse-encryption-tde.md) ou de [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des détails et des exemples sur la connexion à votre entrepôt de données avec différents protocoles, consultez [Se connecter au pool SQL](sql-data-warehouse-connect-overview.md).
