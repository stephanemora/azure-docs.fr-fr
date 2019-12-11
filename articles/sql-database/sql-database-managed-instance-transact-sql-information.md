---
title: Différences entre les instances gérées T-SQL
description: Cet article décrit les différences T-SQL entre une instance managée dans Azure SQL Database et dans SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
ms.date: 11/04/2019
ms.custom: seoapril2019
ms.openlocfilehash: e517b6030aa1c9549e33c00425851afae90aac42
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707641"
---
# <a name="managed-instance-t-sql-differences-limitations-and-known-issues"></a>Différences T-SQL, limitations et problèmes connus avec une instance managée

Cet article résume et explique les différences de syntaxe et de comportement existant entre Azure SQL Database Managed Instance et le moteur de base de données SQL Server local. L’option de déploiement d’instance gérée est hautement compatible avec le moteur de base de données SQL Server local. La plupart des fonctionnalités du moteur de base de données SQL Server sont prises en charge dans une instance gérée.

![Migration](./media/sql-database-managed-instance/migration.png)

Certaines limitations PaaS ont été introduites dans Managed Instance et certains comportements ont changé par rapport à SQL Server. Les différences se répartissent en différentes catégories, à savoir : <a name="Differences"></a>

- la [disponibilité](#availability) incluant les différences dans les [groupes de disponibilité Always On](#always-on-availability-groups) et les [sauvegardes](#backup) ;
- la [sécurité](#security) incluant les différences dans l’[audit](#auditing), les [certificats](#certificates), les [informations d’identification](#credential), les [fournisseurs de chiffrement](#cryptographic-providers), les [connexions et les utilisateurs](#logins-and-users) ainsi que la [clé de service et la clé principale du service](#service-key-and-service-master-key) ;
- la [configuration](#configuration) incluant les différences dans l’[extension du pool de mémoires tampons](#buffer-pool-extension), le [classement](#collation), les [niveaux de compatibilité](#compatibility-levels), la [mise en miroir de bases de données](#database-mirroring), les [options de base de données](#database-options), le service [SQL Server Agent](#sql-server-agent) et les [options de Table](#tables) ;
- les [fonctionnalités](#functionalities) incluant [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), les [transactions distribuées](#distributed-transactions), les [événements étendus](#extended-events), les [bibliothèques externes](#external-libraries), le [flux de fichier et FileTable](#filestream-and-filetable), la [recherche sémantique de texte intégral](#full-text-semantic-search), les [serveurs liés](#linked-servers), [Polybase](#polybase), la [Réplication](#replication), la [RESTAURATION](#restore-statement), [Service Broker](#service-broker), les [procédures stockées, fonctions et déclencheurs](#stored-procedures-functions-and-triggers).
- les [paramètres d’environnement](#Environment), tels que les configurations de réseau virtuel et de sous-réseau ;

La plupart de ces fonctionnalités sont des contraintes architecturales et représentent des fonctionnalités de service.

Cette page décrit également des [problèmes connus temporaires](#Issues) qui ont été détectés dans Managed Instance et qui seront résolus ultérieurement.

## <a name="availability"></a>Disponibilité

### <a name="always-on-availability-groups"></a>Groupes de disponibilité Always On

La [haute disponibilité](sql-database-high-availability.md) étant intégrée à Managed Instance, les utilisateurs ne peuvent pas la contrôler. Les instructions suivantes ne sont pas prises en charge :

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- La clause [SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) de l’instruction [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)

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
- Vous ne pouvez pas exécuter `BACKUP DATABASE ... WITH COPY_ONLY` sur une base de données qui est chiffrée avec Transparent Data Encryption (TDE) managé par le service. TDE managé par le service oblige le chiffrement des sauvegardes à l’aide d’une clé de chiffrement TDE interne. La clé ne pouvant pas être exportée, vous ne pouvez pas restaurer la sauvegarde. utilisez des sauvegardes automatiques et la restauration dans le temps, ou utilisez [TDE managé par le client (BYOK)](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) à la place. Vous pouvez également désactiver le chiffrement sur la base de données.
- La taille maximale de la bande de sauvegarde en utilisant la commande `BACKUP` dans une instance managée est de 195 Go, ce qui représente la taille maximale des objets blob. Augmentez le nombre de bandes dans la commande de sauvegarde pour réduire la taille de bande individuelle et ne pas dépasser cette limite.

    > [!TIP]
    > Pour contourner cette limitation, lorsque vous sauvegardez une base de données, soit à partir de SQL Server dans un environnement local, soit sur une machine virtuelle, vous pouvez :
    >
    > - sauvegarder sur `DISK` au lieu de sauvegarder jusqu’à `URL` ;
    > - charger les fichiers de sauvegarde sur le stockage d’objets blob ;
    > - restaurer dans l’instance managée.
    >
    > La commande `Restore` dans une instance managée prend en charge des tailles d’objet blob plus volumineuses dans les fichiers de sauvegarde, car un autre type d’objet blob est utilisé pour le stockage des fichiers de sauvegarde chargés.

Pour plus d’informations sur les sauvegardes à l’aide de T-SQL, consultez [BACKUP](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Sécurité

### <a name="auditing"></a>Audit

Les principales différences entre l’audit des bases de données dans Azure SQL Database et des bases de données dans SQL Server sont les suivantes :

- Avec l’option de déploiement Managed Instance dans Azure SQL Database, l’audit s’effectue au niveau du serveur. Les fichiers journaux `.xel` sont stockés dans le stockage Blob Azure.
- Avec les options de déploiement de base de données unique et de pool élastique dans Azure SQL Database, l’audit fonctionne au niveau de la base de données.
- Dans SQL Server en local ou sur machines virtuelles, l’audit s’effectue au niveau serveur. Les événements sont stockés dans le système de fichiers ou dans les journaux des événements Windows.
 
L’audit XEvent d’une instance gérée prend en charge les cibles de Stockage Blob Azure. Les journaux de fichiers et de Windows ne sont pas pris en charge.

Les principales différences de syntaxe `CREATE AUDIT` pour l’audit du Stockage Blob Azure sont :

- Une nouvelle syntaxe `TO URL` est fournie, vous pouvez l’utiliser pour spécifier l’URL du conteneur de stockage Blob Azure où les fichiers `.xel` sont placés.
- La syntaxe `TO FILE` n’est pas prise en charge, car une instance managée ne peut pas accéder à des partages de fichiers Windows.

Pour plus d'informations, consultez les pages suivantes : 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Audit](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificats

Une instance managée ne pouvant pas accéder à des partages de fichiers et à des dossiers Windows, les contraintes suivantes s’appliquent :

- le fichier `CREATE FROM`/`BACKUP TO` n’est pas pris en charge pour les certificats ;
- le certificat `CREATE`/`BACKUP` de `FILE`/`ASSEMBLY` n’est pas pris en charge ; Les fichiers de clés privés ne peuvent pas être utilisés 

Consultez [CREATE CERTIFICATE](/sql/t-sql/statements/create-certificate-transact-sql) et [BACKUP CERTIFICATE](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Solution de contournement** : Au lieu de créer une sauvegarde du certificat et de restaurer la sauvegarde, [obtenez le contenu binaire du certificat et la clé privée, stockez-les en tant que fichier .sql et créez à partir du binaire](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database) :

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Informations d'identification

Seuls Azure Key Vault et les identités `SHARED ACCESS SIGNATURE` sont pris en charge. Les utilisateurs de Windows ne sont pas pris en charge.

Consultez [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql) et [ALTER CREDENTIAL](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Fournisseurs de chiffrement

Une instance managée ne pouvant pas accéder aux fichiers, vous ne pouvez pas créer de fournisseur de chiffrement :

- `CREATE CRYPTOGRAPHIC PROVIDER` n’est pas pris en charge. Consultez [CREATE CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` n’est pas pris en charge. Consultez [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Connexions et utilisateurs

- Les connexions SQL créées à l’aide de `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY` et `FROM SID` sont prises en charge. Consultez [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql).
- Les principaux de serveur (connexions) Azure Active Directory (Azure AD) créés avec la syntaxe [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) ou la syntaxe [CREATE USER FROM LOGIN [Azure AD Login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) sont pris en charge. Ces connexions sont créées au niveau du serveur.

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

- L’exportation/importation de bases de données à l’aide de fichiers bacpac est prise en charge pour les utilisateurs Azure AD dans Managed Instance avec [SSMS v18.4 ou version ultérieure](/sql/ssms/download-sql-server-management-studio-ssms) et [SQLPackage.exe](/sql/tools/sqlpackage-download).
  - Les configurations suivantes sont prises en charge avec le fichier de base de données bacpac : 
    - Exporter/importer une base de données entre différentes Managed Instance dans le même domaine Azure AD.
    - Exporter une base de données à partir de Managed Instance et l’importer dans SQL Database au sein du même domaine Azure AD. 
    - Exporter une base de données à partir de SQL Database et l’importer dans Managed Instance au sein du même domaine Azure AD.
    - Exporter une base de données à partir de Managed Instance et l’importer dans SQL Server (version 2012 ou ultérieure).
      - Dans cette configuration, tous les utilisateurs Azure AD sont créés en tant que principaux (utilisateurs) de base de données SQL sans connexions. Les utilisateurs sont répertoriés sous le type SQL (SQL_USER dans sys.database_principals). Leurs autorisations et leurs rôles restent dans les métadonnées de la base de données SQL Server et peuvent être utilisés pour l’emprunt d’identité. Toutefois, ils ne peuvent pas être utilisés pour accéder et se connecter à SQL Server à l’aide de leurs informations d’identification.

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

- [Backup master key](/sql/t-sql/statements/backup-master-key-transact-sql) n’est pas pris en charge (géré par le service SQL Database).
- [Restore master key](/sql/t-sql/statements/restore-master-key-transact-sql) n’est pas pris en charge (géré par le service SQL Database).
- [Backup service master key](/sql/t-sql/statements/backup-service-master-key-transact-sql) n’est pas pris en charge (géré par le service SQL Database).
- [Restore service master key](/sql/t-sql/statements/restore-service-master-key-transact-sql) n’est pas pris en charge (géré par le service SQL Database).

## <a name="configuration"></a>Configuration

### <a name="buffer-pool-extension"></a>Extension du pool de mémoires tampons

- L’[extension du pool de mémoires tampons](/sql/database-engine/configure-windows/buffer-pool-extension) n’est pas prise en charge.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` n’est pas pris en charge. Consultez [ALTER SERVER CONFIGURATION](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Collation

Le classement d’instance par défaut est `SQL_Latin1_General_CP1_CI_AS` et peut être spécifié comme paramètre de création. Consultez [Classements](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Niveaux de compatibilité

- Les niveaux de compatibilité pris en charge sont 100, 110, 120, 130, 140 et 150.
- Les niveaux de compatibilité inférieurs à 100 ne sont pas pris en charge.
- Le niveau de compatibilité par défaut est de 140 pour les nouvelles bases de données. Pour les bases de données restaurées, le niveau de compatibilité reste inchangé s’il était de 100 et plus.

Consultez [Niveau de compatibilité ALTER TABLE (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Mise en miroir de bases de données

La mise en miroir de bases de données n’est pas prise en charge.

- Les options `ALTER DATABASE SET PARTNER` et `SET WITNESS` ne sont pas prises en charge.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` n’est pas pris en charge.

Pour plus d’informations, consultez [ALTER DATABASE SET PARTNER AND SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) et [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

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

Pour plus d’informations, consultez [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql).

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

Pour en savoir plus, consultez [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

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

Pour plus d’informations sur SQL Server Agent, consultez [SQL Server Agent](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tables

Les types de tables suivantes ne sont pas prises en charge :

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) (Polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (non pris en charge uniquement dans le niveau usage général)

Pour plus d’informations sur la façon de créer et de modifier des tables, consultez [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) et [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Fonctionnalités

### <a name="bulk-insert--openrowset"></a>Bulk insert/OPENROWSET

Une instance managée ne pouvant pas accéder à des partages de fichiers et à des dossiers Windows, les fichiers doivent être importés à partir du stockage Blob Azure :

- `DATASOURCE` est requis dans la commande `BULK INSERT` lors de l’importation des fichiers depuis le Stockage Blob Azure. Consultez [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` est nécessaire dans la fonction `OPENROWSET` lorsque vous lisez le contenu d’un fichier à partir du stockage Blob Azure. Consultez [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` peut être utilisé pour lire des données à partir d’autres bases de données Azure SQL, instances managées ou instances de SQL Server. Les autres sources, telles que les bases de données Oracle ou les fichiers Excel, ne sont pas prises en charge.

### <a name="clr"></a>CLR

Une instance managée ne pouvant pas accéder à des partages de fichiers et à des dossiers Windows, les contraintes suivantes s’appliquent :

- Seul `CREATE ASSEMBLY FROM BINARY` est pris en charge. Voir [CREATE ASSEMBLY FROM BINARY](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` n’est pas pris en charge. Consultez [CREATE ASSEMBLY FROM FILE](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` ne peut pas référencer des fichiers. Consultez [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Database Mail (db_mail)
 - `sp_send_dbmail` ne peut pas envoyer de pièces jointes à l’aide du paramètre @file_attachments. Le système de fichiers local ainsi que les partages externes ou le Stockage Blob Azure ne sont pas accessibles avec cette procédure.
 - Consultez les problèmes connus liés au paramètre `@query` et à l’authentification.
 
### <a name="dbcc"></a>DBCC

Les instructions DBCC non documentées qui sont activées dans SQL Server ne sont pas prises en charge dans les instances managées.

- Seuls un nombre limité d’indicateurs de trace globaux sont pris en charge. Les `Trace flags` de session ne sont pas pris en charge. Consultez les [indicateurs de trace](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) et [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) fonctionnent avec le nombre limité d’indicateurs de trace globaux.
- [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) ne peut pas être utilisé avec les options REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST et REPAIR_REBUILD, car la base de données ne peut pas être définie sur le mode `SINGLE_USER`. Consultez l’article sur les [différences avec ALTER DATABASE](#alter-database-statement). Les endommagements potentiels de la base de données sont gérés par l’équipe de support Azure. Contactez le support Azure si vous constatez un endommagement de la base de données qui doit être corrigé.

### <a name="distributed-transactions"></a>Transactions distribuées

MSDTC et les [transactions élastiques](sql-database-elastic-transactions-overview.md) ne sont actuellement pas pris en charge dans les instances managées.

### <a name="extended-events"></a>Événements étendus

Certaines cibles propres à Windows pour les événements étendus (XEvent) ne sont pas prises en charge :

- Le `etw_classic_sync` cible n’est pas pris en charge. Stockez les fichiers `.xel` dans le stockage Blob Azure. Consultez [etw_classic_sync target](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Le `event_file` cible n’est pas pris en charge. Stockez les fichiers `.xel` dans le stockage Blob Azure. Consultez [event_file target](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Bibliothèques externes

Les bibliothèques externes R et Python en base de données ne sont pas encore prises en charge. Consultez [SQL Server Machine Learning Services](/sql/advanced-analytics/r/sql-server-r-services).

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

Pour plus d’informations, consultez [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) et [FileTables](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Recherche sémantique de texte intégral

La [recherche sémantique](/sql/relational-databases/search/semantic-search-sql-server) n’est pas prise en charge.

### <a name="linked-servers"></a>Services liés

Les serveurs liés dans des instances gérées prennent en charge un nombre limité de cibles :

- Les cibles prises en charge sont les instances managées, les bases de données uniques et les instances de SQL Server. 
- Les serveurs liés ne prennent pas en charge les transactions accessibles en écriture distribuées (MS DTC).
- Les cibles qui ne sont pas prises en charge sont des fichiers, Analysis Services et d’autres SGBDR. Essayez d’utiliser l’importation CSV native à partir de Stockage Blob Azure à l’aide de `BULK INSERT` ou `OPENROWSET` comme alternative pour l’importation de fichiers.

Opérations

- Les transactions d’écriture entre instances ne sont pas prises en charge.
- `sp_dropserver` est pris en charge pour supprimer un serveur lié. Consultez [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- La fonction `OPENROWSET` peut être utilisée pour exécuter des requêtes uniquement sur des instances SQL Server. Elles peuvent être managées, locales ou dans des machines virtuelles. Consultez [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- La fonction `OPENDATASOURCE` peut être utilisée pour exécuter des requêtes uniquement sur des instances SQL Server. Elles peuvent être managées, locales ou dans des machines virtuelles. Seules les valeurs `SQLNCLI`, `SQLNCLI11` et `SQLOLEDB` sont prises en charge en tant que fournisseur. Par exemple `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Consultez [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Les serveurs liés ne peuvent pas être utilisés pour lire des fichiers (Excel, CSV) à partir des partages réseau. Essayez d’utiliser [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) ou [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) qui lit les fichiers CSV à partir de Stockage Blob Azure. Suivez ces requêtes sur l’[élément de commentaires de Managed Instance](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Les tables externes qui référencent les fichiers dans HDFS ou le stockage Blob Azure ne sont pas prises en charge. Pour plus d’informations sur PolyBase, consultez [PolyBase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Réplication

- Les types de réplication d’instantané et bidirectionnelle sont pris en charge. La réplication de fusion, la réplication d’égal à égal et les abonnements modifiables ne sont pas pris en charge.
- La [réplication transactionnelle](sql-database-managed-instance-transactional-replication.md) est disponible en préversion publique dans Managed Instance sous certaines contraintes :
    - Tous les types de participants à la réplication (serveur de publication, serveur de distribution, abonné d’extraction et abonné de type push) peuvent être placés sur des instances gérées, mais le serveur de publication et le serveur de distribution doivent être tous deux dans le cloud ou locaux.
    - Managed Instance peut communiquer avec les versions récentes de SQL Server. Consultez les versions prises en charge [ici](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems).
    - La réplication transactionnelle présente des [exigences de mise en réseau supplémentaires](sql-database-managed-instance-transactional-replication.md#requirements).

Pour plus d’informations sur la configuration de la réplication, consultez le [didacticiel de réplication](replication-with-sql-database-managed-instance.md).


Si la réplication est activée sur une base de données dans un [groupe de basculement](sql-database-auto-failover-group.md), l’administrateur de l’instance managée doit nettoyer toutes les publications de l’ancien principal et les reconfigurer sur le nouveau principal après un basculement. Les activités suivantes sont nécessaires dans ce scénario :

1. Arrêtez tous les travaux de réplication en cours d’exécution sur la base de données, le cas échéant.
2. Supprimez les métadonnées d’abonnement du serveur de publication en exécutant le script suivant sur la base de données du serveur de publication :

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Supprimez les métadonnées d’abonnement de l’abonné. Exécutez le script suivant dans la base de données d’abonnement sur l’instance de l’abonné :

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Supprimez définitivement tous les objets de réplication du serveur de publication en exécutant le script suivant dans la base de données publiée :

   ```sql
   EXEC sp_removedbreplication
   ```

1. Supprimez définitivement l’ancien serveur de distribution à partir de l’instance principale d’origine (en cas de basculement vers un ancien principal qui utilisait un serveur de distribution). Exécutez le script suivant sur la base de données de référence dans l’ancienne instance gérée du serveur de distribution :

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

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
- `ASYNC RESTORE`: la restauration continue même si la connexion cliente s’arrête. Si votre connexion est interrompue, vous pouvez vérifier l’affichage `sys.dm_operation_status` pour l’état d’une opération de restauration, et pour la création et la suppression d’une base de données. Consultez [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Les options de base de données suivantes sont fixées ou remplacées et ne peuvent pas être modifiées ultérieurement : 

- `NEW_BROKER` si le broker n’est pas activé dans le fichier .bak. 
- `ENABLE_BROKER` si le broker n’est pas activé dans le fichier .bak. 
- `AUTO_CLOSE=OFF` si une base de données dans le fichier .bak comprend `AUTO_CLOSE=ON`. 
- `RECOVERY FULL` si une base de données dans le fichier .bak a le mode de récupération `SIMPLE` ou `BULK_LOGGED`.
- Un groupe de fichiers à mémoire optimisée est ajouté et appelé XTP s’il n’était pas dans le fichier .bak source. 
- Tout groupe de fichiers à mémoire optimisée existant est renommé XTP. 
- Les options `SINGLE_USER` et `RESTRICTED_USER` sont changées en `MULTI_USER`.

Limites : 

- Les sauvegardes des bases de données endommagées peuvent être restaurées en fonction du type d’altération, mais les sauvegardes automatisées ne sont pas effectuées tant que la corruption n’est pas corrigée. Assurez-vous que vous exécutez `DBCC CHECKDB` sur l’instance source et utilisez la sauvegarde `WITH CHECKSUM` afin d’éviter ce problème.
- La restauration d’un fichier `.BAK` d’une base de données qui contient une limitation décrite dans ce document (par exemple, `FILESTREAM` ou des objets `FILETABLE`) ne peut pas être restaurée sur Managed instance.
- Les fichiers `.BAK` qui contiennent plusieurs jeux de sauvegarde ne peuvent pas être restaurés. 
- Les fichiers `.BAK` qui contiennent plusieurs fichiers journaux ne peuvent pas être restaurés.
- Les sauvegardes contenant des bases de données dont la taille excède 8 To, contenant des objets OLTP en mémoire actifs ou comprenant plus de 280 fichiers par instance ne peuvent pas être restaurées sur une instance d’usage général. 
- Les sauvegardes contenant des bases de données dont la taille excède 4 To ou contenant des objets OLTP en mémoire dont la taille totale est supérieure à la taille spécifiée dans les [limites de ressources](sql-database-managed-instance-resource-limits.md) ne peuvent pas être restaurées sur une instance critique pour l’entreprise.
Pour plus d’informations sur les instructions de restauration, consultez [Instructions RESTORE](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Les mêmes limitations s’appliquent à une opération de restauration dans le temps intégrée. À titre d’exemple, une base de données à usage général dont la taille est supérieure à 4 To ne peut pas être restaurée sur une instance critique pour l’entreprise. Une base de données critique pour l’entreprise comprenant des fichiers OLTP en mémoire ou plus 280 fichiers ne peut pas être restaurée sur une instance à usage général.

### <a name="service-broker"></a>Service broker

Le Service Broker entre instances n’est pas pris en charge :

- `sys.routes`: Comme prérequis, vous devez sélectionner l’adresse à partir de sys.routes. L’adresse doit être LOCAL sur tous les itinéraires. Consultez [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: vous ne pouvez pas utiliser `CREATE ROUTE` avec `ADDRESS` si la valeur de celle-ci est différente de `LOCAL`. Consultez [CREATE ROUTE](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: vous ne pouvez pas utiliser `ALTER ROUTE` avec `ADDRESS` si la valeur de celle-ci est différente de `LOCAL`. Consultez [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Procédures stockées, fonctions et déclencheurs

- `NATIVE_COMPILATION` n’est pas pris en charge dans le niveau Usage général.
- Les options [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) suivantes ne sont pas prises en charge : 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` n’est pas pris en charge. Consultez [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` n’est pas pris en charge. Consultez [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` ne sont pas pris en charge, ce qui inclut `sp_addextendedproc` et `sp_dropextendedproc`. Consultez [Procédures stockées étendues](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` et `sp_detach_db` ne sont pas pris en charge. Consultez [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) et [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Fonctions et variables système

Les variables, fonctions et vues suivantes retournent des résultats différents :

- `SERVERPROPERTY('EngineEdition')` retourne la valeur 8. Cette propriété identifie une instance gérée de façon unique. Consultez [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` retourne la valeur NULL, car le concept d’instance tel qu’il existe pour SQL Server ne s’applique pas à une instance managée. Consultez [SERVERPROPERTY(« InstanceName »)](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` retourne un nom DNS « connectable » complet, par exemple, my-managed-instance.wcus17662feb9ce98.database.windows.net. Consultez [@@SERVERNAME](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` retourne le nom DNS « connectable » complet, tel que `myinstance.domain.database.windows.net` pour les propriétés « name » et « data_source ». Consultez [SYS. SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` retourne la valeur NULL, car le concept de service tel qu’il existe pour SQL Server ne s’applique pas à une instance managée. Consultez [@@SERVICENAME](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` est pris en charge. Il retourne NULL si la connexion Azure AD n’est pas dans sys.syslogins. Consultez [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` n’est pas pris en charge. Les données incorrectes sont retournées, ce qui est un problème temporaire connu. Consultez [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Environment"></a>Contraintes d’environnement

### <a name="subnet"></a>Subnet
-  Vous ne pouvez pas placer d’autres ressources (par exemple des machines virtuelles) dans le sous-réseau sur lequel vous avez déployé votre instance gérée. Déployez ces ressources à l’aide d’un sous-réseau différent.
- Le sous-réseau doit avoir un nombre suffisant d’[adresses IP](sql-database-managed-instance-connectivity-architecture.md#network-requirements) disponibles. Le nombre minimal est 16, mais la recommandation est de disposer d’au moins 32 adresses IP dans le sous-réseau.
- [Les points de terminaison de service ne peuvent pas être associés au sous-réseau de l’instance managée](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Vérifiez que l’option Points de terminaison de service est désactivée quand vous créez le réseau virtuel.
- Le nombre de vCores et de types d’instances que vous pouvez déployer dans une région ont certaines [contraintes et limites](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
- Il existe certaines [règles de sécurité qui doivent être appliquées sur le sous-réseau](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>Réseau virtuel
- Le réseau virtuel peut être déployé à l’aide du modèle de ressource ; le modèle classique pour réseau virtuel n’est pas pris en charge.
- Après la création d’une instance managée, le déplacement de l’instance managée ou du réseau virtuel vers un autre groupe de ressources ou vers un autre abonnement n’est pas pris en charge.
- Certains services, tels que App Service Environment, Logic Apps et Managed Instance (utilisés pour la géoréplication, la réplication transactionnelle ou via des serveurs liés), ne peuvent pas accéder aux instances Managed Instance dans des régions différentes si leurs réseaux virtuels sont connectés au moyen du [Peering mondial](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Vous pouvez vous connecter à ces ressources via ExpressRoute ou une connexion entre deux réseaux virtuels, par l’intermédiaire de passerelles de réseau virtuel.

### <a name="tempdb"></a>TEMPDB

La taille de fichier maximale de `tempdb` ne peut pas être supérieure à 24 Go par cœur sur un niveau Usage général. La taille maximale de `tempdb` sur un niveau Critique pour l’entreprise est limitée à la taille de stockage d’instance. La taille du fichier journal `Tempdb` est limitée à 120 Go sur le niveau Usage général. Certaines requêtes peuvent retourner une erreur si elles ont besoin de plus de 24 Go par cœur dans `tempdb` ou si elles produisent plus de 120 Go de données de journal.

### <a name="error-logs"></a>Des journaux d’activité d’erreurs

Une instance managée ajoute des informations détaillées dans les journaux des erreurs. Beaucoup d’événements système internes sont journalisés dans le journal des erreurs. utilisez une procédure personnalisée pour lire les journaux des erreurs en excluant les entrées non pertinentes. Pour plus d’informations, consultez [Instance managée – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) ou [Extension d’instance managée (préversion)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) pour Azure Data Studio.

## <a name="Issues"></a> Problèmes connus

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Les limites de mémoire OLTP en mémoire ne sont pas appliquées

**Date :** Octobre 2019

Le niveau de service Critique pour l'entreprise n’appliquera pas correctement les [limites max de mémoire pour les objets à mémoire optimisée ](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) dans certains cas. L’instance managée peut permettre à la charge de travail d’utiliser davantage de mémoire pour les opérations OLTP en mémoire, ce qui peut affecter la disponibilité et la stabilité de l’instance. Les requêtes OLTP en mémoire qui atteignent les limites peuvent ne pas échouer immédiatement. Ce problème sera corrigé bientôt. Les requêtes qui utilisent plus de mémoire OLTP en mémoire échoueront plus tôt si elles atteignent les [limites](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Solution de contournement :** [Surveillez l’utilisation du stockage OLTP en mémoire ](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) à l’aide de [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) pour vous assurer que la charge de travail n’utilise pas plus que la mémoire disponible. Augmentez les limites de mémoire qui dépendent du nombre de vCores ou optimisez votre charge de travail pour utiliser moins de mémoire.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Erreur retournée lors de la tentative de suppression d’un fichier qui n’est pas vide

**Date :** Octobre 2019

SQL Server/Managed Instance [ne permettent pas à l’utilisateur de supprimer un fichier qui n’est pas vide](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Si vous tentez de supprimer un fichier de données non vide à l’aide d’une instruction `ALTER DATABASE REMOVE FILE`, l’erreur `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` n’est pas retournée immédiatement. Managed Instance continue à essayer de supprimer le fichier et l’opération échoue après 30 minutes avec `Internal server error`.

**Solution de contournement** : Supprimez le contenu du fichier à l’aide de la commande `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)`. S’il s’agit du seul fichier dans le groupe de fichiers, vous devez supprimer les données de la table ou de la partition associées à ce groupe de fichiers avant de réduire le fichier, et éventuellement charger ces données dans une autre table ou partition.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Les opérations de changement de niveau de service et de création d’instance sont bloquées par la restauration de base de données en cours

**Date :** Septembre 2019

Les instructions `RESTORE` en cours, le processus de migration Data Migration Service et la limite de restauration dans le temps intégrée bloquent la mise à jour du niveau de service ou le redimensionnement de l’instance existante et la création de nouvelles instances jusqu’à la fin du processus de restauration. Le processus de restauration bloquera ces opérations sur les instances managées et les pools d’instances sur le sous-réseau où le processus de restauration est en cours d’exécution. Les instances dans les pools d’instances ne sont pas affectées. Les opérations de création ou de changement du niveau de service n’échoueront pas et n’expireront pas : elles se poursuivront une fois le processus de restauration terminé ou annulé.

**Solution de contournement** : Attendez la fin du processus de restauration ou annulez-le si l’opération de création ou de mise à jour du niveau de service a une priorité plus élevée.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Il peut être nécessaire de reconfigurer Resource Governor sur le niveau de service Critique pour l’entreprise après le basculement

**Date :** Septembre 2019

Il se peut que la fonctionnalité [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) qui permet de limiter les ressources affectées aux charges de travail utilisateur classe erronément une charge de travail utilisateur après un basculement ou une modification du niveau de service apportée par un utilisateur (par exemple, la modification du nombre maximal de vCores ou de la taille maximale de stockage d’instance).

**Solution de contournement** : Exécutez `ALTER RESOURCE GOVERNOR RECONFIGURE` régulièrement ou dans le cadre du travail de l’agent SQL qui exécute la tâche SQL lorsque l’instance démarre si vous utilisez [Resource Governor](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>Les boîtes de dialogue Service Broker utilisées entre plusieurs bases de données doivent être réinitialisées après la mise à niveau du niveau de service

**Date :** août 2019

Les boîtes de dialogue Service Broker utilisées entre plusieurs bases de données cessent de transmettre les messages aux services dans d’autres bases de données après un changement du niveau de service. Les messages ne sont **pas perdus** et peuvent être retrouvés dans la file d’attente de l’expéditeur. Toute modification du nombre de vCores ou de la taille de stockage des instances dans Managed Instance entraîne le changement de la valeur `service_broke_guid` affichée dans [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) pour toutes les bases de données. Tout élément `DIALOG` créé à l’aide de l’instruction [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) qui référence des répartiteurs Service Brokers dans une autre base de données cesse de remettre les messages au service cible.

**Solution de contournement :** arrêtez toutes les activités qui utilisent des conversations de boîtes de dialogue Service Broker entre plusieurs bases de données avant de mettre à jour le niveau de service et réinitialisez-les ensuite. S’il reste des messages non transmis après le changement de niveau de service, consultez les messages en question dans la file d’attente source et renvoyez-les à la file d’attente cible.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>L’emprunt d’identité des types de connexion Azure AD n’est pas pris en charge

**Date :** Juillet 2019

L’emprunt d’identité avec `EXECUTE AS USER` ou `EXECUTE AS LOGIN` des principaux AAD suivants n’est pas pris en charge :
-   Utilisateurs AAD avec alias. L’erreur suivante est retournée dans ce cas `15517`.
- Connexions et utilisateurs AAD basés sur des applications ou des principaux de service AAD. Les erreurs suivantes sont retournées dans ces cas `15517` et `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>Le paramètre @query n’est pas pris en charge dans sp_send_db_mail

**Date :** Avril 2019

Le paramètre `@query` dans la procédure [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) ne fonctionne pas.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>La réplication transactionnelle doit être reconfigurée après un basculement géographique

**Date :** mars 2019

Si la réplication transactionnelle est activée sur une base de données dans un groupe de basculement automatique, l’administrateur de l’instance managée doit nettoyer toutes les publications de l’ancien principal et les reconfigurer sur le nouveau principal après un basculement vers une autre région. Pour plus d’informations, consultez [Réplication](#replication).

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Les utilisateurs et connexions AAD ne sont pas pris en charge dans SSDT

**Date :** Nov 2019

SQL Server Data Tools ne prend pas complètement en charge les utilisateurs et les connexions Azure Active Directory.

### <a name="temporary-database-is-used-during-restore-operation"></a>Une base de données temporaire est utilisée d’une opération de restauration

Lors de la restauration d’une base de données sur Managed Instance, le service de restauration commence par créer une base de données vide du nom souhaité pour allouer le nom sur l’instance. Après un certain temps, cette base de données est supprimée et la restauration de la base de données réelle commence. Une valeur GUID aléatoire est allouée temporairement, plutôt qu’un nom, à la base de données en état de *restauration*. Le nom temporaire est remplacé par le nom spécifié dans l’instruction `RESTORE` une fois le processus de restauration terminé. Au cours de la phase initiale, l’utilisateur peut accéder à la base de données vide et même créer des tables ou charger des données dans celle-ci. Cette base de données temporaire est supprimée lorsque le service de restauration démarre la deuxième phase.

**Solution de contournement** : N’accédez pas à la base de données que vous restaurez tant que la restauration n’est pas terminée.

### <a name="tempdb-structure-and-content-is-re-created"></a>La structure et le contenu de TEMPDB sont recréés

La base de données `tempdb` est toujours fractionnée en 12 fichiers de données, et cette structure de fichiers ne peut pas être modifiée. La taille maximale par fichier n’est pas modifiable, et il n’est pas possible d’ajouter de nouveaux fichiers dans `tempdb`. `Tempdb` est toujours recréé en tant que base de données vide lorsque l’instance démarre ou bascule, et les modifications apportées dans `tempdb` ne sont pas conservées.

### <a name="exceeding-storage-space-with-small-database-files"></a>Dépassement de l’espace de stockage avec des fichiers de base de données de petite taille

Les instructions `CREATE DATABASE`, `ALTER DATABASE ADD FILE` et `RESTORE DATABASE` risquent d’échouer, car l’instance peut atteindre la limite de Stockage Azure.

Chaque instance managée Usage général dispose de 35 To de stockage réservé pour l’espace disque Premium Azure. Chaque fichier de base de données est placé sur un disque physique distinct. Les tailles de disque peuvent être de 128 Go, 256 Go, 512 Go, 1 To ou 4 To. L’espace non utilisé sur le disque n’est pas facturé, mais la somme des tailles des disques Premium Azure ne peut pas dépasser 35 To. Dans certains cas, une instance managée qui n’a pas besoin de 8 To au total peut dépasser la limite Azure de 35 To en taille de stockage à cause d’une fragmentation interne.

Par exemple, une instance managée à usage général peut avoir un fichier d’une taille de 1,2 To placé sur un disque de 4 To. Elle peut également comporter 248 fichiers, d’une taille de 1 Go chacun, qui sont placés sur des disques distincts de 128 Go. Dans cet exemple :

- La taille totale du stockage de disque alloué est de 1 x 4 To + 248 x 128 Go = 35 To.
- L’espace total réservé pour les bases de données sur l’instance est de 1 x 1,2 To + 248 x 1 Go = 1,4 To.

Cet exemple montre que, dans certaines circonstances, du fait d’une répartition spécifique des fichiers, une instance managée peut atteindre la limite des 35 To réservée pour un disque Premium Azure attaché, sans que vous vous y attendiez.

Dans cet exemple, les bases de données existantes continuent de fonctionner et peuvent croître sans aucun problème du moment que de nouveaux fichiers ne sont pas ajoutés. La création ou la restauration de bases de données est impossible, car il n’y a pas suffisamment d’espace pour les nouveaux lecteurs de disque, même si la taille totale de toutes les bases de données n’atteint pas la limite de taille d’instance. L’erreur qui est retournée dans ce cas n’est pas claire.

Vous pouvez [identifier le nombre de fichiers restants](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) à l’aide de vues système. Si vous atteignez cette limite, essayez de [vider et de supprimer certains fichiers plus petits au moyen de l’instruction DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file), ou basculez vers le [niveau Critique pour l’entreprise, qui ne connaît pas cette limite](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Affichage des valeurs GUID à la place des noms de base de données

Plusieurs vues système, compteurs de performances, messages d’erreur, événements XEvent et entrées du journal des erreurs affichent des identificateurs de base de données GUID au lieu d’afficher les noms des bases de données. Ne prenez pas en compte ces identificateurs GUID, car ils vont être remplacés à l’avenir par les noms des bases de données.

### <a name="error-logs-arent-persisted"></a>Les journaux des erreurs ne sont pas conservés

Les journaux des erreurs qui sont disponibles dans Managed Instance ne sont pas persistants, et leur taille n’est pas comprise dans la limite de stockage maximale. Les journaux des erreurs peuvent être automatiquement effacés si un basculement se produit. Il peut y avoir des écarts dans l’historique du journal des erreurs, car Managed Instance a été déplacé plusieurs fois sur plusieurs machines virtuelles.

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

**Solution de contournement :** servez-vous de [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) pour utiliser une autre base de données dans un contexte de connexion au lieu d’utiliser deux connexions.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Les modules CLR et les serveurs liés parfois ne parviennent pas à référencer une adresse IP locale

Il arrive que des modules CLR placés dans une instance managée, et que des serveurs liés ou des requêtes distribuées référençant une instance active, ne parviennent pas quelquefois à résoudre l’adresse IP d’une instance locale. Cette erreur est un problème temporaire.

**Solution de contournement :** utilisez des connexions contextuelles dans un module CLR, si possible.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les instances managées, consultez [Présentation de Managed Instance](sql-database-managed-instance.md).
- Pour consulter la liste des fonctionnalités et les comparer, voir [Comparaison des fonctionnalités Azure SQL Database](sql-database-features.md).
- Pour obtenir un guide de démarrage rapide qui montre comment créer une instance managée, voir [Créer une instance managée](sql-database-managed-instance-get-started.md).
