---
title: Rôles serveur
titleSuffix: Azure SQL Database
description: Cet article fournit une vue d’ensemble des rôles serveur pour le serveur logique d’Azure SQL Database.
ms.service: sql-database
ms.subservice: security
author: AndreasWolter
ms.author: anwolter
ms.topic: conceptual
ms.date: 09/02/2021
ms.reviewer: vanto
ms.openlocfilehash: cfb24ee98bf00fa46f75b0bef321a7b1694e2113
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630259"
---
# <a name="azure-sql-database-server-roles-for-permission-management"></a>Rôles serveur Azure SQL Database pour la gestion des autorisations

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dans Azure SQL Database, le serveur est un concept logique, et les autorisations ne peuvent pas être accordées au niveau du serveur. Pour vous aider à gérer les autorisations sur un [serveur logique](logical-servers.md), Azure SQL Database fournit un ensemble de rôles de niveau serveur fixes. Les rôles sont des principaux de sécurité qui regroupent des connexions.

> [!NOTE]
> Dans cet article, le concept de *rôle* s’apparente aux *groupes* du système d’exploitation Windows.

Ces rôles de niveau serveur fixes spéciaux utilisent le préfixe **##MS_** et le suffixe **##** afin de pouvoir être distingués des principaux standard créés par l’utilisateur.

Comme SQL Server local, les autorisations de serveur sont organisées de façon hiérarchique. Les autorisations détenues par ces rôles au niveau du serveur peuvent se propager aux autorisations de base de données. Pour que les autorisations soient propagées efficacement à la base de données, une connexion doit avoir un compte d’utilisateur dans la base de données.

Par exemple, le rôle au niveau du serveur **##MS_ServerStateReader##** détient l’autorisation **VIEW SERVER STATE**. Si une connexion membre de ce rôle a un compte d’utilisateur dans les bases de données *master* et *WideWorldImporters*, cet utilisateur aura l’autorisation **VIEW DATABASE STATE** dans ces deux bases de données. 

> [!NOTE]
> Toute autorisation peut être refusée dans les bases de données utilisateur, ce qui a pour effet de remplacer l’accord à l’échelle du serveur via l’appartenance à un rôle. Toutefois, dans la base de données système *master*, les autorisations ne peuvent pas être accordées ou refusées.

Azure SQL Database fournit actuellement trois rôles serveur fixes. Les autorisations accordées aux rôles serveur fixes ne peuvent pas être modifiées, et ces rôles ne peuvent pas avoir d’autres rôles fixes en tant que membres. Vous pouvez ajouter des connexions SQL au niveau du serveur en tant que membres à des rôles au niveau du serveur.

> [!IMPORTANT]
> Chaque membre d'un rôle serveur fixe peut ajouter des connexions à ce rôle.

Pour plus d’informations sur les connexions et les utilisateurs Azure SQL Database, consultez [Configurer SQL Database, SQL Managed Instance et Azure Synapse Analytics pour autoriser l’accès aux bases de données](logins-create-manage.md).
  
## <a name="built-in-server-level-roles"></a>Rôles de niveau serveur intégrés

Le tableau ci-dessous répertorie les rôles serveur fixes et leurs fonctionnalités.  
  
|Rôle de niveau serveur intégré|Description|  
|------------------------------|-----------------|  
|**##MS_DefinitionReader##**|Les membres du rôle serveur fixe **##MS_DefinitionReader##** peuvent lire toutes les vues catalogue qui sont couvertes par **VIEW ANY DEFINITION**, respectivement **VIEW DEFINITION** sur toute base de données sur laquelle le membre de ce rôle détient un compte d’utilisateur.|  
|**##MS_ServerStateReader##**|Les membres du rôle serveur fixe **##MS_ServerStateReader##** peuvent lire toutes les vues de gestion dynamiques et les fonctions qui sont couvertes par **VIEW SERVER STATE**, respectivement **VIEW DATABASE STATE** sur toute base de données sur laquelle le membre de ce rôle détient un compte d’utilisateur.|
|**##MS_ServerStateManager##**|Les membres du rôle serveur fixe **##MS_ServerStateManager##** ont les mêmes autorisations que celles accordées au rôle **##MS_ServerStateReader##** . En outre, ce rôle contient l’autorisation **ALTER SERVER STATE**, qui permet d’accéder à plusieurs opérations de gestion, telles que `DBCC FREEPROCCACHE`, `DBCC FREESYSTEMCACHE ('ALL')` et `DBCC SQLPERF()`. |  


## <a name="permissions-of-fixed-server-roles"></a>Autorisations des rôles serveur fixes

Chaque rôle de niveau serveur intégré dispose de certaines autorisations. Le tableau suivant présente les autorisations attribuées aux rôles de niveau serveur. Il montre également les autorisations au niveau de la base de données héritées si un compte d’utilisateur existe dans la base de données.
  
|Rôles serveur fixes|Autorisations au niveau du serveur|Autorisations au niveau de la base de données (s’il existe un utilisateur de base de données correspondant à la connexion)  
|-------------|----------|-----------------|  
|**##MS_DefinitionReader##**|VIEW ANY DATABASE, VIEW ANY DEFINITION, VIEW ANY SECURITY DEFINITION|VIEW DEFINITION, VIEW SECURITY DEFINITION|  
|**##MS_ServerStateReader##**|VIEW SERVER STATE, VIEW SERVER PERFORMANCE STATE, VIEW SERVER SECURITY STATE|VIEW DATABASE STATE, VIEW DATABASE PERFORMANCE STATE, VIEW DATABASE SECURITY STATE|  
|**##MS_ServerStateManager##**|ALTER SERVER STATE, VIEW SERVER STATE, VIEW SERVER PERFORMANCE STATE, VIEW SERVER SECURITY STATE|VIEW DATABASE STATE, VIEW DATABASE PERFORMANCE STATE, VIEW DATABASE SECURITY STATE|   
  
  
## <a name="working-with-server-level-roles"></a>Utilisation des rôles de niveau serveur

Le tableau ci-dessous explique les vues système et les fonctions permettant d’utiliser les rôles serveur dans Azure SQL Database.  
  
|Fonctionnalité|Type|Description|  
|-------------|----------|-----------------|  
|[IS_SRVROLEMEMBER &#40;Transact-SQL&#41;](/sql/t-sql/functions/is-srvrolemember-transact-sql)|Métadonnées|Indique si une connexion SQL est un membre du rôle de niveau serveur spécifié.|  
|[sys.server_role_members &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-server-role-members-transact-sql)|Métadonnées|Retourne une ligne pour chaque membre de chaque rôle serveur.|
|[sys.sql_logins &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-sql-logins-transact-sql)|Métadonnées|Retourne une ligne pour chaque connexion SQL.|
|[ALTER SERVER ROLE &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-server-role-transact-sql)|Commande|Change l’appartenance d’un rôle serveur.| 

## <a name="examples"></a><a name="_examples"></a> Exemples

Les exemples de cette section montrent comment utiliser des rôles de niveau serveur dans Azure SQL Database.  

### <a name="a-adding-a-sql-login-to-a-server-level-role"></a>R. Ajout d’une connexion SQL à un rôle de niveau serveur

L’exemple suivant ajoute la connexion SQL « Jiao » au rôle de niveau serveur ##MS_ServerStateReader##. Cette instruction doit être exécutée dans la base de données MASTER virtuelle.
  
```sql  
ALTER SERVER ROLE ##MS_ServerStateReader##
    ADD MEMBER Jiao;  
GO
```  

### <a name="b-listing-all-principals-sql-authentication-which-are-members-of-a-server-level-role"></a>B. Liste de tous les principaux (authentification SQL) qui sont membres d’un rôle au niveau du serveur

L’instruction suivante retourne tous les membres d’un rôle serveur fixe à l’aide des vues catalogue `sys.server_role_members` et `sys.sql_logins`. Cette instruction doit être exécutée dans la base de données MASTER virtuelle.
  
```sql  
SELECT
        sql_logins.principal_id         AS MemberPrincipalID
    ,   sql_logins.name                 AS MemberPrincipalName
    ,   roles.principal_id              AS RolePrincipalID
    ,   roles.name                      AS RolePrincipalName
FROM sys.server_role_members AS server_role_members
INNER JOIN sys.server_principals AS roles
    ON server_role_members.role_principal_id = roles.principal_id
INNER JOIN sys.sql_logins AS sql_logins 
    ON server_role_members.member_principal_id = sql_logins.principal_id
;  
GO  
```  
### <a name="c-complete-example-adding-a-login-to-a-server-level-role-retrieving-metadata-for-role-membership-and-permissions-and-running-a-test-query"></a>C. Exemple complet : ajout d’une connexion à un rôle au niveau du serveur, récupération des métadonnées pour l’appartenance au rôle et les autorisations, et exécution d’une requête de test

#### <a name="part-1-preparing-role-membership-and-user-account"></a>Partie 1 : Préparation de l’appartenance au rôle et du compte d’utilisateur

Exécutez cette commande à partir de la base de données MASTER virtuelle.

```sql  
ALTER SERVER ROLE ##MS_ServerStateReader##
    ADD MEMBER Jiao

-- check membership in metadata:
select IS_SRVROLEMEMBER('##MS_ServerStateReader##', 'Jiao')
--> 1 = Yes

SELECT
        sql_logins.principal_id         AS MemberPrincipalID
    ,   sql_logins.name                 AS MemberPrincipalName
    ,   roles.principal_id              AS RolePrincipalID
    ,   roles.name                      AS RolePrincipalName
FROM sys.server_role_members AS server_role_members
INNER JOIN sys.server_principals AS roles
    ON server_role_members.role_principal_id = roles.principal_id
INNER JOIN sys.sql_logins AS sql_logins 
    ON server_role_members.member_principal_id = sql_logins.principal_id
;   
GO  
``` 

Voici le jeu de résultats obtenu.
  
```
MemberPrincipalID MemberPrincipalName RolePrincipalID RolePrincipalName        
------------- ------------- ------------------ -----------   
6         Jiao      11            ##MS_ServerStateReader##   
```  

Exécutez cette commande à partir d’une base de données utilisateur.

```sql  
-- Creating a database-User for 'Jiao'
CREATE USER Jiao
    FROM LOGIN Jiao
;   
GO  
``` 

#### <a name="part-2-testing-role-membership"></a>Partie 2 : Test de l’appartenance au rôle

Connectez-vous en tant que `Jiao` et connectez-vous à la base de données utilisateur utilisée dans l’exemple.

```sql  
-- retrieve server-level permissions of currently logged on User
SELECT * FROM sys.fn_my_permissions(NULL, 'Server')
;  

-- check server-role membership for `##MS_ServerStateReader##` of currently logged on User
SELECT USER_NAME(), IS_SRVROLEMEMBER('##MS_ServerStateReader##')
--> 1 = Yes

-- Does the currently logged in User have the `VIEW DATABASE STATE`-permission?
SELECT HAS_PERMS_BY_NAME(NULL, 'DATABASE', 'VIEW DATABASE STATE'); 
--> 1 = Yes

-- retrieve database-level permissions of currently logged on User
SELECT * FROM sys.fn_my_permissions(NULL, 'DATABASE')
GO 

-- example query:
SELECT * FROM sys.dm_exec_query_stats
--> will return data since this user has the necessary permission

``` 

## <a name="limitations-of-server-level-roles"></a>Limitations des rôles au niveau du serveur

- La prise d’effet des attributions de rôles peut prendre jusqu’à cinq minutes. De plus, pour les sessions existantes, les modifications apportées aux attributions de rôles serveur ne prennent pas effet tant que la connexion n’a pas été fermée puis rouverte. Cela est dû à l’architecture distribuée entre la base de données *master* et les autres bases de données sur le même serveur logique.
  - Solution de contournement partielle : pour réduire le délai d’attente et être sûr que les attributions de rôle serveur sont à jour dans une base de données, un administrateur de serveur ou un administrateur Azure AD peut s’exécuter `DBCC FLUSHAUTHCACHE` dans la ou les bases de données utilisateur auxquelles la connexion a accès. Les utilisateurs actuellement connectés doivent toujours se reconnecter après avoir exécuté `DBCC FLUSHAUTHCACHE` pour que les modifications d’appartenance prennent effet.

- `IS_SRVROLEMEMBER()` n’est pas pris en charge dans la base de données *master*.


## <a name="see-also"></a>Voir aussi

- [Rôles au niveau de la base de données](/sql/relational-databases/security/authentication-access/database-level-roles)   
- [Affichages catalogue de sécurité &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)   
- [Fonctions de sécurité &#40;Transact-SQL&#41;](/sql/t-sql/functions/security-functions-transact-sql)   
- [Autorisations &#40;moteur de base de données&#41;](/sql/relational-databases/security/permissions-database-engine)
- [DBCC FLUSHAUTHCACHE (Transact-SQL)](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql)
