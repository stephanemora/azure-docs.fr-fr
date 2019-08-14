---
title: Différences T-SQL avec Azure SQL Database Managed Instance | Microsoft Docs
description: Cet article décrit les différences T-SQL entre une instance managée dans Azure SQL Database et dans SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
ms.date: 07/07/2019
ms.custom: seoapril2019
ms.openlocfilehash: fd029c1e7b67d308e3e1fdbedbdc90ea430b4f5b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567246"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Différences T-SQL entre Azure SQL Database Managed Instance et SQL Server

Cet article résume et explique les différences de syntaxe et de comportement existant entre Azure SQL Database Managed Instance et le moteur de base de données SQL Server local. Les sujets suivants sont abordés : <a name="Differences"></a>

- la [disponibilité](#availability) incluant les différences dans [Always On](#always-on-availability) et les [sauvegardes](#backup) ;
- la [sécurité](#security) incluant les différences dans l’[audit](#auditing), les [certificats](#certificates), les [informations d’identification](#credential), les [fournisseurs de chiffrement](#cryptographic-providers), les [connexions et les utilisateurs](#logins-and-users) ainsi que la [clé de service et la clé principale du service](#service-key-and-service-master-key) ;
- la [configuration](#configuration) incluant les différences dans l’[extension du pool de mémoires tampons](#buffer-pool-extension), le [classement](#collation), les [niveaux de compatibilité](#compatibility-levels), la [mise en miroir de bases de données](#database-mirroring), les [options de base de données](#database-options), le service [SQL Server Agent](#sql-server-agent) et les [options de Table](#tables) ;
- les [fonctionnalités](#functionalities) incluant [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), les [transactions distribuées](#distributed-transactions), les [événements étendus](#extended-events), les [bibliothèques externes](#external-libraries), le [flux de fichier et FileTable](#filestream-and-filetable), la [recherche sémantique de texte intégral](#full-text-semantic-search), les [serveurs liés](#linked-servers), [Polybase](#polybase), la [réplication](#replication), [RESTORE](#restore-statement), [Service Broker](#service-broker), les [procédures stockées, fonctions et déclencheurs](#stored-procedures-functions-and-triggers) ;
- les [paramètres d’environnement](#Environment), tels que les configurations de réseau virtuel et de sous-réseau ;
- les [fonctionnalités qui se comportent différemment dans les instances managées](#Changes) ;
- les [limitations temporaires et les problèmes connus](#Issues).

L’option de déploiement Managed Instance est hautement compatible avec le moteur de base de données SQL Server local. La plupart des fonctionnalités du moteur de base de données SQL Server sont prises en charge dans une instance gérée.

![Migration](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>Disponibilité

### <a name="always-on-availability"></a>Always On

La [haute disponibilité](sql-database-high-availability.md) étant intégrée à Managed Instance, les utilisateurs ne peuvent pas la contrôler. Les instructions suivantes ne sont pas prises en charge :

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- La clause [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) de l’instruction [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Sauvegarde

Les instances managées étant dotées de sauvegardes automatiques, les utilisateurs peuvent créer des sauvegardes complètes `COPY_ONLY` de bases de données. Les sauvegardes différentielles de fichiers journaux et de captures instantanées de fichiers ne sont pas prises en charge.

- Avec une instance managée, vous pouvez sauvegarder une base de données d’instance seulemet vers un compte de stockage Blob Azure :
  - Seul `BACKUP TO URL` est pris en charge.
  - `FILE`, `TAPE` et les unités de sauvegarde ne sont pas pris en charge.
- La plupart des options générales `WITH` sont prises en charge.
  - `COPY_ONLY` est obligatoire.
  - `FILE_SNAPSHOT` n’est pas pris en charge.
  - Options de bande : `REWIND`, `NOREWIND`, `UNLOAD` et `NOUNLOAD` ne sont pas pris en charge.
  - Options propres au journal : `NORECOVERY`, `STANDBY` et `NO_TRUNCATE` ne sont pas pris en charge.

Limites : 

- Une instance managée permet de sauvegarder une base de données d’instance vers une sauvegarde comprenant jusqu’à 32 bandes, ce qui est suffisant pour des bases de données allant jusqu’à 4 To si la compression de sauvegarde est utilisée.
- La taille maximale de la bande de sauvegarde en utilisant la commande `BACKUP` dans une instance managée est de 195 Go, ce qui représente la taille maximale des objets blob. Augmentez le nombre de bandes dans la commande de sauvegarde pour réduire la taille de bande individuelle et ne pas dépasser cette limite.

    > [!TIP]
    > Pour contourner cette limitation, lorsque vous sauvegardez une base de données, soit à partir de SQL Server dans un environnement local, soit sur une machine virtuelle, vous pouvez :
    >
    > - sauvegarder sur `DISK` au lieu de sauvegarder jusqu’à `URL` ;
    > - charger les fichiers de sauvegarde sur le stockage d’objets blob ;
    > - restaurer dans l’instance managée.
    >
    > La commande `Restore` dans une instance managée prend en charge des tailles d’objet blob plus volumineuses dans les fichiers de sauvegarde, car un autre type d’objet blob est utilisé pour le stockage des fichiers de sauvegarde chargés.

Pour plus d’informations sur les sauvegardes à l’aide de T-SQL, consultez [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Sécurité

### <a name="auditing"></a>Audit

Les principales différences entre l’audit des bases de données dans Azure SQL Database et des bases de données dans SQL Server sont les suivantes :

- Avec l’option de déploiement Managed Instance dans Azure SQL Database, l’audit s’effectue au niveau du serveur. Les fichiers journaux `.xel` sont stockés dans le stockage Blob Azure.
- Avec les options de déploiement de base de données unique et de pool élastique dans Azure SQL Database, l’audit fonctionne au niveau de la base de données.
- Dans SQL Server en local ou sur machines virtuelles, l’audit s’effectue au niveau serveur. Les événements sont stockés dans le système de fichiers ou dans les journaux des événements Windows.
 
L’audit XEvent dans Managed Instance prend en charge les cibles de Stockage Blob Azure. Les journaux de fichiers et de Windows ne sont pas pris en charge.

Les principales différences de syntaxe `CREATE AUDIT` pour l’audit du Stockage Blob Azure sont :

- Une nouvelle syntaxe `TO URL` est fournie, vous pouvez l’utiliser pour spécifier l’URL du conteneur de stockage Blob Azure où les fichiers `.xel` sont placés.
- La syntaxe `TO FILE` n’est pas prise en charge, car une instance managée ne peut pas accéder à des partages de fichiers Windows.

Pour plus d'informations, consultez les pages suivantes : 

- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Audit](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificats

Une instance managée ne pouvant pas accéder à des partages de fichiers et à des dossiers Windows, les contraintes suivantes s’appliquent :

- le fichier `CREATE FROM`/`BACKUP TO` n’est pas pris en charge pour les certificats ;
- le certificat `CREATE`/`BACKUP` de `FILE`/`ASSEMBLY` n’est pas pris en charge ; Les fichiers de clés privés ne peuvent pas être utilisés 

Consultez [CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) et [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Solution de contournement** : ajoutez le certificat ou la clé privée dans un script, stockez-le dans un fichier .sql et créez-le à partir du fichier binaire :

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Informations d'identification

Seuls Azure Key Vault et les identités `SHARED ACCESS SIGNATURE` sont pris en charge. Les utilisateurs de Windows ne sont pas pris en charge.

Consultez [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) et [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Fournisseurs de chiffrement

Une instance managée ne pouvant pas accéder aux fichiers, vous ne pouvez pas créer de fournisseur de chiffrement :

- `CREATE CRYPTOGRAPHIC PROVIDER` n’est pas pris en charge. Consultez [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` n’est pas pris en charge. Consultez [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Connexions et utilisateurs

- Les connexions SQL créées à l’aide de `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY` et `FROM SID` sont prises en charge. Consultez [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Les principaux de serveur (connexions) Azure Active Directory (Azure AD) créés avec la syntaxe [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) ou la syntaxe [CREATE USER FROM LOGIN [Azure AD Login]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) sont pris en charge (préversion publique). Ces connexions sont créées au niveau du serveur.

    Managed Instance prend en charge les principaux de base de données Azure AD avec la syntaxe `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Cette fonctionnalité est également appelée « utilisateurs de base de données autonome Azure AD ».

- Les connexions Windows créées avec la syntaxe `CREATE LOGIN ... FROM WINDOWS` ne sont pas prises en charge. Utilisez des utilisateurs et des connexions Azure Active Directory.
- L’utilisateur Azure AD qui a créé l’instance dispose de [privilèges d’administrateur illimités](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Les utilisateurs au niveau de la base de données Azure AD non-administrateurs peuvent être créés à l’aide de la syntaxe `CREATE USER ... FROM EXTERNAL PROVIDER`. Consultez [CREATE USER ... FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users).
- Les principaux de serveur (connexions) Azure AD prennent en charge les fonctionnalités SQL dans une seule instance managée. Les fonctionnalités nécessitant une interaction entre les instances, peu importe qu’elles se trouvent au sein du même locataire Azure AD ou de locataires différents, ne sont pas prises en charge pour les utilisateurs Azure AD. Il s’agit de fonctionnalités telles que :

  - la réplication transactionnelle SQL ;
  - le serveur de liaisons.

- La définition d’une connexion Azure AD mappée à un groupe Azure AD en tant que propriétaire de base de données n’est pas prise en charge.
- L’emprunt d’identité des principaux au niveau du serveur Azure AD à l’aide d’autres principaux Azure AD est pris en charge, par exemple avec la clause [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql). Les limitations EXECUTE AS sont les suivantes :

  - EXECUTE AS USER n’est pas pris en charge pour les utilisateurs Azure AD quand le nom diffère du nom de connexion. Par exemple, quand l’utilisateur est créé par le biais de la syntaxe CREATE USER [myAadUser] FROM LOGIN [john@contoso.com] et que l’emprunt d’identité est tenté par le biais de EXEC AS USER = _myAadUser_. Quand vous créez un **USER** à partir d’un principal de serveur (connexion) Azure AD, spécifiez le même user_name que le login_name à partir de **LOGIN**.
  - Seuls les principaux au niveau de l’instance SQL Server (connexions) faisant partie du rôle `sysadmin` peuvent exécuter les opérations suivantes qui ciblent les principaux Azure AD :

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Limitations de la préversion publique pour les principaux de serveur (connexions) Azure AD :

  - Limitations concernant l’administration Active Directory pour Managed Instance :

    - Le compte Administrateur Azure AD utilisé pour configurer l’instance managée ne peut pas être utilisé pour créer un principal de serveur (connexion) Azure AD au sein de l’instance managée. Vous devez créer le premier principal de serveur (connexion) Azure AD à l’aide d’un compte SQL Server qui soit un rôle `sysadmin`. Cette limitation temporaire sera levée après la mise en disponibilité générale des principaux de serveur (connexions) Azure AD. Si vous essayez d’utiliser un compte Administrateur Azure AD pour créer la connexion, l’erreur suivante s’affiche : `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - Actuellement, la première connexion Azure AD créée dans la base de données master doit être créée par le compte SQL Server standard (non Azure AD) qui est un rôle `sysadmin` à l’aide de [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) FROM EXTERNAL PROVIDER. Après la disponibilité générale, cette limitation sera supprimée. Vous pouvez ensuite créer une connexion Azure AD initiale à l’aide de l’administrateur Active Directory pour Managed Instance.
    - DacFx (exportation/importation) utilisé avec SQL Server Management Studio ou SqlPackage n’est pas pris en charge pour les connexions Azure AD. Cette limitation sera levée après la mise en disponibilité générale des principaux de serveur (connexions) Azure AD.
    - Utilisation de principaux de serveur (connexions) Azure AD avec SQL Server Management Studio :

      - La création de scripts de connexions Azure AD qui utilisent une connexion authentifiée n’est pas prise en charge.
      - IntelliSense ne reconnaît pas l’instruction CREATE LOGIN FROM EXTERNAL PROVIDER et affiche un trait de soulignement rouge.

- Seule la connexion du principal au niveau du serveur, qui est créée par le processus de provisionnement de Managed Instance, les membres des rôles de serveur, tels que `securityadmin` ou `sysadmin`, ou d’autres connexions disposant de l’autorisation ALTER ANY LOGIN au niveau du serveur peuvent créer les principaux de serveur (connexions) Azure AD dans la base de données master pour Managed Instance.
- Si la connexion est un principal SQL, seules les connexions faisant partie du rôle `sysadmin` peuvent utiliser la commande create pour créer les connexions d’un compte Azure AD.
- La connexion Azure AD doit être membre d’un compte Azure AD dans le même annuaire que celui utilisé pour Azure SQL Database Managed Instance.
- Les principaux de serveur (connexions) Azure AD sont visibles dans l’Explorateur d’objets à compter de SQL Server Management Studio 18.0 préversion 5.
- Le chevauchement des principaux de serveur (connexions) Azure AD avec un compte d’administrateur Azure AD est autorisé. Les principaux de serveur (connexions) Azure AD sont prioritaires par rapport à l’Administrateur Azure AD lorsque vous résolvez le principal et que vous appliquez des autorisations à l’instance managée.
- Lors de l’authentification, la séquence suivante est appliquée pour résoudre le principal d’authentification :

    1. Si le compte Azure AD existe en tant que mappage direct au principal de serveur (connexion) Azure AD, qui est présent dans sys.server_principals en tant que type « E », l’accès est accordé et les autorisations du principal de serveur (connexion) Azure AD sont appliquées.
    2. Si le compte Azure AD est membre d’un groupe Azure AD mappé au principal de serveur (connexion) Azure AD, qui est présent dans sys.server_principals en tant que type « X », l’accès est accordé et les autorisations de la connexion du groupe Azure AD sont appliquées.
    3. Si le compte Azure AD est un administrateur Azure AD spécial configuré dans le portail pour Managed Instance, et qui n’existe pas dans les vues système de Managed Instance, les autorisations fixes spéciales de l’administrateur Azure AD pour Managed Instance (mode hérité) sont appliquées.
    4. Si le compte Azure AD existe en tant que mappage direct à un utilisateur Azure AD dans une base de données, qui est présent dans sys.database_principals en tant que type « E », l’accès est accordé et les autorisations de l’utilisateur de base de données Azure AD sont appliquées.
    5. Si le compte Azure AD est membre d’un groupe Azure AD mappé à un utilisateur Azure AD dans une base de données, qui est présent dans sys.database_principals en tant que type « X », l’accès est accordé et les autorisations de la connexion du groupe Azure AD sont appliquées.
    6. S’il existe une connexion Azure AD mappée à un compte d’utilisateur Azure AD ou à un compte de groupe Azure AD, qui se résout sur l’utilisateur qui s’authentifie, toutes les autorisations de cette connexion Azure AD sont appliquées.

### <a name="service-key-and-service-master-key"></a>Clé de service et clé principale de service

- [Backup master key](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) n’est pas pris en charge (géré par le service SQL Database).
- [Restore master key](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) n’est pas pris en charge (géré par le service SQL Database).
- [Backup service master key](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) n’est pas pris en charge (géré par le service SQL Database).
- [Restore service master key](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) n’est pas pris en charge (géré par le service SQL Database).

## <a name="configuration"></a>Configuration

### <a name="buffer-pool-extension"></a>Extension du pool de mémoires tampons

- L’[extension du pool de mémoires tampons](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) n’est pas prise en charge.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` n’est pas pris en charge. Consultez [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Collation

Le classement d’instance par défaut est `SQL_Latin1_General_CP1_CI_AS` et peut être spécifié comme paramètre de création. Consultez [Classements](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Niveaux de compatibilité

- Les niveaux de compatibilité pris en charge sont 100, 110, 120, 130, and 140.
- Les niveaux de compatibilité inférieurs à 100 ne sont pas pris en charge.
- Le niveau de compatibilité par défaut est de 140 pour les nouvelles bases de données. Pour les bases de données restaurées, le niveau de compatibilité reste inchangé s’il était de 100 et plus.

Consultez [Niveau de compatibilité ALTER TABLE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Mise en miroir de bases de données

La mise en miroir de bases de données n’est pas prise en charge.

- Les options `ALTER DATABASE SET PARTNER` et `SET WITNESS` ne sont pas prises en charge.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` n’est pas pris en charge.

Pour plus d’informations, consultez [ALTER DATABASE SET PARTNER AND SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) et [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Options de la base de données

- Les fichiers journaux multiples ne sont pas pris en charge.
- Les objets en mémoire ne sont pas pris en charge dans le niveau de service Usage général. 
- Il existe une limite de 280 fichiers par instance Usage général, ce qui implique un maximum de 280 fichiers par base de données. Les fichiers de données et de journaux du niveau Usage général sont tous les deux comptabilisés dans cette limite. [Le niveau Critique pour l’entreprise prend en charge 32 767 fichiers par base de données](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- La base de données ne peut pas contenir de groupes de fichiers qui contiennent des données flux de fichier. La restauration échoue si le fichier.bak contient des données `FILESTREAM`. 
- Chaque fichier est placé dans Stockage Blob Azure. L’E/S et le débit par fichier dépendent de la taille de chaque fichier.

#### <a name="create-database-statement"></a>Instruction CREATE DATABASE

Les limites suivantes s’appliquent à `CREATE DATABASE` :

- Les fichiers et les groupes de fichiers ne peuvent pas être définis. 
- L’option `CONTAINMENT` n’est pas prise en charge. 
- Les options `WITH` ne sont pas prises en charge. 
   > [!TIP]
   > Comme solution de contournement, utilisez `ALTER DATABASE` après `CREATE DATABASE` pour définir les options de la base de données de manière à ajouter des fichiers ou pour définir l’autonomie. 

- L’option `FOR ATTACH` n’est pas prise en charge.
- L’option `AS SNAPSHOT OF` n’est pas prise en charge.

Pour plus d’informations, consultez [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instruction ALTER DATABASE

Certaines propriétés de fichiers ne peuvent pas être définies ou modifiées :

- Un chemin de fichier ne peut pas être spécifié dans l’instruction T-SQL `ALTER DATABASE ADD FILE (FILENAME='path')`. Enlevez `FILENAME` du script car une instance gérée place les fichiers automatiquement. 
- Un nom de fichier ne peut pas être modifié à l’aide de l’instruction `ALTER DATABASE`.

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

- L’activation et la désactivation de SQL Server Agent ne sont actuellement pas prises en charge dans Managed Instance. SQL Agent est toujours en cours d’exécution.
- Les paramètres de SQL Server Agent sont en lecture seule. La procédure `sp_set_agent_properties` n’est pas prise en charge dans Managed Instance. 
- Tâches
  - Les étapes de travail T-SQL sont prises en charge.
  - Les travaux de réplication suivants sont pris en charge :
    - Lecteur de journaux de transactions
    - Instantané
    - Serveur de distribution
  - Les étapes de travail SSIS sont prises en charge.
  - Les autres types d’étapes de travail ne sont pas pris en charge actuellement :
    - L’étape de travail de réplication de fusion n’est pas prise en charge. 
    - L’agent de lecture de la file d’attente n’est pas pris en charge. 
    - L’interpréteur de commandes n’est pas encore pris en charge.
  - Les instances managées ne peuvent pas accéder à des ressources externes, par exemple des partages réseau via robocopy. 
  - SQL Server Analysis Services n’est pas pris en charge.
- Les notifications sont partiellement prises en charge.
- Les notifications par e-mail sont prises en charge bien qu’elles nécessitent de votre part la configuration d’un profil Database Mail. SQL Server Agent ne peut utiliser qu’un seul profil Database Mail, et il doit être appelé `AzureManagedInstance_dbmail_profile`. 
  - Le récepteur d’appel n’est pas pris en charge.
  - NetSend n’est pas pris en charge.
  - Les alertes ne sont pas encore prises en charge.
  - Les proxies ne sont pas pris en charge.
- EventLog n’est pas pris en charge.

Les fonctionnalités suivantes de l’agent SQL ne sont pas prises en charge actuellement :

- Proxies
- Planification de travaux sur une UC inactive
- Activation ou désactivation d’un Agent
- Alertes

Pour plus d’informations sur SQL Server Agent, consultez [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tables

Les tables suivantes ne sont pas prises en charge :

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED` 

Pour plus d’informations sur la façon de créer et de modifier des tables, consultez [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) et [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Fonctionnalités

### <a name="bulk-insert--openrowset"></a>Insertion en bloc/openrowset

Une instance managée ne pouvant pas accéder à des partages de fichiers et à des dossiers Windows, les fichiers doivent être importés à partir du stockage Blob Azure :

- `DATASOURCE` est requis dans la commande `BULK INSERT` lors de l’importation des fichiers depuis le Stockage Blob Azure. Consultez [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` est nécessaire dans la fonction `OPENROWSET` lorsque vous lisez le contenu d’un fichier à partir du stockage Blob Azure. Consultez [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR

Une instance managée ne pouvant pas accéder à des partages de fichiers et à des dossiers Windows, les contraintes suivantes s’appliquent :

- Seul `CREATE ASSEMBLY FROM BINARY` est pris en charge. Consultez [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` n’est pas pris en charge. Consultez [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` ne peut pas référencer des fichiers. Consultez [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

Les instructions DBCC non documentées qui sont activées dans SQL Server ne sont pas prises en charge dans les instances managées.

- Les `Trace flags` ne sont pas pris en charge. Consultez les [indicateurs de trace](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` n’est pas pris en charge. Consultez [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` n’est pas pris en charge. Consultez [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Transactions distribuées

MSDTC et les [transactions élastiques](sql-database-elastic-transactions-overview.md) ne sont actuellement pas pris en charge dans les instances managées.

### <a name="extended-events"></a>Événements étendus

Certaines cibles propres à Windows pour les événements étendus (XEvent) ne sont pas prises en charge :

- Le `etw_classic_sync` cible n’est pas pris en charge. Stockez les fichiers `.xel` dans le stockage Blob Azure. Consultez [etw_classic_sync target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Le `event_file` cible n’est pas pris en charge. Stockez les fichiers `.xel` dans le stockage Blob Azure. Consultez [event_file target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Bibliothèques externes

Les bibliothèques externes R et Python en base de données ne sont pas encore prises en charge. Consultez [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>Flux de fichier et Filetable

- Les données flux de fichier ne sont pas prises en charge.
- La base de données ne peut pas contenir de groupes de fichiers avec des données `FILESTREAM`.
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

- Les cibles prises en charge sont les instances managées, les bases de données uniques et les instances de SQL Server. 
- Les serveurs liés ne prennent pas en charge les transactions accessibles en écriture distribuées (MS DTC).
- Les cibles qui ne sont pas prises en charge sont des fichiers, Analysis Services et d’autres SGBDR. Essayez d’utiliser l’importation CSV native à partir de Stockage Blob Azure à l’aide de `BULK INSERT` ou `OPENROWSET` comme alternative pour l’importation de fichiers.

Opérations

- Les transactions d’écriture entre instances ne sont pas prises en charge.
- `sp_dropserver` est pris en charge pour supprimer un serveur lié. Consultez [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- La fonction `OPENROWSET` peut être utilisée pour exécuter des requêtes uniquement sur des instances SQL Server. Elles peuvent être managées, locales ou dans des machines virtuelles. Consultez [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- La fonction `OPENDATASOURCE` peut être utilisée pour exécuter des requêtes uniquement sur des instances SQL Server. Elles peuvent être managées, locales ou dans des machines virtuelles. Seules les valeurs `SQLNCLI`, `SQLNCLI11` et `SQLOLEDB` sont prises en charge en tant que fournisseur. Par exemple `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Consultez [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).
- Les serveurs liés ne peuvent pas être utilisés pour lire des fichiers (Excel, CSV) à partir des partages réseau. Essayez d’utiliser [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) ou [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) qui lit les fichiers CSV à partir de Stockage Blob Azure. Suivez ces requêtes sur l’[élément de commentaires de Managed Instance](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Les tables externes qui référencent les fichiers dans HDFS ou le stockage Blob Azure ne sont pas prises en charge. Pour plus d’informations sur PolyBase, consultez [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Réplication

La [réplication transactionnelle](sql-database-managed-instance-transactional-replication.md) est disponible en préversion publique dans Managed Instance avec certaines contraintes :
- Tous les types de participants de réplication (serveur de publication, serveur de distribution, abonné d’extraction et abonné de type push) peuvent être placés sur Managed Instance, mais le serveur de publication et le serveur de distribution ne peuvent pas être placés sur des instances différentes.
- Les types de réplication transactionnelle, d’instantané et bidirectionnelle sont pris en charge. La réplication de fusion, la réplication d’égal à égal et les abonnements modifiables ne sont pas pris en charge.
- Managed Instance peut communiquer avec les versions récentes de SQL Server. Consultez les versions prises en charge [ici](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems).
- La réplication transactionnelle présente des [exigences de mise en réseau supplémentaires](sql-database-managed-instance-transactional-replication.md#requirements).

Pour plus d’informations sur la configuration de la réplication, consultez le [didacticiel de réplication](replication-with-sql-database-managed-instance.md).

### <a name="restore-statement"></a>L’instruction RESTORE 

- Syntaxe prise en charge :
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Syntaxe non prise en charge :
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Source : 
  - `FROM URL` (Stockage Blob Azure) est l’unique option prise en charge.
  - L’unité de sauvegarde/`FROM DISK`/`TAPE` n’est pas prise en charge.
  - Les jeux de sauvegarde ne sont pas pris en charge.
- Les options `WITH` ne sont pas prises en charge, par exemple non `DIFFERENTIAL` ou `STATS`.
- `ASYNC RESTORE`: la restauration continue même si la connexion cliente s’arrête. Si votre connexion est interrompue, vous pouvez vérifier l’affichage `sys.dm_operation_status` pour l’état d’une opération de restauration, et pour la création et la suppression d’une base de données. Consultez [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Les options de base de données suivantes sont fixées ou remplacées et ne peuvent pas être modifiées ultérieurement : 

- `NEW_BROKER` si le broker n’est pas activé dans le fichier .bak. 
- `ENABLE_BROKER` si le broker n’est pas activé dans le fichier .bak. 
- `AUTO_CLOSE=OFF` si une base de données dans le fichier .bak comprend `AUTO_CLOSE=ON`. 
- `RECOVERY FULL` si une base de données dans le fichier .bak a le mode de récupération `SIMPLE` ou `BULK_LOGGED`.
- Un groupe de fichiers à mémoire optimisée est ajouté et appelé XTP s’il n’était pas dans le fichier .bak source. 
- Tout groupe de fichiers à mémoire optimisée existant est renommé XTP. 
- Les options `SINGLE_USER` et `RESTRICTED_USER` sont changées en `MULTI_USER`.

Limites : 

- Les fichiers `.BAK` qui contiennent plusieurs jeux de sauvegarde ne peuvent pas être restaurés. 
- Les fichiers `.BAK` qui contiennent plusieurs fichiers journaux ne peuvent pas être restaurés.
- La restauration échoue si le fichier.bak contient des données `FILESTREAM`.
- Les sauvegardes contenant des bases de données qui ont des objets en mémoire actifs ne peuvent pas être restaurées sur une instance Usage général. Pour plus d’informations sur les instructions de restauration, consultez [Instructions RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Service broker

Le Service Broker entre instances n’est pas pris en charge :

- `sys.routes`: Comme prérequis, vous devez sélectionner l’adresse à partir de sys.routes. L’adresse doit être LOCAL sur tous les itinéraires. Consultez [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: vous ne pouvez pas utiliser `CREATE ROUTE` avec `ADDRESS` si la valeur de celle-ci est différente de `LOCAL`. Consultez [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: vous ne pouvez pas utiliser `ALTER ROUTE` avec `ADDRESS` si la valeur de celle-ci est différente de `LOCAL`. Consultez [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Procédures stockées, fonctions et déclencheurs

- `NATIVE_COMPILATION` n’est pas pris en charge dans le niveau Usage général.
- Les options [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) suivantes ne sont pas prises en charge : 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` n’est pas pris en charge. Consultez [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` n’est pas pris en charge. Consultez [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` ne sont pas pris en charge, ce qui inclut `sp_addextendedproc` et `sp_dropextendedproc`. Consultez [Procédures stockées étendues](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` et `sp_detach_db` ne sont pas pris en charge. Consultez [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) et [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Environment"></a>Contraintes d’environnement

### <a name="subnet"></a>Subnet
- Dans le sous-réseau réservé pour votre instance managée, vous ne pouvez pas placer d’autres ressources (par exemple des machines virtuelles). Mettez ces ressources dans d’autres sous-réseaux.
- Le sous-réseau doit avoir un nombre suffisant d’[adresses IP](sql-database-managed-instance-connectivity-architecture.md#network-requirements) disponibles. Le nombre minimal est 16, mais la recommandation est de disposer d’au moins 32 adresses IP dans le sous-réseau.
- [Les points de terminaison de service ne peuvent pas être associés au sous-réseau de l’instance managée](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Vérifiez que l’option Points de terminaison de service est désactivée quand vous créez le réseau virtuel.
- Le nombre de vCores et de types d’instances que vous pouvez déployer dans une région ont certaines [contraintes et limites](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
- Il existe certaines [règles de sécurité qui doivent être appliquées sur le sous-réseau](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>Réseau virtuel
- Le réseau virtuel peut être déployé à l’aide du modèle de ressource ; le modèle classique pour réseau virtuel n’est pas pris en charge.
- Après la création d’une instance managée, le déplacement de l’instance managée ou du réseau virtuel vers un autre groupe de ressources ou vers un autre abonnement n’est pas pris en charge.
- Certains services, tels que App Service Environment, Logic Apps et Managed Instance (utilisés pour la géoréplication, la réplication transactionnelle ou via des serveurs liés), ne peuvent pas accéder aux instances Managed Instance dans des régions différentes si leurs réseaux virtuels sont connectés au moyen du [Peering mondial](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Vous pouvez vous connecter à ces ressources via ExpressRoute ou une connexion entre deux réseaux virtuels, par l’intermédiaire de passerelles de réseau virtuel.

## <a name="Changes"></a> Changements de comportement

Les variables, fonctions et vues suivantes retournent des résultats différents :

- `SERVERPROPERTY('EngineEdition')` retourne la valeur 8. Cette propriété identifie une instance gérée de façon unique. Consultez [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` retourne la valeur NULL, car le concept d’instance tel qu’il existe pour SQL Server ne s’applique pas à une instance managée. Consultez [SERVERPROPERTY(« InstanceName »)](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` retourne un nom DNS « connectable » complet, par exemple, my-managed-instance.wcus17662feb9ce98.database.windows.net. Consultez [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` retourne le nom DNS « connectable » complet, tel que `myinstance.domain.database.windows.net` pour les propriétés « name » et « data_source ». Consultez [SYS. SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` retourne la valeur NULL, car le concept de service tel qu’il existe pour SQL Server ne s’applique pas à une instance managée. Consultez [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` est pris en charge. Il retourne NULL si la connexion Azure AD n’est pas dans sys.syslogins. Consultez [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` n’est pas pris en charge. Les données incorrectes sont retournées, ce qui est un problème temporaire connu. Consultez [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Issues"></a> Problèmes connus et limitations

### <a name="tempdb-size"></a>Taille de TEMPDB

La taille de fichier maximale de `tempdb` ne peut pas être supérieure à 24 Go par cœur sur un niveau Usage général. La taille maximale de `tempdb` sur un niveau Critique pour l’entreprise est limitée à la taille de stockage d’instance. La taille du fichier journal `tempdb` est limitée à 120 Go sur les niveaux usage général et critique pour l’entreprise. La base de données `tempdb` est toujours divisée en 12 fichiers de données. Cette taille maximale par fichier n’est pas modifiable, et de nouveaux fichiers ne peuvent pas être ajoutés à `tempdb`. Certaines requêtes peuvent retourner une erreur si elles ont besoin de plus de 24 Go par cœur dans `tempdb` ou si elles produisent plus de 120 Go de journal. `tempdb` est toujours recréé en tant que base de données vide lorsque l’instance démarre ou bascule, et les modifications apportées dans `tempdb` ne sont pas conservées. 

### <a name="cant-restore-contained-database"></a>Impossible de restaurer la base de données autonome

Managed Instance ne peut pas restaurer les [bases de données autonomes](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). La restauration dans le temps des bases de données autonomes existantes ne fonctionne pas sur Managed Instance. Ce problème sera résolu prochainement. En attendant, nous vous conseillons de supprimer l’option d’autonomie de vos bases de données qui sont placées sur Managed Instance. N’utilisez pas l’option d’autonomie pour les bases de données de production. 

### <a name="exceeding-storage-space-with-small-database-files"></a>Dépassement de l’espace de stockage avec des fichiers de base de données de petite taille

Les instructions `CREATE DATABASE`, `ALTER DATABASE ADD FILE` et `RESTORE DATABASE` risquent d’échouer, car l’instance peut atteindre la limite de Stockage Azure.

Chaque instance managée Usage général dispose de 35 To de stockage réservé pour l’espace disque Premium Azure. Chaque fichier de base de données est placé sur un disque physique distinct. Les tailles de disque peuvent être de 128 Go, 256 Go, 512 Go, 1 To ou 4 To. L’espace non utilisé sur le disque n’est pas facturé, mais la somme des tailles des disques Premium Azure ne peut pas dépasser 35 To. Dans certains cas, une instance managée qui n’a pas besoin de 8 To au total peut dépasser la limite Azure de 35 To en taille de stockage à cause d’une fragmentation interne.

Par exemple, une instance managée Usage général peut avoir un fichier d’une taille de 1,2 To placé sur un disque de 4 To. Elle peut également posséder 248 fichiers, d’une taille de 1 Go chacun, qui sont placés sur des disques distincts de 128 Go. Dans cet exemple :

- La taille totale du stockage de disque alloué est de 1 x 4 To + 248 x 128 Go = 35 To.
- L’espace total réservé pour les bases de données sur l’instance est de 1 x 1,2 To + 248 x 1 Go = 1,4 To.

Cet exemple montre que, dans certaines circonstances, du fait d’une répartition spécifique des fichiers, une instance managée peut atteindre la limite des 35 To réservée pour un disque Premium Azure attaché, sans que vous vous y attendiez.

Dans cet exemple, les bases de données existantes continuent de fonctionner et peuvent croître sans aucun problème du moment que de nouveaux fichiers ne sont pas ajoutés. La création ou la restauration de bases de données est impossible, car il n’y a pas suffisamment d’espace pour les nouveaux lecteurs de disque, même si la taille totale de toutes les bases de données n’atteint pas la limite de taille d’instance. L’erreur qui est retournée dans ce cas n’est pas claire.

Vous pouvez [identifier le nombre de fichiers restants](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) à l’aide de vues système. Si vous atteignez cette limite, essayez de [vider et de supprimer certains fichiers plus petits au moyen de l’instruction DBCC SHRINKFILE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file), ou basculez vers le [niveau Critique pour l’entreprise, qui ne connaît pas cette limite](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="incorrect-configuration-of-the-sas-key-during-database-restore"></a>Configuration incorrecte de la clé SAP au cours d’une restauration de la base de données

Il se peut que `RESTORE DATABASE` qui lit le fichier .bak réessaie constamment de lire le fichier .bak et retourne une erreur après une longue période si la signature d’accès partagé dans `CREDENTIAL` est incorrecte. Exécutez RESTORE HEADERONLY avant de restaurer une base de données pour vous assurer que la clé SAP est correcte.
Veillez à supprimer le `?` au début de la clé SAP générée en utilisant le portail Azure.

### <a name="tooling"></a>Outils

SQL Server Management Studio et SQL Server Data Tools peuvent rencontrer des problèmes lorsqu’ils accèdent à une instance managée.

- L’utilisation de principaux de serveur (connexions) et d’utilisateurs (préversion publique) Azure AD avec SQL Server Data Tools n’est pas prise en charge actuellement.
- La création de scripts pour les principaux de serveur (connexions) et les utilisateurs (préversion publique) Azure AD n’est pas prise en charge dans SQL Server Management Studio.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Les noms des bases de données sont incorrects dans plusieurs vues, journaux d’activité et messages

Plusieurs vues système, compteurs de performances, messages d’erreur, événements XEvent et entrées du journal des erreurs affichent des identificateurs de base de données GUID au lieu d’afficher les noms des bases de données. Ne prenez pas en compte ces identificateurs GUID, car ils vont être remplacés à l’avenir par les noms des bases de données.

### <a name="database-mail"></a>Messagerie de base de données

Le paramètre `@query` dans la procédure [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) ne fonctionne pas.

### <a name="database-mail-profile"></a>Profil Database Mail

Le profil Database Mail utilisé par l’instance SQL Server Agent doit être appelé `AzureManagedInstance_dbmail_profile`. Il n’existe aucune restriction pour les autres noms de profil Database Mail.

### <a name="error-logs-arent-persisted"></a>Les journaux des erreurs ne sont pas conservés

Les journaux des erreurs qui sont disponibles dans Managed Instance ne sont pas persistants, et leur taille n’est pas comprise dans la limite de stockage maximale. Les journaux des erreurs peuvent être automatiquement effacés si un basculement se produit.

### <a name="error-logs-are-verbose"></a>Les journaux d’activité des erreurs contiennent des détails non pertinents

Une instance managée ajoute des informations détaillées dans les journaux des erreurs, la plupart ne sont pas pertinentes. La quantité d’informations qui s’y trouve va être réduite à l’avenir.

**Solution de contournement :** utilisez une procédure personnalisée pour lire les journaux des erreurs en excluant les entrées non pertinentes. Pour plus d’informations, consultez [Managed Instance - sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>L’utilisation de la même étendue de transaction pour deux bases de données appartenant à une même instance n’est pas prise en charge

Dans .NET, la classe `TransactionScope` ne fonctionne pas si deux requêtes sont envoyées à deux bases de données appartenant à la même instance et à la même étendue de transaction :

```csharp
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

Même si ce code fonctionne avec les données d’une même instance, il nécessite MSDTC.

**Solution de contournement :** servez-vous de [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) pour utiliser une autre base de données dans un contexte de connexion au lieu d’utiliser deux connexions.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Les modules CLR et les serveurs liés parfois ne parviennent pas à référencer une adresse IP locale

Il arrive que des modules CLR placés dans une instance managée, et que des serveurs liés ou des requêtes distribuées référençant une instance active, ne parviennent pas quelquefois à résoudre l’adresse IP d’une instance locale. Cette erreur est un problème temporaire.

**Solution de contournement :** utilisez des connexions contextuelles dans un module CLR, si possible.

### <a name="tde-encrypted-databases-with-a-service-managed-key-dont-support-user-initiated-backups"></a>Les bases de données chiffrées avec TDE, au moyen d’une clé managée par le service, ne prennent pas en charge les sauvegardes initiées par l’utilisateur

Vous ne pouvez pas exécuter `BACKUP DATABASE ... WITH COPY_ONLY` sur une base de données qui est chiffrée avec Transparent Data Encryption (TDE) managé par le service. TDE managé par le service oblige le chiffrement des sauvegardes à l’aide d’une clé de chiffrement TDE interne. La clé ne pouvant pas être exportée, vous ne pouvez pas restaurer la sauvegarde.

**Solution de contournement :** utilisez des sauvegardes automatiques et la restauration dans le temps, ou utilisez [TDE managé par le client (BYOK)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) à la place. Vous pouvez également désactiver le chiffrement sur la base de données.

### <a name="point-in-time-restore-follows-time-by-the-time-zone-set-on-the-source-instance"></a>La restauration dans le temps suit le fuseau horaire défini sur l’instance source

La restauration dans le temps interprète actuellement le temps de restauration en suivant le fuseau horaire de l’instance source plutôt qu’en suivant l’heure UTC.
Pour plus d’informations, consultez [Problèmes connus de fuseau horaire de Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-timezone#known-issues).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les instances managées, consultez [Présentation de Managed Instance](sql-database-managed-instance.md).
- Pour consulter la liste des fonctionnalités et les comparer, voir [Comparaison des fonctionnalités Azure SQL Database](sql-database-features.md).
- Pour obtenir un guide de démarrage rapide qui montre comment créer une instance managée, voir [Créer une instance managée](sql-database-managed-instance-get-started.md).
