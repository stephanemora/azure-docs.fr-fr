---
title: Octroi de l’accès
description: Découvrez comment octroyer un accès à Microsoft Azure SQL Database et Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: sql-data-warehouse, seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 02/06/2020
ms.openlocfilehash: 5142cc941b37cfef7be79e5129b6df7094bfd00e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197842"
---
# <a name="azure-sql-database-and-azure-synapse-access-control"></a>Contrôle de l’accès à Azure SQL Database et Azure Synapse

Pour assurer la sécurité, Azure [SQL Database](sql-database-technical-overview.md) et [Azure Synapse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) contrôlent l’accès avec des règles de pare-feu qui limitent la connectivité en fonction de l’adresse IP, des mécanismes d’authentification qui obligent les utilisateurs à prouver leur identité, et des mécanismes d’autorisation qui les restreignent à certaines actions et données. 

> [!IMPORTANT]
> Pour une vue d’ensemble des fonctionnalités de sécurité de SQL Database, consultez [Sécurisation de SQL Database](sql-database-security-overview.md). Pour un tutoriel, consultez [Sécuriser votre base de données Azure SQL](sql-database-security-tutorial.md). Pour obtenir une vue d’ensemble des fonctionnalités de sécurité SQL Analytics dans Azure Synapse, consultez [Vue d’ensemble de la sécurité Azure Synapse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

## <a name="firewall-and-firewall-rules"></a>Pare-feu et règles de pare-feu

Microsoft Azure SQL Database fournit un service de base de données relationnelle pour Azure et d’autres applications basées sur Internet. Pour aider à protéger vos données, le pare-feu empêche tout accès à votre serveur de base de données jusqu’à ce que vous spécifiiez les ordinateurs qui disposent d’autorisations. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande. Pour en savoir plus, consultez [Vue d’ensemble des règles de pare-feu Azure SQL Database](sql-database-firewall-configure.md).

Le service Azure SQL Database est disponible uniquement via le port TCP 1433. Pour accéder à une base de données SQL depuis votre ordinateur, vérifiez que le pare-feu de votre ordinateur client autorise les communications TCP sortantes sur le port 1433. Si elles ne sont pas nécessaire pour les autres applications, bloquez les connexions entrantes sur le port TCP 1433. 

Dans le cadre du processus de connexion, les connexions à partir des machines virtuelles Azure sont redirigées vers une autre adresse IP et un autre port, propres à chaque rôle de travail. Le numéro du port est compris entre 11000 et 11999. Pour plus d’informations sur les ports TCP, consultez [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database2](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="authentication"></a>Authentication

Une base de données SQL prend en charge deux types d’authentification :

- **Authentification SQL** :

  Cette méthode d’authentification utilise un nom d’utilisateur et un mot de passe. Lorsque vous avez créé un serveur SQL Database pour votre base de données, vous avez spécifié un compte de connexion « Admin serveur », associé à un nom d’utilisateur et à un mot de passe. À l’aide de ces informations d’identification, vous pouvez vous authentifier auprès de n’importe quelle base de données sur ce serveur, en tant que propriétaire de la base de données, ou « dbo ». 
- **Authentification Azure Active Directory** :

  Cette méthode d’authentification utilise des identités gérées par Azure Active Directory, et est prise en charge pour les domaines managés et intégrés. Si vous souhaitez utiliser l’authentification Azure Active Directory, vous devez créer un autre administrateur de serveur appelé « administrateur Azure AD », autorisé à gérer les groupes et utilisateurs Active Directory Azure. Cet administrateur peut également effectuer toutes les opérations d’un administrateur de serveur ordinaire. Pour une procédure pas à pas relative à la création d’un administrateur Azure AD pour activer l’authentification Azure Active Directory, consultez [Connexion à la base de données SQL avec l’authentification Azure Active Directory](sql-database-aad-authentication.md) .

Le moteur de base de données ferme les connexions restées inactives pendant plus de 30 minutes. La connexion nécessite une nouvelle identification pour fonctionner à nouveau. Les connexions perpétuelles à SQL Database requièrent une nouvelle autorisation (effectuée par le moteur de base de données) au moins toutes les 10 heures. Le moteur de base de données tente de renouveler l’autorisation à l’aide du mot de passe envoyé à l’origine. L’utilisateur n’a rien à saisir. Pour des raisons de performances, lorsqu’un mot de passe est réinitialisé dans SQL Database, la connexion n’est pas authentifiée à nouveau, même si elle est réinitialisée suite à un regroupement de connexions. Cela est différent du comportement local de SQL Server. Si le mot de passe a été modifié depuis que la connexion a été initialement autorisée, la connexion doit être interrompue et une nouvelle connexion établie à l’aide du nouveau mot de passe. Un utilisateur disposant de l’autorisation `KILL DATABASE CONNECTION` peut mettre explicitement fin à une connexion à SQL Database à l’aide de la commande [KILL](https://docs.microsoft.com/sql/t-sql/language-elements/kill-transact-sql).

Les comptes d’utilisateur peuvent être soit créés dans la base de données principale et autorisés à accéder à toutes les bases de données sur le serveur, soit créés dans la base de données elle-même (appelés utilisateurs à relation contenant-contenu). Pour plus d’informations sur la création et la gestion des connexions, consultez [Gérer les connexions](sql-database-manage-logins.md). Utilisez des bases de données autonomes pour améliorer la portabilité et l’évolutivité. Pour plus d’informations sur les utilisateurs autonomes, consultez [Utilisateurs de base de données autonome - Rendre votre base de données portable](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), [CREATE USER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) et [Bases de données autonomes](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases).

Nous vous recommandons, au titre de meilleure pratique, d’utiliser un compte dédié pour l’authentification de votre application. Cela vous permet de limiter les autorisations accordées à cette application et de réduire les risques d’activité malveillante, au cas où le code de votre application serait vulnérable à une attaque par injection de code SQL. La méthode recommandée consiste à créer un [utilisateur de base de données autonome](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), qui permet à votre application de s’authentifier directement auprès de la base de données. 

## <a name="authorization"></a>Autorisation

Le terme « autorisation » fait référence aux actions qu’un utilisateur peut exécuter dans une base de données Azure SQL Database et qui sont contrôlées par les [rôles détenus](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) et les [autorisations sur les objets](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine). Nous vous recommandons, à titre de meilleure pratique, d’accorder aux utilisateurs des privilèges aussi réduits que possible. Le compte d’administrateur du serveur avec lequel vous vous connectez appartient au groupe « db_owner », qui est autorisé à effectuer tout type d’opérations dans la base de données. Enregistrez ce compte pour l’utiliser lors du déploiement des mises à niveau de schéma et d’autres opérations de gestion. Utilisez le compte « ApplicationUser », doté d’autorisations plus limitées, pour vous connecter à la base de données à partir de votre application, en bénéficiant du niveau de privilèges le moins élevé requis par cette dernière. Pour plus d’informations, consultez [Gérer les connexions](sql-database-manage-logins.md).

En règle générale, seuls les administrateurs ont besoin d’accéder à la base de données `master`. L’accès normal à chaque base de données utilisateur doit se faire par les utilisateurs non administrateurs de base de données autonome créés dans chaque base de données. Lorsque vous utilisez des utilisateurs de base de données autonome, vous n’avez pas besoin de créer des connexions à la base de données `master`. Pour plus d’informations, voir [Utilisateurs de base de données autonome - Rendre votre base de données portable](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable).

Vous devez vous familiariser avec les fonctionnalités suivantes qui peuvent être utilisées pour limiter ou élever les autorisations :

- Vous pouvez recourir à l’[emprunt d’identité](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) et à la [signature de module](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) pour élever le niveau des autorisations temporairement, en toute sécurité.
- [sécurité au niveau des lignes](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) peut être utilisée pour limiter le nombres de lignes accessibles par l’utilisateur.
- [masquage des données](sql-database-dynamic-data-masking-get-started.md) permet de restreindre l’exposition des données sensibles.
- [procédures stockées](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) , vous pouvez limiter le nombre d’actions susceptibles d’être exécutées sur la base de données.

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble des fonctionnalités de sécurité de SQL Database, consultez [Sécurisation de SQL Database](sql-database-security-overview.md).
- Pour en savoir plus sur les règles de pare-feu, consultez [Règles de pare-feu](sql-database-firewall-configure.md).
- Pour en savoir plus sur les utilisateurs et les connexions, consultez [Gérer les connexions](sql-database-manage-logins.md). 
- Pour une discussion sur la surveillance proactive, consultez [Audit de base de données](sql-database-auditing.md) et [Détection des menaces pour SQL Database](sql-database-threat-detection.md).
- Pour un tutoriel, consultez [Sécuriser votre base de données Azure SQL](sql-database-security-tutorial.md).
