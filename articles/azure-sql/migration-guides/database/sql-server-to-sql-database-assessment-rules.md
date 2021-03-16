---
title: Règles d’évaluation pour la migration de SQL Server vers SQL Database
description: Règles d’évaluation pour identifier les problèmes liés à l’instance SQL Server source qui doivent être résolus avant la migration vers Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: bf825572226bf5d7432fd3ad825f2f3a13355c53
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054088"
---
# <a name="assessment-rules-for-sql-server-to-sql-database-migration"></a>Règles d’évaluation pour la migration de SQL Server vers SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Les outils de migration valident votre instance de SQL Server source en exécutant un certain nombre de règles d’évaluation afin d’identifier les problèmes à résoudre avant la migration de votre base de données SQL Server vers Azure SQL Database. 

Cet article fournit la liste des règles utilisées pour évaluer la faisabilité de la migration de votre base de données SQL Server vers Azure SQL Database. 


## <a name="bulk-insert"></a>Bulk insert<a id="BulkInsert"></a>

**Titre : L’instruction BULK INSERT avec une source de données blob non Azure n’est pas prise en charge dans Azure SQL Database.**    
**Catégorie**: Problème   

**Description**   
Azure SQL Database ne peut pas accéder à des partages de fichiers ou à des dossiers Windows. Consultez la section « Objets affectés » pour connaître les utilisations spécifiques des instructions BULK INSERT qui ne font pas référence à un blob Azure. Des objets avec l’instruction « BULK INSERT » où la source n’est pas Stockage Blob Azure ne fonctionneront pas après migration vers Azure SQL Database. 


**Recommandation**   
Vous devez convertir les instructions BULK INSERT qui utilisent des fichiers locaux ou des partages de fichiers pour qu’elles utilisent des fichiers de Stockage Blob Azure à la place, pendant la migration vers Azure SQL Database. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure.

## <a name="compute-clause"></a>Clause Compute<a id="ComputeClause"></a>

**Titre : la clause COMPUTE n’est plus disponible et a été supprimée.**    
**Catégorie** : Avertissement   

**Description**   
La clause COMPUTE génère des totaux qui s’affichent sous la forme de colonnes de résumé supplémentaires à la fin du jeu de résultats. Toutefois, cette clause n’est plus prise en charge dans Azure SQL Database. 


**Recommandation**   
Le module T-SQL doit être réécrit en utilisant l’opérateur ROLLUP à la place. Le code ci-dessous montre comment remplacer COMPUTE par ROLLUP : 

```sql 
USE AdventureWorks 
GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP; 
```

Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="change-data-capture-cdc"></a>Capture des changements de données (CDC)<a id="CDC"></a>

**Titre : La capture des changements de données (CDC) n’est pas prise en charge dans Azure SQL Database**   
**Catégorie**: Problème   


**Description**   
La capture des changements de données n’est pas prise en charge dans Azure SQL Database. Évaluez si vous pouvez utiliser Change Tracking à la place.  Vous pouvez également migrer vers Azure SQL Managed Instance ou SQL Server sur des machines virtuelles Azure. 


**Recommandation**   
La capture des changements de données n’est pas prise en charge dans Azure SQL Database. Évaluez si vous pouvez utiliser Change Tracking à la place ou envisagez de migrer vers Azure SQL Managed Instance.

Informations supplémentaires : [Activer Change Tracking dans SQL Azure](https://social.technet.microsoft.com/wiki/contents/articles/2976.azure-sql-how-to-enable-change-tracking.aspx)

## <a name="clr-assemblies"></a>Assemblys CLR<a id="ClrAssemblies"></a>

**Titre : Les assemblys SQL CLR ne sont pas pris en charge dans Azure SQL Database**   
**Catégorie**: Problème   


**Description**   
Azure SQL Database ne prend pas en charge les assemblys SQL CLR. 


**Recommandation**   
Actuellement, il n’existe aucun moyen de parvenir à cela dans Azure SQL Database. Les solutions alternatives recommandées requièrent un code d’application et des modifications de base de données pour utiliser uniquement les assemblys pris en charge par Azure SQL Database. Vous pouvez également migrer vers Azure SQL Managed Instance ou SQL Server sur des machines virtuelles Azure.

Informations supplémentaires : [Différences Transact-SQL non prises en charge dans SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)

## <a name="cryptographic-provider"></a>Fournisseur de chiffrement<a id="CryptographicProvider"></a>

**Titre : Nous avons identifié une utilisation de CREATE CRYPTOGRAPHIC PROVIDER ou ALTER CRYPTOGRAPHIC PROVIDER, ce qui n’est pas pris en charge dans Azure SQL Database**   
**Catégorie**: Problème   

**Description**   
Le service Azure SQL Database ne prend pas en charge les instructions CRYPTOGRAPHIC PROVIDER, car il ne peut pas accéder aux fichiers. Consultez la section Objets affectés pour connaître les utilisations spécifiques des instructions CRYPTOGRAPHIC PROVIDER. Les objets avec `CREATE CRYPTOGRAPHIC PROVIDER` ou `ALTER CRYPTOGRAPHIC PROVIDER` ne fonctionneront pas correctement après migration vers Azure SQL Database.  


**Recommandation**   
Passez en revue les objets avec `CREATE CRYPTOGRAPHIC PROVIDER` ou `ALTER CRYPTOGRAPHIC PROVIDER` . Dans tous les objets de ce type requis, supprimez les utilisations de ces fonctionnalités. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure

## <a name="cross-database-references"></a>Références des bases de données croisées<a id="CrossDataseReferences"></a>

**Titre : Les requêtes de bases de données croisées ne sont pas prises en charge dans Azure SQL Database**   
**Catégorie**: Problème   

**Description**   
Les bases de données sur ce serveur utilisent des requêtes de bases de données croisées qui ne sont pas prises en charge dans Azure SQL Database. 


**Recommandation**   
Azure SQL Database ne prend pas en charge les requêtes de bases de données croisées. Les actions suivantes sont recommandées : 
- Migrez la ou les bases de données dépendantes vers Azure SQL Database, et utilisez la fonctionnalité Requête de base de données élastique (actuellement en préversion) pour interroger les bases de données SQL Azure. 
- Déplacez les jeux de données dépendants d’autres bases de données dans la base de données en cours de migration. 
- Migrez vers Azure SQL Managed Instance.
- Migrez vers SQL Server sur une machine virtuelle Azure. 

Informations supplémentaires : [Découvrir la requête de base de données élastique Azure SQL Database (version préliminaire)](../../database/elastic-query-overview.md)

## <a name="database-compatibility"></a>Compatibilité de base de données<a id="DbCompatLevelLowerThan100"></a>

**Titre : Azure SQL Database ne prend pas en charge les niveaux de compatibilité inférieurs à 100.**    
**Catégorie** : Avertissement   

**Description**   
Le Niveau de compatibilité de la base de données est un outil précieux quand il s’agit de moderniser une base de données. Il permet en effet de mettre à niveau le Moteur de base de données SQL Server tout en continuant de connecter l’état opérationnel des applications en conservant le niveau de compatibilité de la base de données antérieur à la mise à niveau. Azure SQL Database ne prend pas en charge les niveaux de compatibilité inférieurs à 100. 


**Recommandation**   
Évaluez si la fonctionnalité de l’application est intacte quand le niveau de compatibilité de la base de données est porté à 100 sur Azure SQL Database. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure

## <a name="database-mail"></a>Messagerie de base de données<a id="DatabaseMail"></a>

**Titre : Database Mail n’est pas pris en charge dans Azure SQL Database.**    
**Catégorie** : Avertissement   

**Description**   
Ce serveur utilise la fonctionnalité Database Mail, qui n’est pas prise en charge dans Azure SQL Database.


**Recommandation**   
Envisagez de migrer vers Azure SQL Database qui prend en charge Database Mail.  Vous pouvez également envisager d’utiliser Azure Functions et Sendgrid pour obtenir la fonctionnalité de courrier sur Azure SQL Database.

Informations supplémentaires : [Envoyer un e-mail à partir d’Azure SQL Database à l’aide d’un script Azure Functions](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)


## <a name="database-principal-alias"></a>Alias principal de base de données<a id="DatabasePrincipalAlias"></a>

**Titre : SYS. DATABASE_PRINCIPAL_ALIASES n’est plus disponible et a été supprimé.**    
**Catégorie**: Problème   

**Description**   
SYS.DATABASE_PRINCIPAL_ALIASES n’est plus disponible et a été supprimé dans Azure SQL Database.  


**Recommandation**   
Utilisez des rôles à la place d'alias.

Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="disable_def_cnst_chk-option"></a>Option DISABLE_DEF_CNST_CHK<a id="DisableDefCNSTCHK"></a>

**Titre : L’option SET DISABLE_DEF_CNST_CHK n’est plus disponible et a été supprimée.**    
**Catégorie**: Problème   

**Description**   
L’option SET DISABLE_DEF_CNST_CHK n’est plus disponible et a été supprimée dans Azure SQL Database.  


Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW, indicateur<a id="FastFirstRowHint"></a>

**Titre : L’indicateur de requête FASTFIRSTROW n’est plus disponible et a été supprimé.**    
**Catégorie** : Avertissement   

**Description**   
L’indicateur de requête FASTFIRSTROW n’est plus disponible et a été supprimé dans Azure SQL Database.  


**Recommandation**   
Au lieu de l’indicateur de requête FASTFIRSTROW, utilisez OPTION (FAST n).

Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**Titre : FileStream n’est pas pris en charge dans Azure SQL Database**   
**Catégorie**: Problème   

**Description**   
La fonctionnalité Filestream qui permet de stocker des données non structurées telles que des documents texte, des images et des vidéos dans le système de fichiers NTFS, n’est pas prise en charge dans Azure SQL Database. 


**Recommandation**   
Chargez les fichiers non structurés dans le service Stockage Blob Azure et stockez les métadonnées associées à ces fichiers (nom, type, adresse URL, clé de stockage, etc.) dans Azure SQL Database. Il se peut que vous deviez remanier votre application pour activer la diffusion en continu de blobs vers et depuis Azure SQL Database. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure.

Informations supplémentaire : [Blog sur la diffusion en continu de blobs vers et depuis SQL Azure](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)


## <a name="linked-server"></a>Serveur lié<a id="LinkedServer"></a>

**Titre : La fonctionnalité de serveur lié n’est pas prise en charge dans Azure SQL Database**   
**Catégorie**: Problème   

**Description**   
Des serveurs liés permettent au Moteur de base de données SQL Server d’exécuter des commandes sur des sources de données OLE DB en dehors de l’instance de SQL Server. 


**Recommandation**   
Azure SQL Database ne prend pas en charge la fonctionnalité de serveur lié. Les actions suivantes sont recommandées pour éliminer le besoin de serveurs liés : 
- Identifiez les jeux de données dépendants à partir de serveurs SQL distants et envisagez de les déplacer dans la base de données en cours de migration. 
- Migrez la ou les bases de données dépendantes vers Azure, et utilisez la fonctionnalité Requête de base de données élastique (préversion) pour interroger les bases de données dans Azure SQL Database. 

Informations supplémentaires : [Découvrir la requête élastique Azure SQL Database (version préliminaire)](../../database/elastic-query-overview.md) 

## <a name="ms-dtc"></a>MS DTC<a id="MSDTCTransactSQL"></a>

**Titre : BEGIN DISTRIBUTED TRANSACTION n’est pas pris en charge dans Azure SQL Database.**    
**Catégorie**: Problème   

**Description**   
La transaction distribuée SQL BEGIN DISTRIBUTED TRANSACTION démarrée par Transact et gérée par Microsoft Distributed Transaction Coordinator (MS DTC) n’est pas prise en charge dans Azure SQL Database.  


**Recommandation**   
Examinez la section Objets affectés dans Azure Migrate pour voir tous les objets utilisant la transaction BEGIN DISTRUBUTED TRANSACTION. Envisagez de migrer les bases de données participantes vers Azure SQL Database où les transactions distribuées sur plusieurs instances sont prises en charge (actuellement en préversion). Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure.

Informations supplémentaires : [Transactions sur plusieurs serveurs pour Azure SQL Database ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="openrowset-bulk"></a>OPENROWSET (en bloc)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Titre : OpenRowSet utilisé dans une opération en bloc avec une source de données de stockage blob non Azure n’est pas pris en charge dans Azure SQL Database.**    
**Catégorie**: Problème   

**Description** OPENROWSET prend en charge les opérations en bloc par l’intermédiaire d’un fournisseur BULK intégré qui permet de lire les données d’un fichier et de les retourner sous la forme d’un ensemble de lignes. OPENROWSET avec une source de données de stockage blob non Azure n’est pas pris en charge dans Azure SQL Database.


**Recommandation**   
Azure SQL Database ne peut pas accéder aux partages de fichiers et aux dossiers Windows. Les fichiers doivent donc être importés à partir de Stockage Blob Azure. Par conséquent, seul le type de blob DATASOURCE est pris en charge dans la fonction OPENROWSET. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure

Informations supplémentaires : [Résolution des différences de Transact-SQL durant la migration vers SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="openrowset-provider"></a>OPENROWSET (fournisseur)<a id="OpenRowsetWithSQLAndNonSQLProvider"></a>

**Titre : OpenRowSet avec un fournisseur SQL ou non-SQL n’est pas pris en charge dans Azure SQL Database.**    
**Catégorie**: Problème   

**Description**   
OpenRowSet avec un fournisseur SQL ou non-SQL est une autre façon d’accéder à des tables dans un serveur lié et constitue une méthode efficace pour vous connecter et accéder à des données distantes en utilisant OLE DB. OpenRowSet avec un fournisseur SQL ou non-SQL n’est pas pris en charge dans Azure SQL Database.


**Recommandation**   
Azure SQL Database prend en charge OPENROWSET uniquement pour l’importation à partir du service Stockage Blob Azure. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure

Informations supplémentaires : [Résolution des différences de Transact-SQL durant la migration vers SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="non-ansi-left-outer-join"></a>Jointure externe gauche non ANSI<a id="NonANSILeftOuterJoinSyntax"></a>

**Titre : La jointure externe gauche de style non ANSI n’est plus disponible et a été supprimée.**    
**Catégorie** : Avertissement   

**Description**   
La jointure externe gauche de style non-ANSI n’est plus disponible et a été supprimée dans Azure SQL Database. 


**Recommandation**   
Utilisez la syntaxe de jointure ANSI.

Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="non-ansi-right-outer-join"></a>Jointure externe droite non ANSI<a id="NonANSIRightOuterJoinSyntax"></a>

**Titre : La jointure externe droite de style non ANSI n’est plus disponible et a été supprimée.**    
**Catégorie** : Avertissement   

**Description**   
La jointure externe droite de style non-ANSI n’est plus disponible et a été supprimée dans Azure SQL Database. 


**Recommandation**   
Utilisez la syntaxe de jointure ANSI.

Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="next-column"></a>Colonne Next<a id="NextColumn"></a>

**Titre : les tables et colonnes nommées NEXT provoquent une erreur dans Azure SQL Database.**    
**Catégorie**: Problème   

**Description**   
Des tables ou colonnes nommées NEXT ont été détectées. Les séquences introduites dans Microsoft SQL Server utilisent la fonction ANSI standard NEXT VALUE FOR. Si une table ou une colonne est nommée NEXT, que la colonne a pour alias VALUE et que l'ANSI standard AS est omis, l'instruction obtenue peut provoquer une erreur.  


**Recommandation**   
Réécrivez les instructions pour inclure le mot clé ANSI standard AS lors de l'utilisation d'alias pour une table ou une colonne. Par exemple, quand une colonne est nommée NEXT et que cette colonne a pour alias VALUE, la requête `SELECT NEXT VALUE FROM TABLE` provoque une erreur et doit être réécrite sous la forme SELECT NEXT AS VALUE FROM TABLE. De même, quand une table est nommée NEXT et que cette table a pour alias VALUE, la requête `SELECT Col1 FROM NEXT VALUE` provoque une erreur et doit être réécrite sous la forme `SELECT Col1 FROM NEXT AS VALUE`.

## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Titre : Les appels de RAISERROR de style hérité doivent être remplacés par des équivalents récents.**    
**Catégorie** : Avertissement   

**Description**   
Les appels RAISERROR comme dans l’exemple ci-dessous sont dits de style hérité parce qu’ils n’incluent pas les virgules et les parenthèses. `RAISERROR 50001 'this is a test'`. Cette méthode d’appel de RAISERROR n’est plus disponible et a été supprimée dans Azure SQL Database. 


**Recommandation**   
Réécrivez l’instruction en utilisant la syntaxe de RAISERROR actuelle ou vérifiez si l’approche moderne de `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` est applicable.

Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="server-audits"></a>Audits du serveur<a id="ServerAudits"></a>

**Titre : Utiliser les fonctionnalités d’audit d’Azure SQL Database pour remplacer les audits du serveur**   
**Catégorie** : Avertissement   

**Description**   
Les audits du serveur ne sont pas pris en charge dans Azure SQL Database.


**Recommandation**   
Envisagez les fonctionnalités d’audit d’Azure SQL Database pour remplacer les audits du serveur.  Azure SQL prend en charge l’audit, et les fonctionnalités sont plus riches que celles de SQL Server. Azure SQL Database peut auditer divers événements et actions de base de données, dont l’accès aux données, les modifications de schéma (DDL), les modifications de données (DML), les comptes, les rôles et les autorisations (DCL, exceptions de sécurité). L’audit Azure SQL Database augmente la capacité de votre organisation à acquérir une compréhension approfondie des événements et modifications qui se produisent au sein de la base de données, dont les mises à jour et les requêtes sur les données. Vous pouvez également migrer vers Azure SQL Managed Instance ou SQL Server sur des machines virtuelles Azure.

Informations supplémentaires : [Audit pour Azure SQL Database ](../../database/auditing-overview.md)

## <a name="server-credentials"></a>Informations d’identification du serveur<a id="ServerCredentials"></a>

**Titre : Les informations d’identification incluses dans l’étendue du serveur ne sont pas prises en charge dans Azure SQL Database**   
**Catégorie** : Avertissement   

**Description**   
Les informations d’identification correspondent à un enregistrement qui contient les informations d’authentification requises pour la connexion à une ressource en dehors de SQL Server. Azure SQL Database prend en charge les informations d’identification de base de données, mais pas celles créées dans l’étendue de SQL Server.   


**Recommandation**   
Azure SQL Database prend en charge les informations d’identification incluses dans l’étendue de la base de données. Convertissez les informations d’identification incluses dans l’étendue du serveur en informations d’identification incluses dans l’étendue de la base de données. Vous pouvez également migrer vers Azure SQL Managed Instance ou SQL Server sur des machines virtuelles Azure.

Informations supplémentaires : [Création d’informations d’identification incluses dans l’étendue de la base de données](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)

## <a name="service-broker"></a>Service Broker<a id="ServiceBroker"></a>

**Titre : La fonctionnalité Service Broker n’est pas prise en charge dans Azure SQL Database**   
**Catégorie**: Problème   

**Description**   
SQL Server Service Broker assure la prise en charge native des applications de messagerie et de mise en file d’attente dans le Moteur de base de données SQL Server. La fonctionnalité Service Broker n’est pas prise en charge dans Azure SQL Database.


**Recommandation**   
La fonctionnalité Service Broker n’est pas prise en charge dans Azure SQL Database. Envisagez de migrer vers Azure SQL Managed Instance qui prend en charge Service Broker au sein de la même instance. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure. 

## <a name="server-scoped-triggers"></a>Déclencheurs inclus dans l’étendue du serveur<a id="ServerScopedTriggers"></a>

**Title : Les déclencheurs inclus dans l’étendue du serveur ne sont pas pris en charge dans Azure SQL Database**   
**Catégorie** : Avertissement   

**Description**   
Un déclencheur est un type spécial de procédure stockée qui s’exécute en réponse à certaines actions sur une table, telles que l’insertion, la suppression ou la mise à jour de données. Les déclencheurs inclus dans l’étendue du serveur ne sont pas pris en charge dans Azure SQL Database. Azure SQL Database ne prend pas en charge les options suivantes pour les déclencheurs : FOR LOGON, ENCRYPTION, WITH APPEND, NOT FOR REPLICATION, EXTERNAL NAME (pas de prise en charge de méthode externe), ALL SERVER Option (déclencheur DDL), Déclencheur sur un événement LOGON (déclencheur Logon), Azure SQL Database ne prend pas en charge les déclencheurs CLR.


**Recommandation**   
Utilisez plutôt des déclencheurs de niveau base de données. Vous pouvez également migrer vers Azure SQL Managed Instance ou SQL Server sur des machines virtuelles Azure.

Informations supplémentaires : [Résolution des différences de Transact-SQL durant la migration vers SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="sql-agent-jobs"></a>Travaux SQL Agent<a id="AgentJobs"></a>

**Titre : Les travaux SQL Server Agent ne sont pas disponibles dans Azure SQL Database**   
**Catégorie** : Avertissement   

**Description**   
SQL Server Agent est un service Microsoft Windows qui exécute des tâches d’administration planifiées, qui sont appelées travaux dans SQL Server. Les travaux SQL Server Agent ne sont pas disponibles dans Azure SQL Database. 


**Recommandation** Utilisez des travaux élastiques (préversion), qui remplacent les travaux SQL Server Agent dans Azure SQL Database. Les travaux de base de données élastiques pour Azure SQL Database vous permettent d’exécuter de manière fiable des scripts T-SQL qui s’étendent sur plusieurs bases de données, tout en effectuant automatiquement de nouvelles tentatives et en apportant des garanties d’accomplissement final. Vous pouvez également envisager de migrer vers Azure SQL Managed Instance ou SQL Server sur des machines virtuelles Azure.

Informations supplémentaires : [Prise en main de Tâches de bases de données élastiques (préversion) ](../../database/elastic-jobs-overview.md)

## <a name="sql-database-size"></a>Taille de base de données SQL<a id="SQLDBDatabaseSize"></a>

**Titre : Azure SQL Database ne prend pas en charge les tailles de base de données supérieures à 100 To.**    
**Catégorie**: Problème   

**Description**   
La taille de la base de données est supérieure à la taille maximale prise en charge de 100 To. 


**Recommandation**   
Évaluez si les données peuvent être archivées compressées ou partitionnées dans plusieurs bases de données. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure. 

Informations supplémentaires : [Limites des ressources vCore](../../database/resource-limits-vcore-single-databases.md) 

## <a name="sql-mail"></a>SQL Mail<a id="SqlMail"></a>

**Titre : SQL Mail n’est plus disponible.**    
**Catégorie** : Avertissement   

**Description**   
SQL Mail n’est plus disponible et a été supprimé dans Azure SQL Database.


**Recommandation**   
Envisagez de migrer vers Azure SQL Managed Instance ou SQL Server sur des machines virtuelles Azure, et d’utiliser Database Mail.

Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>

**Titre : Instructions détectées qui référencent des procédures stockées système supprimées qui ne sont pas disponibles dans Azure SQL Database.**    
**Catégorie** : Avertissement   

**Description**   
Les procédures stockées étendues et système non prises en charge suivantes ne peuvent pas être utilisées dans Azure SQL Database - `sp_dboption`, `sp_addserver`, `sp_dropalias`, `sp_activedirectory_obj`, `sp_activedirectory_scp` et `sp_activedirectory_start`.


**Recommandation**    
Supprimez les références aux procédures système non prises en charge qui ont été supprimées dans Azure SQL Database.

Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="trace-flags"></a>Indicateurs de trace<a id="TraceFlags"></a>

**Titre : Azure SQL Database ne prend pas en charge les indicateurs de trace**   
**Catégorie** : Avertissement   

**Description**   
Les indicateurs de trace permettent de définir temporairement des caractéristiques de serveur spécifiques ou de désactiver un comportement particulier. Les indicateurs de trace servent le plus souvent à diagnostiquer des problèmes de performances et à déboguer des procédures stockées ou des systèmes informatiques complexes. Azure SQL Database ne prend pas en charge les indicateurs de trace. 


**Recommandation**   
Consultez la section Objets affectés dans Azure Migrate pour afficher tous les indicateurs de trace qui ne sont pas pris en charge dans Azure SQL Database et évaluer s’ils peuvent être supprimés. Vous pouvez également migrer vers Azure SQL Managed Instance qui prend en charge un nombre limité d’indicateurs de trace globaux, ou vers SQL Server sur une machine virtuelle Azure.

Informations supplémentaires : [Résolution des différences de Transact-SQL durant la migration vers SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="windows-authentication"></a>Authentification Windows<a id="WindowsAuthentication"></a>

**Titre : Les utilisateurs de base de données mappés avec l’authentification Windows (sécurité intégrée) ne sont pas pris en charge dans Azure SQL Database**   
**Catégorie** : Avertissement   

**Description**   
Azure SQL Database prend en charge deux types d’authentification 
- L’authentification SQL utilise un nom d’utilisateur et un mot de passe. 
- L’authentification Azure Active Directory utilise des identités gérées par Azure Active Directory et est prise en charge pour les domaines managés et intégrés. 

Les utilisateurs de base de données mappés avec l’authentification Windows (sécurité intégrée) ne sont pas pris en charge dans Azure SQL Database. 



**Recommandation**   
Fédérez l’Active Directory local avec Azure Active Directory. L’identité Windows peut ensuite être remplacée par les identités Azure Active Directory équivalentes. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure.

Informations supplémentaires : [Fonctionnalités de sécurité de SQL Database](../../database/security-overview.md#authentication)

## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Titre : xp_cmdshell n’est pas pris en charge dans Azure SQL Database.**    
**Catégorie**: Problème   

**Description**   
xp_cmdshell qui génère une interface de commande Windows et transmet une chaîne pour exécution n’est pas pris en charge dans Azure SQL Database. 


**Recommandation**   
Consultez la section Objets affectés dans Azure Migrate pour voir tous les objets qui utilisent xp_cmdshell, et évaluer si la référence à xp_cmdshell ou l’objet affecté peuvent être supprimés. Envisagez aussi d’explorer Azure Automation qui fournit un service d’automatisation et de configuration basé sur le cloud. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure. 

## <a name="next-steps"></a>Étapes suivantes

Pour commencer la migration de votre instance SQL Server vers Azure SQL Database, consultez le [Guide de migration de SQL Server vers Azure SQL Database](sql-server-to-sql-database-guide.md).

- Pour obtenir une matrice des services et outils Microsoft et tiers qui peuvent vous aider dans les différents scénarios de migration de données et de base de données ainsi que dans des tâches spécialisées, consultez [Services et outils de migration de données](../../../dms/dms-tools-matrix.md).

- Pour plus d’informations sur Azure SQL Database, consultez :
   - [Vue d’ensemble d’Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Calculatrice du coût total de possession Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Pour plus d’informations sur l’infrastructure et le cycle d’adoption pour les migrations cloud, consultez :
   -  [Cloud Adoption Framework pour Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Meilleures pratiques pour l’évaluation des coûts et le dimensionnement des charges de travail migrées vers Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Pour évaluer la couche d’accès aux applications, consultez [Data Access Migration Toolkit (préversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Pour savoir comment exécuter un test A/B sur la couche d’accès aux données, consultez [Assistant Expérimentation de base de données](/sql/dea/database-experimentation-assistant-overview).
