---
title: Créer des utilisateurs invités Azure AD
description: Comment créer des utilisateurs invités Azure AD et les définir comme administrateurs Azure AD sans utiliser de groupes Azure AD dans Azure SQL Database, Azure SQL Managed Instance et Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 05/10/2021
ms.openlocfilehash: 91c6893320273ae29cb504715b5f27365a0161be
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434229"
---
# <a name="create-azure-ad-guest-users-and-set-as-an-azure-ad-admin"></a>Créer des utilisateurs invités Azure AD et les définir comme administrateurs Azure AD

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Dans Azure Active Directory (Azure AD), les utilisateurs invités sont des utilisateurs qui ont été importés dans l’instance Azure AD active à partir d’autres annuaires Azure Active Directory ou en dehors de celle-ci. Par exemple, parmi les utilisateurs invités peuvent figurer des utilisateurs issus d’autres annuaires Azure Active Directory ou de comptes comme *\@outlook.com*, *\@hotmail.com*, *\@live.com* ou *\@gmail.com*. 

Cet article explique comment créer un utilisateur invité Azure AD et définir celui-ci en tant qu’administrateur Azure AD pour Azure SQL Managed Instance ou le [serveur logique dans Azure](logical-servers.md) utilisé par Azure SQL Database et Azure Synapse Analytics, sans avoir à ajouter l’utilisateur invité à un groupe dans Azure AD.

## <a name="feature-description"></a>Description de la fonctionnalité

Cette fonctionnalité lève la limitation actuelle qui veut que seuls les utilisateurs invités peuvent se connecter à Azure SQL Database, SQL Managed Instance ou Azure Synapse Analytics s’ils sont membres d’un groupe créé dans Azure AD. Le groupe devait être mappé à un utilisateur manuellement à l’aide de l’instruction [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) dans une base de données spécifique. Dès lors qu’un utilisateur de base de données a été créé pour le groupe Azure AD contenant l’utilisateur invité, celui-ci peut se connecter à la base de données en utilisant Azure Active Directory avec l’authentification MFA. Il est possible de créer des utilisateurs invités et de connecter ceux-ci directement à SQL Database, SQL Managed Instance ou Azure Synapse sans avoir à les ajouter préalablement à un groupe Azure AD, puis à créer un utilisateur de base de données pour ce groupe Azure AD.

Cette fonctionnalité vous donne aussi la possibilité de définir l’utilisateur invité Azure AD directement comme administrateur AD pour le serveur logique ou une instance gérée. La fonctionnalité existante (permettant que l’utilisateur invité fasse partie d’un groupe Azure AD pouvant être défini comme administrateur Azure AD pour le serveur logique ou l’instance gérée) n’est *pas* affectée. Les utilisateurs invités de la base de données qui font partie d’un groupe Azure AD ne sont pas non plus affectés par ce changement.

Pour plus d’informations sur la prise en charge existante des utilisateurs invités utilisant des groupes Azure AD, consultez [Utilisation de l’authentification multifacteur Azure Active Directory](authentication-mfa-ssms-overview.md).

## <a name="prerequisite"></a>Configuration requise

- Le module [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) (ou version supérieure) est nécessaire quand vous utilisez PowerShell pour définir un utilisateur invité comme administrateur Azure AD pour le serveur logique ou l’instance gérée.

## <a name="create-database-user-for-azure-ad-guest-user"></a>Créer un utilisateur de base de données pour l’utilisateur invité Azure AD 

Suivez ces étapes pour créer un utilisateur de base de données à partir d’un utilisateur invité Azure AD.

### <a name="create-guest-user-in-sql-database-and-azure-synapse"></a>Créer un utilisateur invité dans SQL Database et Azure Synapse

1. Vérifiez que l’utilisateur invité (par exemple, `user1@gmail.com`) est déjà ajouté à votre instance Azure AD et qu’un administrateur Azure AD a été défini pour le serveur de base de données. Il est nécessaire de disposer d’un administrateur Azure AD pour l’authentification Azure Active Directory.

1. Connectez-vous à la base de données SQL en tant qu’administrateur Azure AD ou utilisateur Azure AD doté d’autorisations SQL suffisantes pour créer des utilisateurs, puis exécutez la commande ci-dessous sur la base de données à laquelle l’utilisateur invité doit être ajouté :

    ```sql
    CREATE USER [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. Un utilisateur de base de données doit maintenant être créé pour l’utilisateur invité `user1@gmail.com`.

1. Exécutez la commande ci-dessous pour vérifier que l’utilisateur de base de données a bien été créé :

    ```sql
    SELECT * FROM sys.database_principals
    ```

1. Déconnectez-vous et connectez-vous à la base de données en tant qu’utilisateur invité `user1@gmail.com` en utilisant [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) avec la méthode d’authentification **Azure Active Directory - Authentification universelle avec MFA**. Pour plus d’informations, consultez [Utilisation de l’authentification multifacteur Azure Active Directory](authentication-mfa-ssms-overview.md).

### <a name="create-guest-user-in-sql-managed-instance"></a>Créer un utilisateur invité dans SQL Managed Instance

> [!NOTE]
> SQL Managed Instance prend en charge les connexions des utilisateurs Azure AD ainsi que des utilisateurs de base de données Azure AD à relation contenant-contenu. Les étapes ci-dessous montrent comment créer une connexion et un utilisateur pour un utilisateur invité Azure AD dans SQL Managed Instance. Vous pouvez aussi choisir de créer un [utilisateur de base de données à relation contenant-contenu](/sql/relational-databases/security/contained-database-users-making-your-database-portable) dans SQL Managed Instance en employant la méthode de la section [Créer un utilisateur invité dans SQL Database et Azure Synapse](#create-guest-user-in-sql-database-and-azure-synapse).

1. Vérifiez que l’utilisateur invité (par exemple, `user1@gmail.com`) est déjà ajouté à votre instance Azure AD et qu’un administrateur Azure AD a été défini pour le serveur SQL Managed Instance. Il est nécessaire de disposer d’un administrateur Azure AD pour l’authentification Azure Active Directory.

1. Connectez-vous au serveur SQL Managed Instance en tant qu’administrateur Azure AD ou utilisateur Azure AD doté d’autorisations SQL suffisantes pour créer des utilisateurs, puis exécutez la commande suivante sur la base de données `master` pour créer une connexion pour l’utilisateur invité :

    ```sql
    CREATE LOGIN [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. Une connexion doit maintenant être créée pour l’utilisateur invité `user1@gmail.com` dans la base de données `master`.

1. Exécutez la commande ci-dessous pour vérifier que la connexion a bien été créée :

    ```sql
    SELECT * FROM sys.server_principals
    ```

1. Exécutez la commande ci-dessous sur la base de données dans laquelle l’utilisateur invité doit être ajouté : 

    ```sql
    CREATE USER [user1@gmail.com] FROM LOGIN [user1@gmail.com]
    ```

1. Un utilisateur de base de données doit maintenant être créé pour l’utilisateur invité `user1@gmail.com`.

1. Déconnectez-vous et connectez-vous à la base de données en tant qu’utilisateur invité `user1@gmail.com` en utilisant [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) avec la méthode d’authentification **Azure Active Directory - Authentification universelle avec MFA**. Pour plus d’informations, consultez [Utilisation de l’authentification multifacteur Azure Active Directory](authentication-mfa-ssms-overview.md).

## <a name="setting-a-guest-user-as-an-azure-ad-admin"></a>Définition d’un utilisateur invité comme administrateur Azure AD

Définissez l’administrateur Azure AD à l’aide du portail Azure, d’Azure PowerShell ou d’Azure CLI. 

### <a name="azure-portal"></a>Portail Azure 

Pour configurer un administrateur Azure AD pour un serveur logique ou une instance gérée à l’aide du portail Azure, procédez comme suit : 

1. Ouvrez le [Portail Azure](https://portal.azure.com). 
1. Accédez aux paramètres **Azure Active Directory** de votre SQL Server ou instance gérée. 
1. Sélectionnez **Définir l’administrateur**. 
1. Dans la fenêtre d’invite d’Azure AD, tapez l’utilisateur invité, par exemple, `guestuser@gmail.com` . 
1. Sélectionnez ce nouvel utilisateur, puis enregistrez l’opération. 

Pour plus d’informations, consultez [Définition d’administrateur Azure AD](authentication-aad-configure.md#azure-ad-admin-with-a-server-in-sql-database). 


### <a name="azure-powershell-sql-database-and-azure-synapse"></a>Azure PowerShell (SQL Database et Azure Synapse)

Pour configurer un utilisateur invité Azure AD pour un serveur logique, procédez comme suit :  

1. Vérifiez que l’utilisateur invité (par exemple, `user1@gmail.com`) est déjà ajouté à votre instance Azure AD.

1. Exécutez la commande PowerShell suivante pour ajouter l’utilisateur invité comme administrateur Azure AD pour votre serveur logique :

    - Remplacez `<ResourceGroupName>` par le nom de votre groupe de ressources Azure contenant le serveur logique.
    - Remplacez `<ServerName>` par le nom de votre serveur logique. Si le nom de votre serveur est `myserver.database.windows.net`, remplacez `<Server Name>` par `myserver`.
    - Remplacez `<DisplayNameOfGuestUser>` par le nom de votre utilisateur invité.

    ```powershell
    Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -ServerName <ServerName> -DisplayName <DisplayNameOfGuestUser>
    ```

Vous pouvez aussi utiliser la commande Azure CLI [az sql server ad-admin](/cli/azure/sql/server/ad-admin) pour définir l’utilisateur invité comme administrateur Azure AD pour votre serveur logique.

### <a name="azure-powershell-sql-managed-instance"></a>Azure PowerShell (SQL Managed Instance)

Pour configurer un utilisateur invité Azure AD pour une instance gérée, procédez comme suit :  

1. Vérifiez que l’utilisateur invité (par exemple, `user1@gmail.com`) est déjà ajouté à votre instance Azure AD.

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis accédez à votre ressource **Azure Active Directory**. Sous **Gérer**, accédez au volet **Utilisateurs**. Sélectionnez votre utilisateur invité et inscrivez `Object ID`. 

1. Exécutez la commande PowerShell suivante pour ajouter l’utilisateur invité comme administrateur Azure AD pour votre SQL Managed Instance :

    - Remplacez `<ResourceGroupName>` par le nom de votre groupe de ressources Azure qui contient SQL Managed Instance.
    - Remplacez `<ManagedInstanceName>` par le nom de votre SQL Managed Instance.
    - Remplacez `<DisplayNameOfGuestUser>` par le nom de votre utilisateur invité.
    - Remplacez `<AADObjectIDOfGuestUser>` par le `Object ID` récupéré précédemment.

    ```powershell
    Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -InstanceName "<ManagedInstanceName>" -DisplayName <DisplayNameOfGuestUser> -ObjectId <AADObjectIDOfGuestUser>
    ```

Vous pouvez aussi utiliser la commande Azure CLI [az sql mi ad-admin](/cli/azure/sql/mi/ad-admin) pour définir l’utilisateur invité comme administrateur Azure AD pour votre instance gérée.


## <a name="next-steps"></a>Étapes suivantes

- [Configurer et gérer l’authentification Azure AD avec Azure SQL](authentication-aad-configure.md)
- [Utilisation de l’authentification multifacteur Azure Active Directory](authentication-mfa-ssms-overview.md)
- [CRÉER UN UTILISATEUR (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql)