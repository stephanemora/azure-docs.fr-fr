---
title: Azure Active Directory
description: Découvrez comment utiliser Azure Active Directory pour l’authentification auprès de SQL Database, de l’instance managée et d’Azure Synapse Analytics.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/27/2020
ms.openlocfilehash: 58f40bc7406048df2b052bea799bcbf6466fbbae
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421110"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Utiliser l’authentification Azure Active Directory pour l’authentification avec SQL

L’authentification Azure Active Directory est un mécanisme qui sert à se connecter à Azure [SQL Database](sql-database-technical-overview.md), l’[instance managée](sql-database-managed-instance.md) et [Azure Synapse Analytics (anciennement Azure SQL Data Warehouse)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) à l’aide d’identités se trouvant dans Azure Active Directory (Azure AD). 

> [!NOTE]
> Cet article s’applique à Azure SQL Server ainsi qu’à SQL Database et à Azure Synapse. Par souci de simplicité, le nom « SQL Database » est utilisé pour faire référence à SQL Database et à Azure Synapse.

Avec l’authentification Azure AD, vous pouvez gérer de manière centralisée les identités des utilisateurs de base de données et d’autres services Microsoft dans un emplacement centralisé. La gestion centralisée des ID fournit un emplacement unique pour gérer les utilisateurs de la base de données et simplifie la gestion des autorisations. Les avantages suivants sont inclus :

- Elle fournit une alternative à l’authentification SQL Server.
- Permet de bloquer la prolifération des identités utilisateur sur plusieurs serveurs de base de données.
- Permet la rotation de mot de passe à un emplacement unique
- Les clients peuvent gérer les autorisations de base de données à l’aide de groupes (Azure AD) externes.
- Elle peut éliminer le stockage des mots de passe en activant l’authentification intégrée Windows et les autres formes d’authentification prises en charge par Azure Active Directory.
- L’authentification Azure AD utilise des utilisateurs de base de données autonome pour authentifier les identités au niveau de la base de données.
- Azure AD prend en charge l’authentification basée sur des jetons pour les applications se connectant à SQL Database.
- L’authentification Azure AD prend en charge :
  - Identités uniquement cloud Azure AD
  - Identités hybrides Azure AD prenant en charge :
    - Authentification cloud avec deux options associées à une authentification unique (SSO) transparente : authentification**directe** et authentification par **hachage du mot de passe**
    - Authentification fédérée
  - Pour plus d’informations sur les méthodes d’authentification Azure AD et laquelle choisir, consultez l’article suivant :
    - [Choisir la méthode d’authentification adaptée à votre solution d’identité hybride Azure Active Directory](../active-directory/hybrid/choose-ad-authn.md)
- Azure AD prend en charge les connexions à partir de SQL Server Management Studio qui utilisent l’authentification universelle Active Directory, et notamment Multi-Factor Authentication (MFA).  MFA comprend une authentification forte avec une gamme d’options de vérification simples (appel téléphonique, SMS, cartes à puce avec code PIN ou notification d’application mobile). Pour plus d’informations, consultez [Prise en charge de SSMS pour Azure AD MFA avec SQL Database et Azure Synapse](sql-database-ssms-mfa-authentication.md).
- Azure AD prend en charge des connexions similaires à partir de SQL Server Data Tools (SSDT) qui utilisent l’authentification interactive Active Directory. Pour plus d’informations, consultez [Prise en charge d’Azure Active Directory dans SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory).

> [!NOTE]  
> La connexion à SQL Server s’exécutant sur une machine virtuelle Azure n’est pas prise en charge à l’aide d’un compte Azure Active Directory. Utilisez plutôt un compte Active Directory du domaine.  

Les étapes de configuration incluent les procédures suivantes pour configurer et utiliser l’authentification Azure Active Directory.

1. Créer et renseigner Azure AD.
2. Facultatif : associez ou modifiez l’annuaire Active Directory actuellement associé à votre abonnement Azure.
3. Créez un administrateur Azure Active Directory pour le serveur Azure SQL Database, l’instance managée ou [Azure Synapse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Configurer vos ordinateurs clients.
5. Créer des utilisateurs de base de données autonome dans votre base de données mappés sur les identités Azure AD.
6. Se connecter à la base de données à l’aide des identités Azure AD.

> [!NOTE]
> Pour apprendre à créer et à remplir Azure AD, puis à configurer Azure AD avec Azure SQL Database, l’instance managée et Azure Synapse, consultez [Configurer Azure AD avec Azure SQL Database](sql-database-aad-authentication-configure.md).

## <a name="trust-architecture"></a>Architecture d’approbation

- Pour prendre en charge le mot de passe des utilisateurs natifs d’Azure AD, seuls la partie cloud et Azure AD/Azure SQL Database sont considérés.
- Pour prendre en charge les informations d’identification de l’authentification unique Windows (ou utilisateur/mot de passe pour les informations d’identification Windows), utilisez les informations d’identification Azure Active Directory à partir d’un domaine fédéré ou managé configuré pour une authentification unique transparente à des fins d’authentification directe et d’authentification par hachage du mot de passe. Pour plus d’informations, consultez la page [Authentification unique transparente Azure Active Directory](../active-directory/hybrid/how-to-connect-sso.md).
- Pour prendre en charge l’authentification fédérée (ou utilisateur/mot de passe pour les informations d’identification Windows), la communication avec le bloc ADFS est requise.

Pour plus d’informations sur les identités hybrides Azure AD, la configuration et la synchronisation, consultez les articles suivants :

- Authentification par hachage du mot de passe : [Implémenter la synchronisation de hachage du mot de passe avec la synchronisation Azure AD Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Authentification directe : [Authentification directe Azure Active Directory](../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Authentification fédérée : [Déploiement des services de fédération Active Directory (AD FS) dans Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) et [Fédération avec Azure AD Connect](../active-directory/hybrid/how-to-connect-fed-whatis.md)

Pour obtenir un exemple d’authentification fédérée avec l’infrastructure ADFS (ou utilisateur/mot de passe pour les informations d’identification Windows), consultez le diagramme ci-dessous. Les flèches indiquent les voies de communication.

![diagramme autorisation aad][1]

Le diagramme suivant indique la fédération, l’approbation et les relations d’hébergement qui autorisent un client à se connecter à une base de données en soumettant un jeton. Le jeton est authentifié par une instance Azure AD, et approuvé par la base de données. Le client 1 peut représenter un répertoire Azure Active Directory avec des utilisateurs natifs ou un répertoire Azure AD avec des utilisateurs fédérés. Le client 2 représente une solution possible incluant des utilisateurs importés, qui dans cet exemple proviennent d’un répertoire Azure Active Directory fédéré avec la synchronisation d’ADFS avec Azure Active Directory. Il est important de comprendre que l’accès à une base de données à l’aide de l’authentification Azure AD exige que l’abonnement d’hébergement soit associé à Azure AD. Le même abonnement doit être utilisé pour créer le serveur SQL Server hébergeant la base de données Azure SQL ou Azure Synapse.

![relation abonnement][2]

## <a name="administrator-structure"></a>Structure de l’administrateur

En cas d’utilisation de l’authentification Azure AD, il existe deux comptes administrateur pour le serveur SQL Database et l’instance managée : l’administrateur SQL Server d’origine et l’administrateur Azure AD. Les mêmes concepts s’appliquent à Azure Synapse. Seul l’administrateur basé sur un compte Azure AD peut créer le premier utilisateur de la base de données autonome Azure AD dans une base de données utilisateur. La connexion d’administrateur Azure AD peut être un utilisateur Azure AD ou un groupe Azure AD. Lorsque l’administrateur est un compte de groupe, il peut être utilisé par n’importe quel membre du groupe, ce qui permet à plusieurs administrateurs Azure AD pour l’instance de SQL Server. L’utilisation d’un compte de groupe en tant qu’administrateur facilite la gestion en vous permettant d’ajouter et de supprimer des membres du groupe dans Azure AD sans modifier les utilisateurs ou les autorisations de base de données SQL. Seul un administrateur Azure AD (utilisateur ou groupe) peut être configuré à tout moment.

![structure admin][3]

## <a name="permissions"></a>Autorisations

Pour créer de nouveaux utilisateurs, vous devez disposer de l’autorisation `ALTER ANY USER` dans la base de données. L’autorisation `ALTER ANY USER` peut être octroyée à un utilisateur de base de données. L’autorisation `ALTER ANY USER` est également détenue par les comptes d’administrateur de serveur et les utilisateurs de base de données avec les autorisations `CONTROL ON DATABASE` ou `ALTER ON DATABASE` pour cette base de données et par les membres du rôle de base de données `db_owner`.

Pour créer un utilisateur de base de données autonome dans Azure SQL Database, l’instance managée ou Azure Synapse, vous devez vous connecter à la base de données ou à l’instance avec une identité Azure AD. Pour créer le premier utilisateur de la base de données autonome, vous devez vous connecter à la base de données à l’aide d’un administrateur Azure AD (le propriétaire de la base de données). Cette procédure est expliquée dans [Configurer et gérer l’authentification Azure Active Directory avec SQL Database ou Azure Synapse](sql-database-aad-authentication-configure.md). L’authentification Azure AD est possible seulement si l’administrateur Azure AD a été créé pour Azure SQL Database ou Azure Synapse. Si l’administrateur Azure Active Directory a été supprimé du serveur, les utilisateurs Azure Active Directory existants créés précédemment dans le serveur SQL Server ne peuvent plus se connecter à la base de données à l’aide de leurs informations d’identification Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Limitations et fonctionnalités azure AD

- Les membres suivants d’Azure AD peuvent être provisionnés dans le serveur Azure SQL Server ou dans Azure Synapse :

  - Membre natif : membre créé dans Azure AD dans le domaine géré ou le domaine client. Pour plus d’informations, consultez [Ajout de votre nom de domaine personnalisé à Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
  - Membres d’un domaine Active Directory fédéré avec Azure Active Directory sur un domaine managé configuré pour l’authentification unique transparente avec authentification directe ou par hachage du mot de passe. Pour plus d’informations, consultez [Microsoft Azure prend désormais en charge la fédération avec Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) et [Authentification unique transparente Azure Active Directory](../active-directory/hybrid/how-to-connect-sso.md).
  - Membres importés à partir d’autres répertoires Azure AD qui sont des membres natifs ou de domaine fédéré.
  - Groupes Active Directory créés en tant que groupes de sécurité.

- Les utilisateurs Azure AD qui font partie d’un groupe doté du rôle serveur `db_owner` ne peuvent pas utiliser la syntaxe **[CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** dans Azure SQL Database et Azure Synapse. Vous voyez l’erreur suivante :

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Accordez le rôle `db_owner` directement à l’utilisateur Azure AD individuel pour atténuer le problème lié à **CREATE DATABASE SCOPED CREDENTIAL**.

- Ces fonctions système retournent des valeurs NULL lors de leur exécution sous des entités Azure AD :

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Instances managées

- Les principaux de serveur (connexions) et les utilisateurs Azure AD sont pris en charge en tant que fonctionnalité d’évaluation pour les [instances managées](sql-database-managed-instance.md).
- La définition de principaux de serveur (connexions) Azure AD mappés sur un groupe Azure AD en tant que propriétaire de la base de données n’est pas prise en charge dans les [instances managées](sql-database-managed-instance.md).
    - Quand vous ajoutez un groupe dans le cadre du rôle serveur `dbcreator`, les utilisateurs de ce groupe peuvent donc se connecter à l’instance managée et créer des bases de données, mais ils ne peuvent pas accéder à la base de données. En effet, le propriétaire de la nouvelle base de données est SA et non l’utilisateur Azure AD. Ce problème ne se manifeste pas si l’utilisateur individuel est ajouté au rôle serveur `dbcreator`.
- La gestion et l’exécution de travaux SQL Agent sont prises en charge pour les principaux de serveur (connexions) Azure AD.
- Les opérations de sauvegarde et de restauration de base de données peuvent être exécutées par les principaux de serveur (connexions) Azure AD.
- L’audit de toutes les instructions liées aux principaux de serveur (connexions) Azure AD et aux événements d’authentification Azure AD est pris en charge.
- La connexion administrateur dédiée pour les principaux de serveur (connexions) Azure AD membres du rôle serveur sysadmin est prise en charge.
    - Prise en charge par le biais de l’utilitaire SQLCMD et de SQL Server Management Studio.
- Les déclencheurs d’ouverture de session sont pris en charge pour les événements d’ouverture de session provenant de principaux de serveur (connexions) Azure AD.
- Vous pouvez configurer Service Broker et Database Mail à l’aide d’un principal de serveur (connexion) Azure AD.


## <a name="connecting-using-azure-ad-identities"></a>Connexion à l’aide des identités Azure AD

L’authentification Azure Active Directory prend en charge les méthodes suivantes de connexion à une base de données à l’aide d’identités Azure AD :

- Mot de passe Azure Active Directory
- Intégration d’Azure Active Directory
- Authentification universelle Azure Active Directory avec MFA
- À l’aide de l’authentification par jeton d’application

Les méthodes d’authentification suivantes sont prises en charge pour les principaux de serveur (connexions) Azure AD :

- Mot de passe Azure Active Directory
- Intégration d’Azure Active Directory
- Authentification universelle Azure Active Directory avec MFA


### <a name="additional-considerations"></a>Considérations supplémentaires

- Pour améliorer la facilité de gestion, nous vous conseillons de mettre en service un groupe Azure AD dédié en tant qu’administrateur.   
- Un seul administrateur Azure AD (utilisateur ou groupe) peut être configuré pour un serveur Azure SQL Database ou Azure Synapse à tout moment.
  - L’ajout de principaux de serveur (connexions) Azure AD pour des instances managées permet de créer plusieurs principaux de serveur (connexions) Azure AD pouvant être ajoutés au rôle `sysadmin`.
- Seul un administrateur Azure AD pour SQL Server peut se connecter initialement au serveur Azure SQL Database, à l’instance managée ou à Azure Synapse avec un compte Azure Active Directory. L’administrateur Active Directory peut configurer les utilisateurs de base de données Azure AD suivants.   
- Nous vous conseillons de définir l’expiration du délai de connexion à 30 secondes.   
- SQL Server 2016 Management Studio et SQL Server Data Tools pour Visual Studio 2015 (version 14.0.60311.1 d’avril 2016 ou ultérieure) prennent en charge l’authentification Azure Active Directory. (L’authentification Azure AD est prise en charge par le **Fournisseur de données .NET Framework pour SQL Server** ; .NET Framework version 4.6 minimum). Par conséquent, les dernières versions de ces outils et applications de la couche Données (DAC et .bacpac) peuvent utiliser l’authentification Azure AD.   
- À partir de la version 15.0.1, l’[utilitaire sqlcmd](/sql/tools/sqlcmd-utility) et l’[utilitaire bcp](/sql/tools/bcp-utility) prennent en charge l’authentification interactive Active Directory avec MFA.
- SQL Server Data Tools pour Visual Studio 2015 requiert la version d’avril 2016 (version 14.0.60311.1) ou une version ultérieure. Actuellement, les utilisateurs Azure AD ne sont pas affichés dans l’Explorateur d’objets SSDT. Comme solution de contournement, vous pouvez afficher les utilisateurs dans [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- Le [pilote Microsoft JDBC 6.0 pour SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) prend en charge l’authentification Azure AD. Consultez également [Définition des propriétés de connexion](https://msdn.microsoft.com/library/ms378988.aspx).   
- PolyBase ne peut pas s’authentifier avec l’authentification Azure AD.   
- L’authentification Azure AD est prise en charge pour SQL Database dans les panneaux **Importer la base de données** et **Exporter la base de données** du portail Azure. L’importation et l’exportation à l’aide de l’authentification Azure AD sont également prises en charge depuis l’invite de commandes PowerShell.   
- L’authentification Azure AD est prise en charge pour SQL Database, l’instance managée et Azure Synapse par le biais de l’interface de ligne de commande (CLI). Pour plus d’informations, consultez [Configurer et gérer l’authentification Azure Active Directory avec SQL Database ou Azure Synapse](sql-database-aad-authentication-configure.md) et [SQL Server - az sql server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Étapes suivantes

- Pour apprendre à créer et à remplir Azure AD, puis à configurer Azure AD avec Azure SQL Database ou Azure Synapse, consultez [Configurer et gérer l’authentification Azure Active Directory avec SQL Database, l’instance managée ou Azure Synapse](sql-database-aad-authentication-configure.md).
- Pour suivre un tutoriel sur l’utilisation de principaux de serveur (connexions) Azure AD avec des instances managées, consultez [Principaux de serveur (connexions) Azure AD avec des instances managées](sql-database-managed-instance-aad-security-tutorial.md).
- Pour une vue d’ensemble des connexions, des utilisateurs, des rôles de base de données et des autorisations dans SQL Database, consultez [Connexions, utilisateurs, rôles de base de données et autorisations](sql-database-manage-logins.md).
- Pour en savoir plus sur les principaux de base de données, voir [Principaux](https://msdn.microsoft.com/library/ms181127.aspx).
- Pour en savoir plus sur les rôles de base de données, voir [Rôles de base de données](https://msdn.microsoft.com/library/ms189121.aspx).
- Pour obtenir la syntaxe permettant de créer des principaux de serveur (connexions) Azure AD pour des instances managées, consultez [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).
- Pour en savoir plus sur les règles de pare-feu dans la base de données SQL, voir [Règles de pare-feu de la base de données SQL](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png
