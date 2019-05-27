---
title: Différences de SQL Database Managed Instance T-SQL Azure | Microsoft Docs
description: Cet article décrit les différences T-SQL entre une instance managée dans Azure SQL Database et dans SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
manager: craigg
ms.date: 03/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 17609212fcc7620dc0d6d617e7626d12c8bb0592
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65852149"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Différences T-SQL entre Azure SQL Database Managed Instance et SQL Server

Cet article résume et explique les différences de syntaxe et de comportement entre Azure SQL Database Managed Instance et le moteur de base de données locale SQL Server. Les sujets suivants sont abordés : <a name="Differences"></a>

- [Disponibilité](#availability) inclut les différences dans [Always On](#always-on-availability) et [sauvegardes](#backup).
- [Sécurité](#security) inclut les différences dans [audit](#auditing), [certificats](#certificates), [informations d’identification](#credential), [fournisseurs de chiffrement](#cryptographic-providers), [connexions et utilisateurs](#logins-and-users)et le [clé du service et la clé principale du service](#service-key-and-service-master-key).
- [Configuration](#configuration) inclut les différences dans [extension du pool de mémoires tampons](#buffer-pool-extension), [classement](#collation), [niveaux de compatibilité](#compatibility-levels), [mise en miroir de base de données ](#database-mirroring), [options de base de données](#database-options), [Agent SQL Server](#sql-server-agent), et [table options](#tables).
- [Fonctionnalités](#functionalities) inclut [insertion en bloc/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [les transactions distribuées](#distributed-transactions), [événements étendus](#extended-events), [bibliothèques externes](#external-libraries), [filestream et FileTable](#filestream-and-filetable), [recherche sémantique de recherche en texte intégral](#full-text-semantic-search), [serveurs liés](#linked-servers), [PolyBase](#polybase), [réplication](#replication), [restaurer](#restore-statement), [Service Broker](#service-broker), [, fonctions, déclencheurs et procédures stockées](#stored-procedures-functions-and-triggers).
- [Fonctionnalités qui présentent un comportement différent dans les instances gérées](#Changes).
- [Problèmes connus et limitations temporaires](#Issues).

L’option de déploiement Managed Instance est hautement compatible avec le moteur de base de données SQL Server local. La plupart des fonctionnalités du moteur de base de données SQL Server sont prises en charge dans une instance gérée.

![Migration](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>Disponibilité

### <a name="always-on-availability"></a>Always On

[Haute disponibilité](sql-database-high-availability.md) est intégré à Managed Instance et ne peut pas être contrôlé par les utilisateurs. Les instructions suivantes ne sont pas prises en charge :

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- Le [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) clause de le [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) instruction

### <a name="backup"></a>Sauvegarde

Instances managées ont des sauvegardes automatiques, les utilisateurs peuvent créer la base de données complète `COPY_ONLY` sauvegardes. Sauvegarde différentielle, journal et sauvegardes de captures instantanées de fichier ne sont pas pris en charge.

- Avec une instance gérée, vous pouvez sauvegarder une base de données d’instance uniquement pour un compte de stockage Blob Azure :
  - Seul `BACKUP TO URL` est pris en charge.
  - `FILE`, `TAPE`, et les unités de sauvegarde ne sont pas pris en charge.
- La plupart des générales `WITH` options sont prises en charge.
  - `COPY_ONLY` est obligatoire.
  - `FILE_SNAPSHOT` n’est pas pris en charge.
  - Options de bande : `REWIND`, `NOREWIND`, `UNLOAD`, et `NOUNLOAD` ne sont pas pris en charge.
  - Options spécifiques au journal : `NORECOVERY`, `STANDBY`, et `NO_TRUNCATE` ne sont pas pris en charge.

Limites : 

- Avec une instance gérée, vous pouvez sauvegarder une base de données d’instance vers une sauvegarde comprenant jusqu'à 32 bandes, ce qui est suffisant pour les bases de données jusqu'à 4 To si la compression de sauvegarde est utilisée.
- La taille d’entrelacement de sauvegarde maximale à l’aide de la `BACKUP` commande dans une instance managée est 195 Go, ce qui est la taille maximale des objets blob. Augmentez le nombre de bandes dans la commande de sauvegarde pour réduire la taille de bande individuelle et ne pas dépasser cette limite.

    > [!TIP]
    > Pour contourner cette limitation, lorsque vous sauvegardez une base de données à partir de SQL Server dans un environnement sur site ou sur une machine virtuelle, vous pouvez :
    >
    > - Sauvegarde sur `DISK` au lieu de la sauvegarde sur `URL`.
    > - Télécharger les fichiers de sauvegarde vers le stockage Blob.
    > - Restaurer dans l’instance gérée.
    >
    > Le `Restore` commande dans une instance gérée prend en charge des tailles d’objet blob plus volumineuses dans les fichiers de sauvegarde, car un type de l’autre objet blob est utilisé pour le stockage des fichiers de sauvegarde chargés.

Pour plus d’informations sur les sauvegardes à l’aide de T-SQL, consultez [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Sécurité

### <a name="auditing"></a>Audit

Les principales différences entre l’audit des bases de données dans Azure SQL Database et des bases de données dans SQL Server sont les suivantes :

- Avec l’option de déploiement de Managed Instance dans la base de données SQL Azure, l’audit fonctionne au niveau du serveur. Le `.xel` fichiers journaux sont stockés dans le stockage Blob Azure.
- Avec les options de déploiement de base de données unique et de pool élastique dans Azure SQL Database, l’audit fonctionne au niveau de la base de données.
- Dans SQL Server en local ou des machines virtuelles, l’audit fonctionne au niveau du serveur. Les événements sont stockés sur le système de fichiers ou les journaux des événements Windows.
 
L’audit XEvent dans Managed Instance prend en charge les cibles de Stockage Blob Azure. Journaux de fichiers et de Windows ne sont pas pris en charge.

Les principales différences de syntaxe `CREATE AUDIT` pour l’audit du Stockage Blob Azure sont :

- Une nouvelle syntaxe `TO URL` est à condition que vous pouvez utiliser pour spécifier l’URL du conteneur de stockage Blob Azure où le `.xel` fichiers sont placés.
- La syntaxe `TO FILE` n’est pas pris en charge, car une instance gérée ne peut pas accéder aux partages de fichiers Windows.

Pour plus d'informations, consultez les pages suivantes : 

- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Audit](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificats

Une instance gérée ne peut pas accéder aux partages de fichiers et dossiers Windows, donc les contraintes suivantes s’appliquent :

- Le `CREATE FROM` / `BACKUP TO` fichier n’est pas pris en charge pour les certificats.
- Le `CREATE` / `BACKUP` à partir du certificat `FILE` / `ASSEMBLY` n’est pas pris en charge. Les fichiers de clés privés ne peuvent pas être utilisés 

Consultez [CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) et [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Solution de contournement** : Générer un script pour le certificat ou la clé privée, stockez en tant que fichier .sql et créer à partir du fichier binaire :

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Informations d'identification

Seuls Azure Key Vault et les identités `SHARED ACCESS SIGNATURE` sont pris en charge. Les utilisateurs de Windows ne sont pas pris en charge.

Consultez [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) et [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Fournisseurs de chiffrement

Une instance gérée ne peut pas accéder aux fichiers, afin de fournisseurs de chiffrement ne peut pas être créés :

- `CREATE CRYPTOGRAPHIC PROVIDER` n’est pas pris en charge. Consultez [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` n’est pas pris en charge. Consultez [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Connexions et utilisateurs

- Les connexions SQL créées à l’aide de `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, et `FROM SID` sont pris en charge. Consultez [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Azure principaux de serveur Active Directory (Azure AD) (connexions) créés avec le [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) syntaxe ou le [créer de connexion de l’utilisateur [connexion Azure AD]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) syntaxe sont prises en charge (version préliminaire publique). Ces connexions sont créées au niveau du serveur.

    Managed Instance prend en charge les principaux de base de données Azure AD avec la syntaxe `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Cette fonctionnalité est également appelé utilisateurs de base de données Azure AD contenue.

- Les connexions Windows créées avec le `CREATE LOGIN ... FROM WINDOWS` syntaxe ne sont pas pris en charge. Utilisez des utilisateurs et des connexions Azure Active Directory.
- L’utilisateur Azure AD qui a créé l’instance a [privilèges d’administrateur illimités](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Les utilisateurs de niveau de base de données ne sont pas administrateur Azure AD peuvent être créés à l’aide de la `CREATE USER ... FROM EXTERNAL PROVIDER` syntaxe. Consultez [CREATE USER ... FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users).
- Principaux de serveur Azure AD (connexions) prend en charge les fonctionnalités SQL au sein d’une instance managée. Les fonctionnalités qui nécessitent une interaction entre les instances, peu importe si elles sont dans la même instance Azure AD locataire ou de différents clients, ne sont pas prises en charge pour les utilisateurs Azure AD. Il s’agit de fonctionnalités telles que :

  - Réplication transactionnelle SQL.
  - Serveur de lien.

- La définition d’une connexion Azure AD mappée à un groupe Azure AD en tant que propriétaire de base de données n’est pas prise en charge.
- L’emprunt d’identité des entités de sécurité Azure AD au niveau du serveur à l’aide d’autres principaux Azure AD est pris en charge, tels que le [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) clause. Limitations de EXECUTE AS sont :

  - EXECUTE AS USER n’est pas pris en charge pour les utilisateurs Azure AD lorsque le nom est différent du nom de connexion. Par exemple, lorsque l’utilisateur est créé via la syntaxe CREATE USER [myAadUser] FROM LOGIN [john@contoso.com] et l’emprunt d’identité est tentée via EXEC AS USER = _myAadUser_. Lorsque vous créez un **utilisateur** à partir d’un principal de serveur Azure AD (connexion), spécifiez le nom d’utilisateur en tant que le même login_name à partir de **connexion**.
  - Uniquement les niveau de SQL Server principaux (connexions) qui font partie de la `sysadmin` du rôle peuvent exécuter les opérations suivantes qui ciblent les principaux Azure AD :

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Limitations de la version préliminaire publique pour les principaux de serveur Azure AD (connexions) :

  - Limitations d’administrateur actives Directory pour Managed Instance :

    - L’administrateur Azure AD utilisé pour configurer l’instance gérée ne peut pas être utilisé pour créer un serveur Azure AD principal (connexion) au sein de l’instance gérée. Vous devez créer le premier Azure AD serveur principal (connexion) à l’aide d’un compte SQL Server qui est un `sysadmin` rôle. Cette limitation temporaire sera supprimée une fois que les principaux de serveur Azure AD (connexions) la disposition générale. Si vous essayez d’utiliser un compte d’administrateur Azure AD pour créer la connexion, vous consultez l’erreur suivante : `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - Actuellement, la première connexion d’Azure AD créée dans la base de données master doit être créée par le compte SQL Server standard (non-Azure AD) qui est un `sysadmin` rôle à l’aide de [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) à partir d’un fournisseur externe. Après la disponibilité générale, cette limitation sera supprimée. Vous pouvez ensuite créer une initiale connexion Azure Active Directory à l’aide de l’administrateur Active Directory pour Managed Instance.
    - DacFx (exportation/importation) utilisé avec SQL Server Management Studio ou SqlPackage n’est pas pris en charge pour les connexions d’Azure AD. Cette limitation sera supprimée une fois que les principaux de serveur Azure AD (connexions) la disposition générale.
    - À l’aide des principaux de serveur Azure AD (connexions) avec SQL Server Management Studio :

      - Script des connexions d’Azure AD qui utilisent toute connexion authentifiée n’est pas pris en charge.
      - IntelliSense ne reconnaît pas l’instruction de créer la connexion de fournisseur externe et affiche un trait de soulignement rouge.

- Seule au niveau du serveur de connexion principale, qui est créée par l’option Managed Instance tel que le processus, les membres des rôles de serveur, d’approvisionnement `securityadmin` ou `sysadmin`, ou d’autres connexions avec l’autorisation ALTER ANY LOGIN au niveau du serveur peuvent créer des application Azure AD principaux de serveur (connexions) dans la base de données master pour Managed Instance.
- Si la connexion est une entité de sécurité SQL, seules les connexions qui font partie de la `sysadmin` rôle peut utiliser la commande de création de connexions de créer un compte Azure AD.
- La connexion Azure Active Directory doit être membre d’un compte Azure AD dans le même répertoire que celui qui est utilisé pour Azure SQL Database Managed Instance.
- Principaux de serveur Azure AD (connexions) est visibles dans l’Explorateur d’objets à partir de SQL Server Management Studio 18.0 preview 5.
- Le chevauchement des principaux de serveur (connexions) Azure AD avec un compte d’administrateur Azure AD est autorisé. Principaux de serveur Azure AD (connexions) est prioritaires sur l’administrateur Azure AD lorsque vous résolvez le principal et appliquez des autorisations à l’instance gérée.
- Lors de l’authentification, la séquence suivante est appliquée pour résoudre le principal d’authentification :

    1. Si le compte Azure AD existe comme étant directement mappé vers le principal de serveur Azure AD (connexion), qui est présent dans sys.server_principals en tant que type « E », accorder l’accès et appliquer des autorisations du principal de serveur Azure AD (connexion).
    2. Si le compte Azure AD est un membre d’un groupe Azure AD qui est mappé à la principal de serveur Azure AD (connexion), qui est présent dans sys.server_principals comme type de « X », accorder l’accès et appliquer des autorisations de la connexion de groupe Azure AD.
    3. Si le compte Azure AD est un spécial configuré de portail administrateur Azure AD pour Managed Instance, ce qui n’existe pas dans les vues système de Managed Instance, appliquer des autorisations spéciales fixes de l’administrateur Azure AD pour Managed Instance (mode hérité).
    4. Si le compte Azure AD existe en tant que directement mappé à un utilisateur Azure AD dans une base de données, ce qui est présent dans sys.database_principals en tant que type « E », accorder l’accès et appliquer des autorisations de l’utilisateur de base de données Azure AD.
    5. Si le compte Azure AD est un membre d’un groupe Azure AD qui est mappé à un utilisateur Azure AD dans une base de données, ce qui est présent dans sys.database_principals en tant que type « X », accorder l’accès et appliquer des autorisations de la connexion de groupe Azure AD.
    6. S’il existe une connexion Azure Active Directory mappée à un compte d’utilisateur Azure AD ou un compte de groupe Azure AD, qui correspond à l’utilisateur qui s’authentifie, toutes les autorisations à partir de cette connexion d’Azure AD sont appliquées.

### <a name="service-key-and-service-master-key"></a>Clé de service et clé principale de service

- [Backup master key](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) n’est pas pris en charge (géré par le service de base de données SQL).
- [Restore master key](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) n’est pas pris en charge (géré par le service de base de données SQL).
- [Backup service master key](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) n’est pas pris en charge (géré par le service de base de données SQL).
- [Restore service master key](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) n’est pas pris en charge (géré par le service de base de données SQL).

## <a name="configuration"></a>Configuration

### <a name="buffer-pool-extension"></a>Extension du pool de mémoires tampons

- [Extension du pool de mémoires tampons](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) n’est pas pris en charge.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` n’est pas pris en charge. Consultez [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Collation

Le classement d’instance par défaut est `SQL_Latin1_General_CP1_CI_AS` et peut être spécifié comme paramètre de création. Consultez [Classements](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Niveaux de compatibilité

- Niveaux de compatibilité pris en charge sont 100, 110, 120, 130 et 140.
- Les niveaux de compatibilité inférieurs à 100 ne sont pas pris en charge.
- Le niveau de compatibilité par défaut est de 140 pour les nouvelles bases de données. Pour les bases de données restaurées, le niveau de compatibilité reste inchangé s’il s’agissait de 100 et versions ultérieures.

Consultez [Niveau de compatibilité ALTER TABLE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Mise en miroir de bases de données

La mise en miroir de bases de données n’est pas prise en charge.

- Les options `ALTER DATABASE SET PARTNER` et `SET WITNESS` ne sont pas prises en charge.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` n’est pas pris en charge.

Pour plus d’informations, consultez [ALTER DATABASE SET PARTNER AND SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) et [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Options de la base de données

- Les fichiers journaux multiples ne sont pas pris en charge.
- Les objets en mémoire ne sont pas pris en charge dans le niveau de service Usage général. 
- Il existe une limite de 280 fichiers par instance à usage général, ce qui implique un maximum de 280 fichiers par base de données. Les données et fichiers journaux dans le niveau usage général sont comptabilisés dans cette limite. [Le niveau critique pour l’entreprise prend en charge 32 767 fichiers par base de données](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- La base de données ne peut pas contenir de groupes de fichiers qui contiennent des données filestream. Restauration échoue si le fichier.bak contient `FILESTREAM` données. 
- Chaque fichier est placé dans Stockage Blob Azure. L’E/S et le débit par fichier dépendent de la taille de chaque fichier.

#### <a name="create-database-statement"></a>Instruction CREATE DATABASE

Les limitations suivantes s’appliquent à `CREATE DATABASE`:

- Les fichiers et les groupes de fichiers ne peuvent pas être définis. 
- Le `CONTAINMENT` option n’est pas prise en charge. 
- `WITH` options ne sont pas prises en charge. 
   > [!TIP]
   > Pour résoudre ce problème, utilisez `ALTER DATABASE` après `CREATE DATABASE` pour définir les options de base de données pour ajouter des fichiers ou pour définir la relation contenant-contenu. 

- Le `FOR ATTACH` option n’est pas prise en charge.
- Le `AS SNAPSHOT OF` option n’est pas prise en charge.

Pour plus d’informations, consultez [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instruction ALTER DATABASE

Certaines propriétés de fichiers ne peuvent pas être définies ou modifiées :

- Un chemin d’accès de fichier ne peut pas être spécifié dans le `ALTER DATABASE ADD FILE (FILENAME='path')` instruction T-SQL. Enlevez `FILENAME` du script car une instance gérée place les fichiers automatiquement. 
- Un nom de fichier ne peut pas être modifié à l’aide de la `ALTER DATABASE` instruction.

Les options suivantes sont définies par défaut et ne peuvent pas être modifiées :

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

Les options suivantes ne peuvent pas être modifiées :

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Pour en savoir plus, consultez [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>Agent SQL Server

- Paramètres de l’Agent SQL Server sont en lecture seule. La procédure `sp_set_agent_properties` n’est pas pris en charge dans Managed Instance. 
- Tâches
  - Les étapes de travail T-SQL sont prises en charge.
  - Les travaux de réplication suivants sont pris en charge :
    - Lecteur de journaux de transactions
    - Instantané
    - Serveur de distribution
  - Étapes de travail SSIS sont pris en charge.
  - Autres types d’étapes de travail ne sont pas actuellement pris en charge :
    - L’étape de travail de réplication de fusion n’est pas pris en charge. 
    - L’agent de lecture de la file d’attente n’est pas pris en charge. 
    - Interface de commande n’est pas encore pris en charge.
  - Les instances gérées ne peut pas accéder aux ressources externes, par exemple, de partages de réseau via robocopy. 
  - SQL Server Analysis Services ne sont pas pris en charge.
- Les notifications sont partiellement prises en charge.
- Notification par courrier électronique est prise en charge, bien que cela nécessite que vous configurez un profil de messagerie de base de données. L’Agent SQL Server peut utiliser qu’un seul profil de messagerie de base de données, et il doit être appelé `AzureManagedInstance_dbmail_profile`. 
  - Le récepteur d’appel n’est pas pris en charge. 
  - NetSend n’est pas pris en charge.
  - Les alertes ne sont pas encore pris en charge.
  - Serveurs proxy ne sont pas pris en charge. 
- Journal des événements n’est pas pris en charge.

Les fonctionnalités suivantes ne sont pas pris en charge actuellement mais seront activées à l’avenir :

- Proxies
- Planification des travaux sur une UC inactive
- Activation ou désactivation d’un Agent
- Alertes

Pour plus d’informations sur SQL Server Agent, consultez [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tables

Les tableaux suivants ne sont pas prises en charge :

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED` 

Pour plus d’informations sur la façon de créer et modifier des tables, consultez [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) et [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Fonctionnalités

### <a name="bulk-insert--openrowset"></a>Insertion en bloc/openrowset

Une instance gérée ne peut pas accéder aux partages de fichiers et dossiers Windows, afin des fichiers doivent être importés à partir du stockage d’objets Blob Azure :

- `DATASOURCE` est requis dans le `BULK INSERT` commande lors de l’importation de fichiers à partir du stockage d’objets Blob Azure. Consultez [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` est requis dans le `OPENROWSET` fonctionner lorsque vous lisez le contenu d’un fichier à partir du stockage d’objets Blob Azure. Consultez [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR

Une instance gérée ne peut pas accéder aux partages de fichiers et dossiers Windows, donc les contraintes suivantes s’appliquent :

- Seul `CREATE ASSEMBLY FROM BINARY` est pris en charge. Consultez [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` n’est pas pris en charge. Consultez [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` ne peut pas référencer des fichiers. Consultez [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

Les instructions DBCC non documentées qui sont activées dans SQL Server ne sont pas pris en charge dans les instances gérées.

- Les `Trace flags` ne sont pas pris en charge. Consultez [indicateurs de Trace](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` n’est pas pris en charge. Consultez [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` n’est pas pris en charge. Consultez [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Transactions distribuées

MSDTC et [transactions élastiques](sql-database-elastic-transactions-overview.md) actuellement ne sont pas pris en charge dans les instances gérées.

### <a name="extended-events"></a>Événements étendus

Certaines cibles spécifiques de Windows pour les événements étendus (XEvents) ne sont pas prises en charge :

- Le `etw_classic_sync` cible n’est pas pris en charge. Store `.xel` fichiers dans le stockage Blob Azure. Consultez [etw_classic_sync target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Le `event_file` cible n’est pas pris en charge. Store `.xel` fichiers dans le stockage Blob Azure. Consultez [event_file target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Bibliothèques externes

Dans la base de données R et Python, les bibliothèques externes ne sont pas encore prises en charge. Consultez [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream et FileTable

- les données FileStream n’est pas pris en charge.
- La base de données ne peut pas contenir de groupes de fichiers avec `FILESTREAM` données.
- `FILETABLE` n’est pas pris en charge.
- Les tables ne peuvent pas avoir de types `FILESTREAM`.
- Les fonctions suivantes ne sont pas prises en charge :
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Pour plus d’informations, consultez [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) et [FileTables](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Recherche sémantique de texte intégral

La [recherche sémantique](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) n’est pas prise en charge.

### <a name="linked-servers"></a>Services liés

Les serveurs liés dans des instances managées prennent en charge un nombre limité de cibles :

- Cibles prises en charge sont SQL Server et la base de données SQL.
- Les cibles qui ne sont pas pris en charge sont les fichiers, Analysis Services et autres SGBDR.

Opérations

- Les transactions d’écriture entre instances ne sont pas prises en charge.
- `sp_dropserver` est pris en charge pour supprimer un serveur lié. Consultez [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- Le `OPENROWSET` fonction peut être utilisée pour exécuter des requêtes uniquement sur les instances de SQL Server. Ils peuvent être gérés, en local, ou dans des machines virtuelles. Consultez [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- Le `OPENDATASOURCE` fonction peut être utilisée pour exécuter des requêtes uniquement sur les instances de SQL Server. Ils peuvent être gérés, en local, ou dans des machines virtuelles. Uniquement les `SQLNCLI`, `SQLNCLI11`, et `SQLOLEDB` valeurs sont prises en charge en tant que fournisseur. Par exemple `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Consultez [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>PolyBase

Les tables externes qui référencent que les fichiers dans HDFS ou stockage Blob Azure ne sont pas pris en charge. Pour plus d’informations sur PolyBase, consultez [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Réplication

Une réplication est disponible en préversion publique pour Managed Instance. Pour plus d’informations sur la réplication, consultez [réplication SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).

### <a name="restore-statement"></a>L’instruction RESTORE 

- Syntaxe prise en charge :
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Syntaxe non pris en charge :
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Source : 
  - `FROM URL` (Stockage Blob azure) est la seule option prise en charge.
  - L’unité de sauvegarde/`FROM DISK`/`TAPE` n’est pas prise en charge.
  - Les jeux de sauvegarde ne sont pas pris en charge.
- `WITH` options ne sont pas prises en charge, tels que non `DIFFERENTIAL` ou `STATS`.
- `ASYNC RESTORE`: Restauration continue même si la connexion cliente s’arrête. Si votre connexion est supprimée, vous pouvez vérifier le `sys.dm_operation_status` vue de l’état d’une opération de restauration, ainsi que pour une base de données CREATE et DROP. Consultez [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Les options de base de données suivantes sont définies ou substitution et ne peut pas être modifié ultérieurement : 

- `NEW_BROKER` Si le service broker n’est pas activé dans le fichier .bak. 
- `ENABLE_BROKER` Si le service broker n’est pas activé dans le fichier .bak. 
- `AUTO_CLOSE=OFF` Si une base de données dans le fichier .bak a `AUTO_CLOSE=ON`. 
- `RECOVERY FULL` Si une base de données dans le fichier .bak a `SIMPLE` ou `BULK_LOGGED` en mode de récupération.
- Un groupe de fichiers mémoire optimisé est ajouté et appelé XTP s’il n’était pas dans le fichier .bak source. 
- N’importe quel filegroup optimisé en mémoire existant est renommé XTP. 
- `SINGLE_USER` et `RESTRICTED_USER` options sont converties en `MULTI_USER`.

Limites : 

- `.BAK` Impossible de restaurer les fichiers qui contiennent plusieurs jeux de sauvegarde. 
- `.BAK` Impossible de restaurer les fichiers qui contiennent plusieurs fichiers journaux.
- Restauration échoue si le fichier.bak contient `FILESTREAM` données.
- Impossible de restaurer les sauvegardes qui contiennent des bases de données qui ont des objets en mémoire actives sur une instance à usage général. Pour plus d’informations sur les instructions de restauration, consultez [instructions RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Service broker

Le Service Broker entre instances n’est pas pris en charge :

- `sys.routes`: Comme condition préalable, vous devez sélectionner l’adresse à partir de sys.routes. L’adresse doit être LOCAL sur tous les itinéraires. Consultez [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Vous ne pouvez pas utiliser `CREATE ROUTE` avec `ADDRESS` autre que `LOCAL`. Consultez [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Vous ne pouvez pas utiliser `ALTER ROUTE` avec `ADDRESS` autre que `LOCAL`. Consultez [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Déclencheurs, fonctions et procédures stockées

- `NATIVE_COMPILATION` n’est pas pris en charge dans le niveau usage général.
- Les options [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) suivantes ne sont pas prises en charge : 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` n’est pas pris en charge. Consultez [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` n’est pas pris en charge. Consultez [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` ne sont pas pris en charge, qui inclut `sp_addextendedproc`  et `sp_dropextendedproc`. Consultez [les procédures stockées étendues](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` et `sp_detach_db` ne sont pas pris en charge. Consultez [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) et [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Changes"></a> Changements de comportement

Les variables, fonctions et vues suivantes retournent des résultats différents :

- `SERVERPROPERTY('EngineEdition')` Retourne la valeur 8. Cette propriété identifie une instance gérée de façon unique. Consultez [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` Retourne la valeur NULL, car le concept d’instance tel qu’il existe de SQL Server ne s’applique à une instance gérée. Consultez [SERVERPROPERTY(« InstanceName »)](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Retourne un nom de « connectable » complet DNS, par exemple, my-managed-instance.wcus17662feb9ce98.database.windows.net. Consultez [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` Retourne un nom de « connectable » complet DNS, tels que `myinstance.domain.database.windows.net` propriétés « nom » et « data_source ». Consultez [SYS. SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` Retourne la valeur NULL, car le concept de service tel qu’il existe de SQL Server ne s’applique à une instance gérée. Consultez [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` est pris en charge. Elle retourne NULL si la connexion Azure Active Directory n’est pas dans sys.syslogins. Consultez [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` n’est pas pris en charge. Données incorrect sont retournées, qui est une table temporaire problème connu. Consultez [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Issues"></a> Problèmes connus et limitations

### <a name="tempdb-size"></a>Taille de TEMPDB

La taille de fichier maximale de `tempdb` ne peut pas être supérieure à 24 Go par cœur sur un niveau usage général. La valeur maximale `tempdb` taille sur un niveau critique pour l’entreprise est limitée avec la taille de stockage d’instance. Le `tempdb` base de données est toujours Fractionner en fichiers de 12 données. Cette taille maximale par fichier ne peut pas être modifiée, et les nouveaux fichiers ne peut pas être ajoutés à `tempdb`. Certaines requêtes peuvent retourner une erreur s’ils ont besoin de plus de 24 Go par cœur dans `tempdb`. `tempdb` est toujours recréée en tant que base de données vide lorsque le démarrage de l’instance ou de basculement et les modifier faite dans `tempdb` ne sera pas préservé. 

### <a name="cant-restore-contained-database"></a>Impossible de restaurer le contenu de la base de données

Instance gérée ne peut pas restaurer [bases de données autonomes](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Restauration de point-à-temps des bases de données en relation contenant-contenus existants ne fonctionne pas sur Managed Instance. Ce problème sera résolu prochainement. En attendant, nous vous recommandons de supprimer l’option de relation contenant-contenu à partir de vos bases de données qui sont placés sur Managed Instance. N’utilisez pas l’option de relation contenant-contenu pour les bases de données de production. 

### <a name="exceeding-storage-space-with-small-database-files"></a>Dépassement de l’espace de stockage avec des fichiers de base de données de petite taille

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`, et `RESTORE DATABASE` instructions risque d’échouer car l’instance peut atteindre la limite de stockage Azure.

Chaque instance gérée à usage général a jusqu'à 35 To de stockage réservé pour l’espace disque Premium Azure. Chaque fichier de base de données est placé sur un disque physique distinct. Les tailles de disque peuvent être de 128 Go, 256 Go, 512 Go, 1 To ou 4 To. Espace disponible sur le disque n’est pas facturé, mais la somme totale des tailles de disque Premium de Azure ne peut pas dépasser 35 To. Dans certains cas, une instance gérée qui n’a pas besoin de 8 To au total peut dépasser la limite de 35 To Azure sur taille de stockage en raison de la fragmentation interne.

Par exemple, une instance gérée à usage général peut avoir un fichier qui est de 1,2 To taille placé sur un disque de 4 To. Il peut également avoir 248 fichiers chaque de 1 Go qui sont placés sur des disques distincts de 128 Go. Dans cet exemple :

- La taille totale du stockage de disque alloué est de 1 x 4 To + 248 x 128 Go = 35 To.
- L’espace total réservé pour les bases de données sur l’instance est de 1 x 1,2 To + 248 x 1 Go = 1,4 To.

Cet exemple illustre que dans certaines circonstances, en raison d’une distribution spécifique de fichiers, une instance gérée peut atteindre la limite de 35 To n’est réservée pour un disque Premium Azure lorsque vous ne l’espériez pouvoir.

Dans cet exemple, les bases de données existantes continuent de fonctionner et peuvent croître sans aucun problème tant que nouveaux fichiers ne sont pas ajoutés. Nouvelles bases de données ne peut pas être créés ou restaurées, car il n’est pas suffisamment d’espace pour les nouveaux lecteurs de disque, même si la taille totale de toutes les bases de données n’atteint pas la limite de taille d’instance. L’erreur est retournée dans ce cas n’est pas clair.

Vous pouvez [identifier le nombre de fichiers restants](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) à l’aide de vues système. Si vous atteignez cette limite, essayez de [vides et de supprimer certains des fichiers plus petits à l’aide de l’instruction DBCC SHRINKFILE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) ou basculez vers le [niveau critique pour l’entreprise, ce qui n’a pas cette limite](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="incorrect-configuration-of-the-sas-key-during-database-restore"></a>Restaurer une configuration incorrecte de la clé SAP au cours de la base de données

`RESTORE DATABASE` qui lit le fichier .bak peut être réessaie constamment de lire le .bak les fichier et renvoie une erreur après une longue période de temps si la signature d’accès partagé dans `CREDENTIAL` est incorrect. Exécutez RESTORE HEADERONLY avant de restaurer une base de données pour être sûr que la clé SAS est correcte.
Assurez-vous que vous supprimez le caractère de début `?` à partir de la clé SAS qui est générée à l’aide du portail Azure.

### <a name="tooling"></a>Outils

SQL Server Management Studio et SQL Server Data Tools peuvent rencontrer des problèmes alors qu’ils accèdent à une instance gérée.

- Principaux de serveur Azure AD (connexions) et les utilisateurs (préversion publique) avec SQL Server Data Tools actuellement n’est pas pris en charge.
- Écriture de scripts pour les principaux de serveur Azure AD (connexions) et les utilisateurs (préversion publique) n’est pas pris en charge dans SQL Server Management Studio.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Les noms des bases de données sont incorrects dans plusieurs vues, journaux d’activité et messages

Plusieurs vues système, compteurs de performances, messages d’erreur, événements XEvent et entrées du journal des erreurs affichent des identificateurs de base de données GUID au lieu d’afficher les noms des bases de données. Ne vous fiez ces identificateurs GUID, car elle soient remplacées avec des noms de base de données à l’avenir.

### <a name="database-mail"></a>Messagerie de base de données

Le `@query` paramètre dans le [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) procédure ne fonctionne pas.

### <a name="database-mail-profile"></a>Profil de messagerie de base de données

Le profil de messagerie de base de données utilisé par l’Agent SQL Server doit être appelé `AzureManagedInstance_dbmail_profile`. Il n’existe aucune restriction pour les autres noms de profil de messagerie de base de données.

### <a name="error-logs-arent-persisted"></a>Journaux d’erreurs ne sont pas conservées.

Journaux d’erreurs qui sont disponibles dans Managed Instance ne sont pas conservées, et leur taille n’est pas incluse dans la limite maximale de stockage. Journaux d’erreurs peuvent être automatiquement effacées en cas de basculement.

### <a name="error-logs-are-verbose"></a>Les journaux d’activité des erreurs contiennent des détails non pertinents

Une instance gérée place les informations détaillées dans les journaux d’erreurs et une grande partie de celui-ci ne s’applique pas. Diminue la quantité d’informations dans les journaux d’erreurs à l’avenir.

**Solution de contournement :** Utiliser une procédure personnalisée pour lire les journaux d’erreurs qui exclut certaines entrées non pertinentes. Pour plus d’informations, consultez [Managed Instance – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Étendue de transaction sur deux bases de données dans la même instance n’est pas pris en charge

Le `TransactionScope` classe dans .NET ne fonctionne pas si les deux requêtes sont envoyées aux deux bases de données dans la même instance sous la même étendue de transaction :

```C#
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

Bien que ce code fonctionne avec les données dans la même instance, il nécessaire de MSDTC.

**Solution de contournement :** Utilisez [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) à utiliser une autre base de données dans un contexte de connexion au lieu d’utiliser deux connexions.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Parfois, les serveurs liés et modules CLR ne peut pas référencer une adresse IP locale

Modules CLR placés dans une instance gérée et des serveurs liés ou des requêtes distribuées qui font parfois référencent à une instance en cours ne peut pas résoudre l’adresse IP d’une instance locale. Cette erreur est un problème temporaire.

**Solution de contournement :** Utilisez si possible les connexions de contexte dans un module CLR.

### <a name="tde-encrypted-databases-with-a-service-managed-key-dont-support-user-initiated-backups"></a>Chiffrement transparent des données chiffrées de bases de données avec une clé gérée par le service ne prennent pas en charge les sauvegardes lancées par l’utilisateur

Vous ne pouvez pas exécuter `BACKUP DATABASE ... WITH COPY_ONLY` sur une base de données est chiffrée avec géré par le service Transparent Data Encryption (TDE). TDE géré par le service force des sauvegardes est chiffré avec une clé de chiffrement transparent des données interne. La clé ne peut pas être exportée, afin que vous ne pouvez pas restaurer la sauvegarde.

**Solution de contournement :** Utilisez des sauvegardes automatiques et restauration de point-à-temps ou [gérée par le client (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) à la place. Vous pouvez également désactiver le chiffrement sur la base de données.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les instances gérées, consultez [ce qui est une instance gérée ?](sql-database-managed-instance.md)
- Pour une fonctionnalités liste et de comparaison, consultez [comparaison des fonctionnalités de base de données SQL Azure](sql-database-features.md).
- Pour un démarrage rapide qui vous montre comment créer une nouvelle instance managée, consultez [créer une instance gérée](sql-database-managed-instance-get-started.md).
