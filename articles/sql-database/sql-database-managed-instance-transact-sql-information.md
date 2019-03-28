---
title: Différences T-SQL sur Azure SQL Database Managed Instance | Microsoft Docs
description: Cet article décrit les différences T-SQL entre une instance managée dans Azure SQL Database et dans SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 03/13/2019
ms.openlocfilehash: b044a7c2b3122fcbce44ae2e45198f57f6a87260
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541279"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Différences T-SQL entre Azure SQL Database Managed Instance et SQL Server

L’option de déploiement Managed Instance est hautement compatible avec le moteur de base de données SQL Server local. La plupart des fonctionnalités du moteur de base de données SQL Server sont prises en charge dans une instance managée.

![migration](./media/sql-database-managed-instance/migration.png)

Comme il existe toujours des différences de syntaxe et de comportement, cet article résume et explique ces différences. <a name="Differences"></a>

- [Disponibilité](#availability) incluant les différences dans [Always On](#always-on-availability) et [Sauvegardes](#backup),
- [Sécurité](#security) incluant les différences dans [audit](#auditing), [Certificats](#certificates), [Informations d’identification](#credential), [Fournisseurs de chiffrement](#cryptographic-providers), [Connexions/utilisateurs](#logins--users), [Clé de service et clé principale du service](#service-key-and-service-master-key),
- [Configuration](#configuration) incluant les différences dans [Extension du pool de mémoires tampons](#buffer-pool-extension), [Classement](#collation), [Niveaux de compatibilité](#compatibility-levels), [Mise en miroir de bases de données](#database-mirroring), [Options de base de données](#database-options), [SQL Server Agent](#sql-server-agent), [Options de Table](#tables),
- [Fonctionnalités](#functionalities) incluant [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [Transactions distribuées](#distributed-transactions), [Événements étendus](#extended-events), [Bibliothèques externes](#external-libraries), [FileStream et FileTable](#filestream-and-filetable), [Recherche sémantique de texte intégral](#full-text-semantic-search), [Serveurs liés](#linked-servers), [Polybase](#polybase), [Réplication](#replication), [RESTORE](#restore-statement), [Service Broker](#service-broker), [Procédures, fonctions et déclencheurs stockés](#stored-procedures-functions-triggers),
- [Fonctionnalités qui se comportent différemment dans les instances managées](#Changes)
- [Limitations temporaires et problèmes connus](#Issues)

## <a name="availability"></a>Disponibilité

### <a name="always-on-availability"></a>Always On

[Haute disponibilité](sql-database-high-availability.md) est intégré à Managed Instance et ne peut pas être contrôlé par les utilisateurs. Les instructions suivantes ne sont pas prises en charge :

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- Clause [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) de l’instruction [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Sauvegarde

Les instances managées disposent de sauvegardes automatiques qui permettent aux utilisateurs de créer des sauvegardes complètes `COPY_ONLY` des bases de données. Les sauvegardes différentielles, les sauvegardes de journaux et de captures instantanées de fichiers ne sont pas prises en charge.

- Avec une instance managée, vous pouvez sauvegarder une base de données d’instance uniquement vers un compte Stockage Blob Azure :
  - Seul `BACKUP TO URL` est pris en charge
  - `FILE`, `TAPE`, et les unités de sauvegarde ne sont pas pris en charge  
- La plupart des options générales `WITH` sont prises en charge
  - `COPY_ONLY` est obligatoire
  - `FILE_SNAPSHOT` non pris en charge
  - Options de bande : `REWIND`, `NOREWIND`, `UNLOAD` et `NOUNLOAD` ne sont pas pris en charge
  - Options propres au journal : `NORECOVERY`, `STANDBY` et `NO_TRUNCATE` ne sont pas pris en charge

Limites :  

- Une instance managée permet de sauvegarder une base de données d’instance vers une sauvegarde comprenant jusqu’à 32 bandes, ce qui est suffisant pour les bases de données jusqu’à 4 To si la compression de sauvegarde est utilisée.
- Taille de l’entrelacement de sauvegarde maximale à l’aide du `BACKUP` commande dans une instance managée est 195 Go (taille maximale des objets blob). Augmentez le nombre de bandes dans la commande de sauvegarde pour réduire la taille de bande individuelle et ne pas dépasser cette limite.

    > [!TIP]
    > Pour contourner cette limitation lorsque vous sauvegardez une base de données à partir de SQL Server dans un environnement sur site ou sur une machine virtuelle, vous pouvez procédez comme suit :
    >
    > - Sauvegarde à `DISK` au lieu de la sauvegarde sur `URL`
    > - Télécharger les fichiers de sauvegarde pour le stockage d’objets Blob
    > - Restaurer dans l’instance gérée
    >
    > Le `Restore` commande dans des instances gérées prend en charge des tailles d’objet blob plus volumineuses dans les fichiers de sauvegarde, car un type de l’autre objet blob est utilisé pour le stockage des fichiers de sauvegarde chargés.

Pour plus d’informations sur les sauvegardes à l’aide de T-SQL, consultez [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Sécurité

### <a name="auditing"></a>Audit

Les principales différences entre l’audit des bases de données dans Azure SQL Database et des bases de données dans SQL Server sont les suivantes :

- Avec l’option de déploiement Managed Instance dans Azure SQL Database, l’audit s’effectue au niveau du serveur et stocke les fichiers journaux `.xel` dans Stockage Blob Azure.
- Avec les options de déploiement de base de données unique et de pool élastique dans Azure SQL Database, l’audit fonctionne au niveau de la base de données.
- Dans SQL Server (en local ou sur machines virtuelles), l’audit fonctionne au niveau du serveur, mais stocke les événements dans les journaux des événements du système de fichiers/Windows.
  
L’audit XEvent dans Managed Instance prend en charge les cibles de Stockage Blob Azure. Les journaux de fichiers et de Windows ne sont pas pris en charge.

Les principales différences de syntaxe `CREATE AUDIT` pour l’audit du Stockage Blob Azure sont :

- Une nouvelle syntaxe `TO URL` est fournie et vous permet de spécifier l’URL du conteneur du Stockage Blob Azure où les fichiers `.xel` seront placés
- La syntaxe `TO FILE` n’est pas prise en charge, car une instance managée ne peut pas accéder à des partages de fichiers Windows.

Pour plus d'informations, consultez les pages suivantes :  

- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Audit](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificats

Une instance managée ne pouvant pas accéder à des partages de fichiers et à des dossiers Windows, les contraintes suivantes s’appliquent :

- Le fichier `CREATE FROM`/`BACKUP TO` n’est pas pris en charge pour les certificats
- Le certificat `CREATE`/`BACKUP` de `FILE`/`ASSEMBLY` n’est pas pris en charge. Les fichiers de clés privés ne peuvent pas être utilisés  

Consultez [CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) et [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql).  
  
**Solution de contournement** : ajoutez le certificat/la clé privée dans un script, stockez-le dans un fichier .sql et créez-le à partir du fichier binaire :

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Informations d'identification

Seuls Azure Key Vault et les identités `SHARED ACCESS SIGNATURE` sont pris en charge. Les utilisateurs de Windows ne sont pas pris en charge.

Consultez [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) et [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Fournisseurs de chiffrement

Une instance managée ne pouvant pas accéder aux fichiers, vous ne pouvez pas créer de fournisseurs de chiffrement :

- `CREATE CRYPTOGRAPHIC PROVIDER` n’est pas pris en charge. Consultez [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` n’est pas pris en charge. Consultez [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins--users"></a>Connexions/utilisateurs

- Les connexions SQL créées `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, et `FROM SID` sont prises en charge. Consultez [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Les principaux de serveur (connexions) Azure Active Directory (Azure AD) créés avec la syntaxe [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) ou la syntaxe [CREATE USER FROM LOGIN [Azure AD Login]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) sont pris en charge (**préversion publique**). Il s’agit de connexions créées au niveau du serveur.

    Managed Instance prend en charge les principaux de base de données Azure AD avec la syntaxe `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. On emploie également le terme « utilisateurs de base de données autonome Azure AD ».

- Les connexions Windows créées avec la syntaxe `CREATE LOGIN ... FROM WINDOWS` ne sont pas prises en charge. Utilisez des utilisateurs et des connexions Azure Active Directory.
- L’utilisateur Azure AD qui a créé l’instance dispose de [privilèges d’administrateur illimités](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Les utilisateurs au niveau de la base de données Azure Active Directory (Azure AD) non-administrateurs peuvent être créés à l’aide de la syntaxe `CREATE USER ... FROM EXTERNAL PROVIDER`. Consultez [CREATE USER ... FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users).
- Les principaux de serveur (connexions) Azure AD prennent en charge les fonctionnalités SQL dans une seule instance MI. Les fonctionnalités nécessitant une interaction entre les instances, que ce soit au sein du même locataire Azure AD ou d’un locataire différent, ne sont pas prises en charge pour les utilisateurs Azure AD. Il s’agit de fonctionnalités telles que :

  - Réplication transactionnelle SQL
  - Serveur de liaisons

- La définition d’une connexion Azure AD mappée à un groupe Azure AD en tant que propriétaire de base de données n’est pas prise en charge.
- L’emprunt d’identité des principaux au niveau du serveur Azure AD à l’aide d’autres principaux Azure AD est pris en charge, par exemple avec la clause [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql). Limitation concernant EXECUTE AS :

  - EXECUTE AS USER n’est pas pris en charge pour les utilisateurs Azure AD quand le nom diffère du nom de connexion. Par exemple, quand l’utilisateur est créé par le biais de la syntaxe CREATE USER [myAadUser] FROM LOGIN [john@contoso.com] et que l’emprunt d’identité est tenté par le biais de EXEC AS USER = _myAadUser_. Quand vous créez un **USER** à partir d’un principal de serveur (connexion) Azure AD, spécifiez le même user_name que le login_name à partir de **LOGIN**.
  - Seuls les principaux au niveau du serveur SQL (connexions) faisant partie du rôle `sysadmin` peuvent exécuter les opérations suivantes ciblant les principaux Azure AD :

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Limitations de la **préversion publique** pour les principaux de serveur (connexions) Azure AD :

  - Limitations concernant l’administration Active Directory pour Managed Instance :

    - Le compte Administrateur Azure AD utilisé pour configurer l’instance managée ne peut pas être utilisé pour créer un principal de serveur (connexion) Azure AD au sein de l’instance managée. Vous devez créer le premier principal de serveur (connexion) Azure AD à l’aide d’un compte SQL Server `sysadmin`. Il s’agit d’une limitation temporaire qui sera levée une fois que les principaux de serveur (connexions) Azure AD deviendront des comptes en disponibilité générale. L’erreur suivante s’affiche si vous essayez d’utiliser un compte Administrateur Azure AD pour créer la connexion : `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - Actuellement, la première connexion Azure AD créée dans la base de données master doit être créée par le compte SQL Server standard (non Azure AD) qui est un `sysadmin` à l’aide de [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) FROM EXTERNAL PROVIDER. Après la disponibilité générale, cette limitation sera supprimée et une connexion Azure AD initiale pourra être créée par l’administrateur Active Directory pour Managed Instance.
    - DacFx (exportation/importation) utilisé avec SQL Server Management Studio (SSMS) ou SqlPackage n’est pas pris en charge pour les connexions Azure AD. Cette limitation sera levée une fois que les principaux de serveur (connexions) Azure AD deviendront des comptes en disponibilité générale.
    - Utilisation de principaux de serveur (connexions) Azure AD avec SSMS

      - La création de scripts de connexions Azure AD (à l’aide de toute connexion authentifiée) n’est pas prise en charge.
      - IntelliSense ne reconnaît pas l’instruction **CREATE LOGIN FROM EXTERNAL PROVIDER** et affiche un trait de soulignement rouge.

- Seuls la connexion du principal au niveau du serveur (créée par le processus de provisionnement Managed Instance), les membres des rôles de serveur (`securityadmin` ou `sysadmin`) ou d’autres connexions disposant de l’autorisation ALTER ANY LOGIN au niveau du serveur peuvent créer les principaux de serveur (connexions) Azure AD dans la base de données master pour Managed Instance.
- Si la connexion est un principal SQL, seules les connexions faisant partie du rôle `sysadmin` peuvent utiliser la commande create afin de créer des connexions pour un compte Azure AD.
- La connexion Azure AD doit être membre d’un compte Azure AD dans le même répertoire que celui utilisé pour Azure SQL Managed Instance.
- Les principaux de serveur (connexions) Azure AD sont visibles dans l’Explorateur d’objets à compter de SSMS 18.0 préversion  5.
- Le chevauchement des principaux de serveur (connexions) Azure AD avec un compte d’administrateur Azure AD est autorisé. Les principaux de serveur (connexions) Azure AD sont prioritaires par rapport à l’Administrateur Azure AD lors de la résolution du principal et de l’application des autorisations à l’instance managée.
- Lors de l’authentification, la séquence suivante est appliquée pour résoudre le principal d’authentification :

    1. Si le compte Azure AD existe en tant que mappage direct au principal de serveur (connexion) Azure AD (présent dans sys.server_principals en tant que type « E »), l’accès est accordé et les autorisations du principal de serveur (connexion) Azure AD sont appliquées.
    2. Si le compte Azure AD est membre d’un groupe Azure AD mappé au principal de serveur (connexion) Azure AD (présent dans sys.server_principals en tant que type « X »), l’accès est accordé et les autorisations de la connexion du groupe Azure AD sont appliquées.
    3. Si le compte Azure AD est un administrateur Azure AD spécial configuré dans le portail pour Managed Instance (il n’existe pas dans les vues système de Managed Instance), les autorisations fixes spéciales de l’administrateur Azure AD pour Managed Instance (mode hérité) sont appliquées.
    4. Si le compte Azure AD existe en tant que mappage direct à un utilisateur Azure AD dans une base de données (dans sys.database_principals en tant que type « E »), l’accès est accordé et les autorisations de l’utilisateur de base de données Azure AD sont appliquées.
    5. Si le compte Azure AD est membre d’un groupe Azure AD mappé à un utilisateur Azure AD dans une base de données (dans sys.database_principals en tant que type « X »), l’accès est accordé et les autorisations de la connexion du groupe Azure AD sont appliquées.
    6. S’il existe une connexion Azure AD mappée à un compte d’utilisateur Azure AD ou à un compte de groupe Azure AD, avec résolution à l’utilisateur qui s’authentifie, toutes les autorisations de cette connexion Azure AD sont appliquées.

### <a name="service-key-and-service-master-key"></a>Clé de service et clé principale de service

- [Backup master key](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) n’est pas pris en charge (géré par SQL Database service)
- [Restore master key](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) n’est pas pris en charge (géré par SQL Database service)
- [Backup service master key](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) n’est pas pris en charge (géré par SQL Database service)
- [Restore service master key](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) n’est pas pris en charge (géré par SQL Database service)

## <a name="configuration"></a>Configuration

### <a name="buffer-pool-extension"></a>Extension du pool de mémoires tampons

- L’[extension du pool de mémoires tampons](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) n’est pas prise en charge.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` n’est pas pris en charge. Consultez [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Collation

Le classement d’instance par défaut est `SQL_Latin1_General_CP1_CI_AS` et peut être spécifié comme paramètre de création. Consultez [Classements](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Niveaux de compatibilité

- Les niveaux de compatibilité pris en charge sont les suivants : 100, 110, 120, 130, 140  
- Les niveaux de compatibilité inférieurs à 100 ne sont pas pris en charge.
- Le niveau de compatibilité par défaut est de 140 pour les nouvelles bases de données. Pour les bases de données restaurées, le niveau de compatibilité reste inchangé s’il était de 100 et plus.

Consultez [Niveau de compatibilité ALTER TABLE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Mise en miroir de bases de données

La mise en miroir de bases de données n’est pas prise en charge.

- Les options `ALTER DATABASE SET PARTNER` et `SET WITNESS` ne sont pas prises en charge.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` n’est pas pris en charge.

Pour plus d’informations, consultez [ALTER DATABASE SET PARTNER AND SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) et [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Options de la base de données

- Les fichiers journaux multiples ne sont pas pris en charge.
- Les objets en mémoire ne sont pas pris en charge dans le niveau de service Usage général.  
- Il existe une limite de 280 fichiers par instance à usage général qui implique le maximum de 280 fichiers par base de données. Les données et des fichiers journaux en général objectif niveau sont comptabilisés dans cette limite. [Niveau critique pour l’entreprise prend en charge 32 767 fichiers par base de données](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- La base de données ne peut pas contenir de groupes de fichiers contenant des données FILESTREAM.  La restauration échoue si le fichier.bak contient des données `FILESTREAM`.  
- Chaque fichier est placé dans Stockage Blob Azure. L’E/S et le débit par fichier dépendent de la taille de chaque fichier.  

#### <a name="create-database-statement"></a>Instruction CREATE DATABASE

Les éléments suivants sont des limitations `CREATE DATABASE` :

- Les fichiers et les groupes de fichiers ne peuvent pas être définis.  
- L’option `CONTAINMENT` n’est pas prise en charge.  
- Les options `WITH` ne sont pas prises en charge.  
   > [!TIP]
   > Comme solution de contournement, utilisez `ALTER DATABASE` après `CREATE DATABASE` pour définir les options de la base de données de façon à ajouter des fichiers ou pour définir la relation contenant-contenu.  

- L’option `FOR ATTACH` n’est pas prise en charge.
- L’option `AS SNAPSHOT OF` n’est pas prise en charge.

Pour plus d’informations, consultez [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instruction ALTER DATABASE

Certaines propriétés de fichiers ne peuvent pas être définies ou modifiées :

- Le chemin de fichier ne peut pas être spécifié dans l’instruction T-SQL `ALTER DATABASE ADD FILE (FILENAME='path')`. Enlevez `FILENAME` du script car une instance managée place les fichiers automatiquement.  
- Le nom de fichier ne peut pas être modifié à l’aide de l’instruction `ALTER DATABASE`.

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

- Les paramètres de l’Agent SQL sont en lecture seule. La procédure `sp_set_agent_properties` n’est pas prise en charge dans Managed Instance.  
- Tâches
  - Les étapes de travail T-SQL sont prises en charge.
  - Les travaux de réplication suivants sont pris en charge :
    - Lecteur de journaux de transactions
    - Instantané
    - Serveur de distribution
  - Les étapes de travail SSIS sont prises en charge
  - Les autres types d’étapes de travail ne sont pas pris en charge actuellement, notamment :
    - L’étape de travail de réplication de fusion n’est pas prise en charge.  
    - L’agent de lecture de la file d’attente n’est pas pris en charge.  
    - L’interface de commande n’est pas encore prise en charge.
  - Les instances managées ne peuvent pas accéder à des ressources externes (par exemple des partages réseau via robocopy).  
  - PowerShell n’est pas encore pris en charge.
  - Analysis Services n’est pas pris en charge.
- Les notifications sont partiellement prises en charge.
- Les notifications par e-mail sont prises en charge, elles requièrent la configuration d’un profil de messagerie de base de données. Il ne peut y avoir qu’un seul profil de messagerie de base de données et il doit être appelé `AzureManagedInstance_dbmail_profile` en préversion publique (limitation temporaire).  
  - Le récepteur d’appel n’est pas pris en charge.  
  - NetSend n’est pas pris en charge.
  - Les alertes ne sont pas encore prises en charge.
  - Les proxies ne sont pas pris en charge.  
- Le journal des événements n’est pas pris en charge.

Les fonctionnalités suivantes ne sont actuellement pas prises en charge, mais seront activées à l’avenir :

- Proxies
- Planification de travaux sur une UC inactive
- Activation/Désactivation de l’Agent
- Alertes

Pour plus d’informations sur SQL Server Agent, consultez [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tables

Les éléments suivants ne sont pas pris en charge :

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED`  

Pour plus d’informations sur la création et modification des tables, consultez [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) et [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Fonctionnalités

### <a name="bulk-insert--openrowset"></a>Insertion en bloc/openrowset

Une instance managée ne pouvant pas accéder à des partages de fichiers et à des dossiers Windows, les fichiers doivent être importés à partir de Stockage Blob Azure :

- `DATASOURCE` est requis dans la commande `BULK INSERT` lors de l’importation des fichiers depuis le Stockage Blob Azure. Consultez [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` est requis dans la fonction `OPENROWSET` lorsque vous lisez le contenu d’un fichier à partir du Stockage Blob Azure. Consultez [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR

Une instance managée ne pouvant pas accéder à des partages de fichiers et à des dossiers Windows, les contraintes suivantes s’appliquent :

- Seul `CREATE ASSEMBLY FROM BINARY` est pris en charge. Consultez [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).  
- `CREATE ASSEMBLY FROM FILE` n’est pas pris en charge. Consultez [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` ne peut pas référencer des fichiers. Consultez [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

Les instructions DBCC non documentées activées dans SQL Server ne sont pas prises en charge dans les instances managées.

- Les `Trace Flags` ne sont pas pris en charge. Consultez les [indicateurs de trace](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` n’est pas pris en charge. Consultez [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` n’est pas pris en charge. Consultez [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Transactions distribuées

Actuellement, ni MSDTC, ni les [Transactions élastiques](sql-database-elastic-transactions-overview.md) ne sont pris en charge dans les instances managées.

### <a name="extended-events"></a>Événements étendus

Certaines cibles propres à Windows pour les événements XEvent ne sont pas prises en charge :

- `etw_classic_sync target` n’est pas pris en charge. Stockez les fichiers `.xel` sur le Stockage Blob Azure. Consultez [etw_classic_sync target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- `event_file target` n’est pas pris en charge. Stockez les fichiers `.xel` sur le Stockage Blob Azure. Consultez [event_file target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Bibliothèques externes

Les bibliothèques R et Python externes en base de données ne sont pas encore prises en charge. Consultez [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>Flux de fichier et Filetable

- Les données FILESTREAM ne sont pas prises en charge.
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

- Cibles prises en charge : SQL Server et SQL Database
- Cibles non prises en charge : fichiers, Analysis Services et autres SGBDR.

Opérations

- Les transactions d’écriture entre instances ne sont pas prises en charge.
- `sp_dropserver` est pris en charge pour supprimer un serveur lié. Consultez [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- La fonction `OPENROWSET` peut être utilisée pour exécuter des requêtes uniquement sur les instances de SQL Server (géré, local ou sur des machines virtuelles). Consultez [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- La fonction `OPENDATASOURCE` peut être utilisée pour exécuter des requêtes uniquement sur les instances de SQL Server (géré, local ou sur des machines virtuelles). Seules les valeurs `SQLNCLI`, `SQLNCLI11`, et `SQLOLEDB` sont prises en charge en tant que fournisseur. Par exemple : `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Consultez [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>Polybase

Les tables externes référençant les fichiers dans HDFS ou le Stockage Blob Azure ne sont pas prises en charge. Pour plus d’informations sur Polybase, consultez [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Réplication

Une réplication est disponible en préversion publique pour Managed Instance. Pour plus d’informations sur la réplication, consultez [Réplication SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).

### <a name="restore-statement"></a>L’instruction RESTORE

- Syntaxe prise en charge
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Syntaxe non prise en charge
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Source  
  - `FROM URL` (Stockage Blob Azure) est l’unique option prise en charge.
  - L’unité de sauvegarde/`FROM DISK`/`TAPE` n’est pas prise en charge.
  - Les jeux de sauvegarde ne sont pas pris en charge.
- Les options `WITH` ne sont pas prises en charge (`DIFFERENTIAL`, `STATS`, et ainsi de suite).
- `ASYNC RESTORE` - la restauration continue même si la connexion cliente s’arrête. Si votre connexion est interrompue, vous pouvez vérifier l’affichage `sys.dm_operation_status` de l’état d’une opération de restauration (ainsi que pour la création et la suppression d’une base de données). Consultez [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).  

Les options de base de données suivantes sont fixées/remplacées et ne peuvent pas être modifiées ultérieurement :  

- `NEW_BROKER` (si le broker n’est pas activé dans le fichier .bak)  
- `ENABLE_BROKER` (si le broker n’est pas activé dans le fichier .bak)  
- `AUTO_CLOSE=OFF` (si une base de données dans le fichier .bak comporte `AUTO_CLOSE=ON`)  
- `RECOVERY FULL` (si une base de données dans le fichier .bak a le mode de récupération `SIMPLE` ou `BULK_LOGGED`)
- Le groupe de fichiers à mémoire optimisée est ajouté et appelé XTP s’il n’était pas dans le fichier .bak source  
- Les groupe de fichiers mémoire optimisée existants sont renommés XTP  
- Les options `SINGLE_USER` et `RESTRICTED_USER` sont changées en `MULTI_USER`

Limites :  

- Les fichiers `.BAK` contenant plusieurs jeux de sauvegarde ne peuvent pas être restaurés.
- Les fichiers `.BAK` contenant plusieurs fichiers journaux ne peuvent pas être restaurés.
- La restauration échoue si le fichier.bak contient des données `FILESTREAM`.
- Il est actuellement impossible de restaurer les sauvegardes contenant des bases de données qui ont des objets en mémoire actifs.  
- Il est actuellement impossible de restaurer les sauvegardes contenant des bases de données où des objets en mémoire ont existé à un moment donné.
- Il est actuellement impossible de restaurer les sauvegardes contenant des bases de données en mode lecture seule. Cette limitation sera supprimée sous peu.

Pour plus d’informations sur les instructions de restauration, consultez [Instructions RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Service broker

Le Service Broker entre instances n’est pas pris en charge :

- `sys.routes` - Condition préalable : sélectionnez l’adresse à partir de sys.routes. L’adresse doit être LOCAL sur tous les itinéraires. Consultez [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE` : vous ne pouvez pas utiliser `CREATE ROUTE` avec `ADDRESS` si la valeur de celle-ci est différente de `LOCAL`. Consultez [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE` : vous ne pouvez pas `ALTER ROUTE` avec une `ADDRESS` autre que `LOCAL`. Consultez [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql).  

### <a name="stored-procedures-functions-triggers"></a>Procédures stockées, fonctions, déclencheurs

- `NATIVE_COMPILATION` n’est pas pris en charge dans le niveau usage général.
- Les options [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) suivantes ne sont pas prises en charge :
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` n’est pas pris en charge. Consultez [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` n’est pas pris en charge. Consultez [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- Les `Extended stored procedures` ne sont pas prises en charge, y compris `sp_addextendedproc` et `sp_dropextendedproc`. Consultez [Procédures stockées étendues](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)
- `sp_attach_db`, `sp_attach_single_file_db` et `sp_detach_db` ne sont pas pris en charge. Consultez [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) et [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Changes"></a> Changements de comportement

Les variables, fonctions et vues suivantes retournent des résultats différents :

- `SERVERPROPERTY('EngineEdition')` retourne la valeur 8. Cette propriété identifie une instance managée de façon unique. Consultez [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` retourne la valeur NULL, car le concept d’instance tel qu’il existe pour SQL Server ne s’applique pas à une instance managée. Consultez [SERVERPROPERTY(« InstanceName »)](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` retourne le nom DNS « connectable »complet, par exemple, my-managed-instance.wcus17662feb9ce98.database.windows.net. Consultez [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` -retourne le nom DNS « connectable »complet, tel que `myinstance.domain.database.windows.net` pour les propriétés « name » et « data_source ». Consultez [SYS. SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` retourne la valeur NULL, car le concept de service tel qu’il existe pour SQL Server ne s’applique pas à une instance managée. Consultez [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` est pris en charge. Retourne NULL si la connexion Azure AD n’est pas dans sys.syslogins. Consultez [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql).  
- `SUSER_SID` n’est pas pris en charge. Retourne des données incorrectes (problème temporaire connu). Consultez [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql).
- `GETDATE()` et autres fonctions de date et d’heure intégrées retourne toujours l’heure dans le fuseau horaire UTC. Consultez [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql).

## <a name="Issues"></a> Problèmes connus et limitations

### <a name="tempdb-size"></a>Taille de TEMPDB

Taille de fichier maximale de `tempdb` ne peut pas être supérieur à 24 Go/core sur le niveau usage général. Max `tempdb` taille sur le niveau critique pour l’entreprise est limité par la taille de stockage d’instance. `tempdb` est toujours Fractionner en fichiers de 12 données. Cette taille maximale par fichier ne peut pas être changée, et de nouveaux fichiers ne peuvent pas être ajoutés à `tempdb`. Certaines requêtes peuvent retourner une erreur s’ils ont besoin de plus de 24 Go / cœur dans `tempdb`.

### <a name="cannot-restore-contained-database"></a>Impossible de restaurer le contenu de la base de données

Instance gérée ne peut pas restaurer [bases de données autonomes](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Restauration de point-à-temps des bases de données en relation contenant-contenus existantes ne fonctionnent pas sur Managed Instance. Ce problème sera supprimé prochainement et en attendant, nous vous recommandons de supprimer l’option de relation contenant-contenu à partir de vos bases de données qui sont placés sur Managed Instance et n’utilisent pas d’option de relation contenant-contenu pour les bases de données de production.

### <a name="exceeding-storage-space-with-small-database-files"></a>Dépassement de l’espace de stockage avec des fichiers de base de données de petite taille

Chaque Instance de géré à usage général a jusqu'à 35 To de stockage réservé pour l’espace disque Premium Azure, et chaque fichier de base de données est placé sur un disque physique distinct. Les tailles de disque peuvent être de 128 Go, 256 Go, 512 Go, 1 To ou 4 To. L’espace non utilisé sur le disque n’est pas facturé, mais la somme des tailles des disques Premium Azure ne peut pas dépasser 35 To. Dans certains cas, une instance gérée qui n’a pas besoin de 8 To au total peut dépasser la limite Azure de 35 To sur la taille de stockage, en raison d’une fragmentation interne.

Par exemple, une Instance gérée d’usage général peut avoir un fichier de 1,2 To par la taille qui est placé sur un disque de 4 To et 248 fichiers (chaque taille 1 Go) qui sont placés sur des disques distincts de 128 Go. Dans cet exemple :

- La taille totale du stockage de disque alloué est de 1 x 4 To + 248 x 128 Go = 35 To.
- L’espace total réservé pour les bases de données sur l’instance est de 1 x 1,2 To + 248 x 1 Go = 1,4 To.

Cet exemple montre que dans certaines circonstances, du fait d’une distribution spécifique des fichiers, une instance managée peut atteindre les 35 To réservés pour le disque Premium Azure attaché sans que vous vous y attendiez.

Dans cet exemple, les bases de données existantes continuent de fonctionner et peuvent croître sans aucun problème du moment que de nouveaux fichiers ne sont pas ajoutés. Toutefois, la création ou la restauration de bases de données est impossible, car il n’y a pas suffisamment d’espace pour les nouveaux lecteurs de disque, même si la taille totale de toutes les bases de données n’atteint pas la limite de taille d’instance. L’erreur retournée dans ce cas n’est pas claire.

Vous pouvez [identifier le nombre de fichiers restants](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) à l’aide de vues système. Si vous avez atteint cette limite essayez [vides et de supprimer certains des fichiers plus petits à l’aide d’instruction DBCC SHRINKFILE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) ou shitch à [niveau critique pour l’entreprise qui n’est associé à cette limite](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>Configuration incorrecte de la clé SAP au cours d’une restauration de la base de données

Il se peut que `RESTORE DATABASE` qui lit le fichier .bak réessaie constamment de lire le fichier .bak et retourne une erreur après une longue période si la signature d’accès partagé dans `CREDENTIAL` est incorrecte. Exécutez RESTORE HEADERONLY avant de restaurer une base de données pour vous assurer que la clé SAP est correcte.
Veillez à supprimer le `?` au début de la clé SAP générée à l’aide du portail Microsoft Azure.

### <a name="tooling"></a>Outils

SQL Server Management Studio (SSMS) et SQL Server Data Tools (SSDT) peuvent rencontrer des problèmes au moment d’accéder à une instance managée.

- L’utilisation de principaux de serveur (connexions) et d’utilisateurs Azure AD (**préversion publique**) avec SSDT n’est pas prise en charge actuellement.
- La création de scripts pour les principaux de serveur (connexions) et les utilisateurs Azure AD (**préversion publique**) n’est pas prise en charge dans SSMS.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Les noms des bases de données sont incorrects dans plusieurs vues, journaux et messages

Plusieurs vues système, compteurs de performances, messages d’erreur, événements XEvent et entrées du journal des erreurs affichent des identificateurs de base de données GUID au lieu d’afficher les noms des bases de données. Ne prenez pas en compte ces identificateurs GUID, car ils vont être prochainement remplacés par les noms des bases de données.

### <a name="database-mail-profile"></a>Profil de messagerie de base de données

Le profil de messagerie de base de données utilisé par l’Agent SQL doit être appelé `AzureManagedInstance_dbmail_profile`.

### <a name="error-logs-are-not-persisted"></a>Les journaux des erreurs ne sont pas persistants

Les journaux des erreurs qui sont disponibles dans Managed Instance ne sont pas persistants et leur taille n’est pas comprise dans la limite de stockage maximale. Les journaux des erreurs peuvent être automatiquement effacés en cas de basculement.

### <a name="error-logs-are-verbose"></a>Les journaux des erreurs contiennent des détails non pertinents

Une instance managée ajoute des informations détaillées dans les journaux des erreurs, dont la plupart ne sont pas pertinentes. La quantité d’informations qui s’y trouvent va être prochainement réduite.

**Solution de contournement** : utilisez une procédure personnalisée pour faire en sorte que les journaux des erreurs n’affichent pas les entrées non pertinentes. Pour plus d’informations, consultez [Managed Instance – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>L’utilisation de la même étendue de transaction pour deux bases de données appartenant à une même instance n’est pas prise en charge

`TransactionScope` classe dans .NET ne fonctionne pas si les deux requêtes sont envoyées aux deux bases de données dans la même instance sous la même étendue de transaction :

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

Même si ce code fonctionne avec les données d’une même instance, il nécessite MSDTC.

**Solution de contournement** : servez-vous de [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) pour utiliser une autre base de données dans le contexte de la connexion au lieu d’utiliser deux connexions.

### <a name="clr-modules-and-linked-servers-sometime-cant-reference-local-ip-address"></a>Les modules CLR et les serveurs liés n’arrivent pas à référencer l’adresse IP locale

Il arrive que des modules CLR placés dans une instance managée, et que des requêtes distribuées ou des serveurs liés référençant une instance active, ne parviennent pas à résoudre l’adresse IP de l’instance locale. Cette erreur est un problème temporaire.

**Solution de contournement** : utilisez des connexions contextuelles dans le module CLR, si possible.

### <a name="tde-encrypted-databases-dont-support-user-initiated-backups"></a>Les bases de données chiffrées avec TDE ne prennent pas en charge les sauvegardes initiées par l'utilisateur

Vous ne pouvez pas exécuter `BACKUP DATABASE ... WITH COPY_ONLY` sur une base de données chiffrée avec TDE (Transparent Data Encryption). TDE vous oblige à chiffrer les sauvegardes avec des clés TDE internes. Et comme les clés ne peuvent pas être exportées, vous ne pourrez pas restaurer la sauvegarde.

**Solution de contournement** : Utilisez des sauvegardes automatiques et des restaurations ponctuelles, ou désactivez le chiffrement de la base de données.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les instances managées, consultez [Qu’est-ce qu’une instance managée ?](sql-database-managed-instance.md).
- Pour consulter la liste des fonctionnalités et les comparer, consultez [Fonctionnalités SQL communes](sql-database-features.md).
- Pour obtenir un guide de démarrage rapide vous expliquant comment créer une instance managée, consultez [Créer une instance managée SQL Azure](sql-database-managed-instance-get-started.md).
