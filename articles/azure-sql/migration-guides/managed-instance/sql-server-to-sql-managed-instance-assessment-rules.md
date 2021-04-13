---
title: Règles d’évaluation pour la migration de SQL Server vers Azure SQL Managed Instance
description: Règles d’évaluation pour identifier les problèmes liés à l’instance SQL Server source qui doivent être résolus avant la migration vers Azure SQL Managed Instance.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: fc8959d44fbacd90916a045d23db4bee872c4670
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026034"
---
# <a name="assessment-rules-for-sql-server-to--azure-sql-managed-instance-migration"></a>Règles d’évaluation pour la migration de SQL Server vers Azure SQL Managed Instance
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Les outils de migration valident votre instance de SQL Server source en exécutant un certain nombre de règles d’évaluation afin d’identifier les problèmes à résoudre avant la migration de votre base de données SQL Server vers Azure SQL Managed Instance. 

Cet article fournit la liste des règles utilisées pour évaluer la faisabilité de la migration de votre base de données SQL Server vers Azure SQL Managed Instance. 

## <a name="analysiscommand-job"></a>Travail AnalysisCommand<a id="AnalysisCommandJob"></a>

**Titre : L’étape de travail AnalysisCommand n’est pas prise en charge dans Azure SQL Managed Instance.**    
**Catégorie** : Avertissement   


**Description**   
Il s’agit d’une étape de travail qui exécute une commande Analysis Services. L’étape de travail AnalysisCommand n’est pas prise en charge dans Azure SQL Managed Instance.

**Recommandation**     
Consultez la section Objets affectés dans Azure Migrate pour afficher tous les travaux utilisant l’étape de travail Commande Analysis Services et déterminer si l’étape de travail ou l’objet affecté peuvent être supprimés. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure.

Informations supplémentaires : [Différences de SQL Server Agent dans Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="analysisquery-job"></a>Travail AnalysisQuery<a id="AnalysisQueryJob"></a>

**Titre : L’étape de travail AnalysisQuery n’est pas prise en charge dans Azure SQL Managed Instance.**    
**Catégorie** : Avertissement   

**Description**   
Il s’agit d’une étape de travail qui exécute une requête Analysis Services. L’étape de travail AnalysisQuery n’est pas prise en charge dans Azure SQL Managed Instance.


**Recommandation**   
Consultez la section Objets affectés dans Azure Migrate pour afficher tous les travaux utilisant l’étape de travail Requête Analysis Services et déterminer si l’étape de travail ou l’objet affecté peuvent être supprimés. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure.

Informations supplémentaires : [Différences de SQL Server Agent dans Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="assembly-from-file"></a>Assembly d’un fichier<a id="AssemblyFromFile"></a>

**Titre : Les instructions « CREATE ASSEMBLY » et « ALTER ASSEMBLY » avec un paramètre de fichier ne sont pas pris en charge dans Azure SQL Managed Instance.**    
**Catégorie** : Avertissement   

**Description**   
Azure SQL Managed Instance ne peut pas accéder à des partages de fichiers ou à des dossiers Windows. Consultez la section « Objets affectés » pour connaître les utilisations spécifiques des instructions BULK INSERT qui ne font pas référence à un blob Azure. Des objets avec l’instruction « BULK INSERT » où la source n’est pas Stockage Blob Azure ne fonctionneront pas après migration vers Azure SQL Managed Instance.


**Recommandation**   
Vous devez convertir les instructions BULK INSERT qui utilisent des fichiers locaux ou des partages de fichiers pour qu’elles utilisent des fichiers de Stockage Blob Azure à la place, pendant la migration vers Azure SQL Managed Instance. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure. 

Informations supplémentaires : [Différences de CLR dans Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)

## <a name="bulk-insert"></a>Bulk insert<a id="BulkInsert"></a>

**Titre : L’instruction BULK INSERT avec une source de données blob non Azure n’est pas prise en charge dans Azure SQL Managed Instance.**    
**Catégorie**: Problème   

**Description**   
Azure SQL Managed Instance ne peut pas accéder à des partages de fichiers ou à des dossiers Windows. Consultez la section « Objets affectés » pour connaître les utilisations spécifiques des instructions BULK INSERT qui ne font pas référence à un blob Azure. Des objets avec l’instruction « BULK INSERT » où la source n’est pas Stockage Blob Azure ne fonctionneront pas après migration vers Azure SQL Managed Instance.


**Recommandation**   
Vous devez convertir les instructions BULK INSERT qui utilisent des fichiers locaux ou des partages de fichiers pour qu’elles utilisent des fichiers de Stockage Blob Azure à la place, pendant la migration vers Azure SQL Managed Instance.

Informations supplémentaires : [Différences d’instruction Bulk Insert et d’OPENROWSET dans Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="clr-security"></a>Sécurité CLR<a id="ClrStrictSecurity"></a>

**Titre : Les assemblys CLR marqués comme étant SAFE ou EXTERNAL_ACCESS sont considérés comme étant UNSAFE**   
**Catégorie**: Problème   

**Description**   
Le mode Sécurité CLR stricte est appliqué dans Azure SQL Managed Instance. Ce mode est activé par défaut et introduit des changements importants pour les bases de données contenant des assemblys CLR définis par l’utilisateur marqués comme étant SAFE ou EXTERNAL_ACCESS.


**Recommandation**   
CLR utilise la sécurité d’accès du code (CAS) dans le .NET Framework, qui n’est plus pris en charge comme limite de sécurité. Depuis le moteur de base de données SQL Server 2017 (14. x), une option `sp_configure` appelée clr strict security a été introduite pour améliorer la sécurité des assemblys CLR. L’option clr strict security activée par défaut traite les assemblys CLR SAFE et EXTERNAL_ACCESS comme s’ils étaient marqués comme étant UNSAFE. Quand l’option clr strict security est désactivée, un assembly CLR créé avec PERMISSION_SET = SAFE peut être en mesure d’accéder à des ressources système externes, d’appeler du code non managé et d’acquérir des privilèges sysadmin. Une fois la sécurité stricte activée, le chargement des assemblys qui ne sont pas signés échoue. Par ailleurs, si une base de données contient des assemblys SAFE ou EXTERNAL_ACCESS, les instructions RESTORE ou ATTACH DATABASE peuvent aboutir, mais le chargement des assemblys peut échouer. Pour charger les assemblys, vous devez modifier, ou bien supprimer et recréer, chaque assembly, de façon à ce qu’il soit signé avec un certificat ou une clé asymétrique ayant un login correspondant avec l’autorisation UNSAFE ASSEMBLY sur le serveur.

Informations supplémentaires : [Sécurité CLR stricte](/sql/database-engine/configure-windows/clr-strict-security)

## <a name="compute-clause"></a>Clause Compute<a id="ComputeClause"></a>

**Titre : la clause COMPUTE n’est plus disponible et a été supprimée.**    
**Catégorie** : Avertissement   

**Description**   
La clause COMPUTE génère des totaux qui s’affichent sous la forme de colonnes de résumé supplémentaires à la fin du jeu de résultats. Toutefois, cette clause n’est plus prise en charge dans Azure SQL Managed Instance.



**Recommandation**   
Le module T-SQL doit être réécrit en utilisant l’opérateur ROLLUP à la place. Le code ci-dessous montre comment remplacer COMPUTE par ROLLUP : 

```sql
USE AdventureWorks GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) 
BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP;
```

Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="cryptographic-provider"></a>Fournisseur de chiffrement<a id="CryptographicProvider"></a>

**Titre : Nous avons identifié une utilisation de CREATE CRYPTOGRAPHIC PROVIDER ou ALTER CRYPTOGRAPHIC PROVIDER, ce qui n’est pas pris en charge dans Azure SQL Managed Instance.**    
**Catégorie**: Problème   

**Description**   
Le service Azure SQL Managed Instance ne prend pas en charge les instructions CRYPTOGRAPHIC PROVIDER, car il ne peut pas accéder aux fichiers. Consultez la section Objets affectés pour connaître les utilisations spécifiques des instructions CRYPTOGRAPHIC PROVIDER. Des objets avec « CREATE CRYPTOGRAPHIC PROVIDER » ou « ALTER CRYPTOGRAPHIC PROVIDER » ne fonctionneront pas correctement après migration vers Azure SQL Managed Instance.


**Recommandation**   
Révisez les objets avec « CREATE CRYPTOGRAPHIC PROVIDER » ou « ALTER CRYPTOGRAPHIC PROVIDER ». Dans tous les objets de ce type requis, supprimez les utilisations de ces fonctionnalités. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure. 

Informations supplémentaires : [Différences de fournisseur de chiffrement dans Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#cryptographic-providers)

## <a name="database-compatibility"></a>Compatibilité de base de données<a id="DbCompatLevelLowerThan100"></a>

**Titre : Un niveau de compatibilité de la base de données inférieur à 100 n’est pas pris en charge**   
**Catégorie** : Avertissement   

**Description**   
Le Niveau de compatibilité de la base de données est un outil précieux quand il s’agit de moderniser une base de données. Il permet en effet de mettre à niveau le Moteur de base de données SQL Server tout en continuant de connecter l’état opérationnel des applications en conservant le Niveau de compatibilité de la base de données antérieur à la mise à niveau. Azure SQL Managed Instance ne prend pas en charge les niveaux de compatibilité inférieurs à 100. Lors de la restauration de la base de données avec un niveau de compatibilité inférieur à 100 sur Azure SQL Managed Instance, le niveau de compatibilité est porté à 100. 


**Recommandation**... Évaluez si la fonctionnalité de l’application est intacte quand le niveau de compatibilité de la base de données est porté à 100 sur Azure SQL Managed Instance. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure.

Informations supplémentaires : [Niveaux de compatibilité pris en charge dans Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#compatibility-levels)

## <a name="database-principal-alias"></a>Alias principal de base de données<a id="DatabasePrincipalAlias"></a>

**Titre : SYS. DATABASE_PRINCIPAL_ALIASES n’est plus disponible et a été supprimé.**    
**Catégorie**: Problème   

**Description**   
SYS.DATABASE_PRINCIPAL_ALIASES n’est plus disponible et a été supprimé dans Azure SQL Managed Instance.


**Recommandation**   
Utilisez des rôles à la place d'alias.

Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="disable_def_cnst_chk-option"></a>Option DISABLE_DEF_CNST_CHK<a id="DisableDefCNSTCHK"></a>

**Titre : L’option SET DISABLE_DEF_CNST_CHK n’est plus disponible et a été supprimée.**    
**Catégorie**: Problème   

**Description**   
L’option SET DISABLE_DEF_CNST_CHK n’est plus disponible et a été supprimée dans Azure SQL Managed Instance.


Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW, indicateur<a id="FastFirstRowHint"></a>

**Titre : L’indicateur de requête FASTFIRSTROW n’est plus disponible et a été supprimé.**    
**Catégorie** : Avertissement   

**Description**   
L’indicateur de requête FASTFIRSTROW n’est plus disponible et a été supprimé dans Azure SQL Managed Instance.


**Recommandation**   
Au lieu de l’indicateur de requête FASTFIRSTROW, utilisez OPTION (FAST n).

Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**Titre : Les fonctionnalités Filestream et filetable ne sont pas prises en charge dans Azure SQL Managed Instance.**    
**Catégorie**: Problème   

**Description**   
La fonctionnalité Filestream qui permet de stocker des données non structurées telles que des documents texte, des images et des vidéos dans le système de fichiers NTFS, n’est pas prise en charge dans Azure SQL Managed Instance. **Il n’est pas possible de migrer cette base de données, car il n’est pas possible de restaurer la sauvegarde contenant les groupes de fichiers Filestream sur Azure SQL Managed Instance.**


**Recommandation**   
Chargez les fichiers non structurés dans le service Stockage Blob Azure et stockez les métadonnées associées à ces fichiers (nom, type, adresse URL, clé de stockage, etc.) dans Azure SQL Managed Instance. Il se peut que vous deviez remanier votre application pour activer la diffusion en continu de blobs vers et depuis Azure SQL Managed Instance. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure.

Informations supplémentaire : [Blog sur la diffusion en continu de blobs vers et depuis SQL Azure](https://azure.microsoft.com/en-in/blog/streaming-blobs-to-and-from-sql-azure/)

## <a name="heterogeneous-ms-dtc"></a>MS DTC hétérogène<a id="MIHeterogeneousMSDTCTransactSQL"></a>

**Titre : La transaction BEGIN DISTRIBUTED TRANSACTION avec un serveur distant non SQL Server n’est pas pris en charge dans Azure SQL Managed Instance.**    
**Catégorie**: Problème   

**Description**   
La transaction distribuée SQL BEGIN DISTRIBUTED TRANSACTION démarrée par Transact et gérée par Microsoft Distributed Transaction Coordinator (MS DTC) n’est pas prise en charge dans Azure SQL Managed Instance si le serveur distant n’est pas un SQL Server. 


**Recommandation**   
Examinez la section Objets affectés dans Azure Migrate pour voir tous les objets utilisant la transaction BEGIN DISTRUBUTED TRANSACTION. Envisagez de migrer les bases de données participantes vers Azure SQL Database où les transactions distribuées sur plusieurs instances sont prises en charge (actuellement en préversion). Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure.

Informations supplémentaires : [Transactions sur plusieurs serveurs pour Azure SQL Managed Instance ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)

## <a name="homogenous-ms-dtc"></a>MS DTC homogène<a id="MIHomogeneousMSDTCTransactSQL"></a>

**Titre : La transaction BEGIN DISTRIBUTED TRANSACTION est prise en charge sur plusieurs serveurs pour Azure SQL Managed Instance.**    
**Catégorie**: Problème   

**Description**   
La transaction distribuée SQL BEGIN DISTRIBUTED TRANSACTION démarrée par Transact et gérée par Microsoft Distributed Transaction Coordinator (MS DTC) est prise en charge sur plusieurs serveurs pour Azure SQL Managed Instance.


**Recommandation**   
Examinez la section Objets affectés dans Azure Migrate pour voir tous les objets utilisant la transaction BEGIN DISTRUBUTED TRANSACTION. Envisagez de migrer les bases de données participantes vers Azure SQL Database où les transactions distribuées sur plusieurs instances sont prises en charge (actuellement en préversion). Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure. 

Informations supplémentaires : [Transactions sur plusieurs serveurs pour Azure SQL Managed Instance](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="linked-server-non-sql-provider"></a>Serveur lié (fournisseur non SQL)<a id="LinkedServerWithNonSQLProvider"></a>

**Titre : Un serveur lié avec fournisseur non SQL Server n’est pas pris en charge dans Azure SQL Managed Instance.**    
**Catégorie**: Problème   

**Description**   
Des serveurs liés permettent au Moteur de base de données SQL Server d’exécuter des commandes sur des sources de données OLE DB en dehors de l’instance de SQL Server. Un serveur lié avec fournisseur non SQL Server n’est pas pris en charge dans Azure SQL Managed Instance. 


**Recommandation**   
Azure SQL Managed Instance ne prend pas en charge la fonctionnalité de serveur lié si le fournisseur de serveur distant n’est pas SQL Server, comme Oracle, Sybase, etc. 

Les actions suivantes sont recommandées pour éliminer le besoin de serveurs liés : 
- Identifiez les bases de données dépendantes à partir de serveurs non SQL distants et envisagez de les déplacer dans la base de données en cours de migration. 
- Migrez les bases de données dépendantes vers des cibles prises en charge, telles que des instances SQL Managed Instance, SQL Database, Azure Synapse SQL et SQL Server. 
- Envisagez de créer un serveur lié entre Azure SQL Managed Instance et SQL Server sur une machine virtuelle Azure (machine virtuelle SQL).  Ensuite, à partir de la machine virtuelle SQL, créez un serveur lié sur Oracle, Sybase, etc. Cette approche implique deux tronçons, mais peut être utilisée comme solution de contournement temporaire.  
- Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure.

Informations supplémentaires : [Différences de serveurs liés dans Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)

## <a name="merge-job"></a>Travail de fusion<a id="MergeJob"></a>

**Titre : L’étape de travail de fusion n’est pas prise en charge dans Azure SQL Managed Instance.**    
**Catégorie** : Avertissement   

**Description**   
Il s’agit d’une étape de travail qui active l’Agent de fusion pour la réplication. L'Agent de fusion de réplication est un fichier exécutable d'utilitaire qui applique l'instantané initial contenu dans les tables de base de données aux Abonnés. Il fusionne également les modifications de données incrémentielles ayant eu lieu sur le serveur de publication après la création de l'instantané initial, puis harmonise les conflits soit en fonction des règles que vous configurez, soit à l'aide d'un programme de résolution personnalisé que vous créez. L’étape de travail de fusion n’est pas prise en charge dans Azure SQL Managed Instance.


**Recommandation**   
Consultez la section Objets affectés dans Azure Migrate pour afficher tous les travaux utilisant l’étape de travail de fusion et déterminer si l’étape de travail ou l’objet affecté peuvent être supprimés. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure

Informations supplémentaires : [Différences de SQL Server Agent dans Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="mi-database-size"></a>Taille de base de données MI<a id="MIDatabaseSize<"></a>

**Titre : Azure SQL Managed Instance ne prend pas en charge les tailles de base de données supérieures à 8 To.**    
**Catégorie**: Problème   

**Description**   
La taille de la base de données est supérieure au stockage réservé d’instance maximal. **Cette base de données ne peut pas être sélectionnée pour la migration, car sa taille dépasse la limite autorisée.**


**Recommandation**   
Évaluez si les données peuvent être archivées compressées ou partitionnées dans plusieurs bases de données. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure.

Informations supplémentaires : [Caractéristiques de génération matérielle d’Azure SQL Managed Instance ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)



## <a name="mi-instance-size"></a>Taille d’instances MI<a id="MIInstanceSize<"></a>

**Titre : La taille de stockage d’instance maximale dans Azure SQL Managed Instance ne peut pas être supérieure à 8 To.**    
**Catégorie** : Avertissement   

**Description**   
La taille de toutes les bases de données est supérieure au stockage réservé d’instance maximal.  


**Recommandation**   
Envisagez de migrer les bases de données vers différentes instances Azure SQL Managed ou vers SQL Server sur une machine virtuelle Azure si toutes les bases de données doivent exister sur la même instance. 

Informations supplémentaires : [Caractéristiques de génération matérielle d’Azure SQL Managed Instance ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)


## <a name="multiple-log-files"></a>Fichiers journaux multiples<a id="MultipleLogFiles<"></a>

**Titre : Azure SQL Managed Instance ne prend pas en charge des fichiers journaux multiples.**    
**Catégorie**: Problème   

**Description**   
SQL Server permet à une base de données de journaliser dans plusieurs fichiers. Cette base de données contient des fichiers journaux multiples qui ne sont pas pris en charge dans Azure SQL Managed Instance. **Il n’est pas possible de migrer cette base de données, car il n’est pas possible de restaurer la sauvegarde sur Azure SQL Managed Instance. 
**

**Recommandation**   
Azure SQL Managed Instance prend en charge un seul journal par base de données. Vous devez supprimer tous les fichiers journaux sauf un avant de migrer cette base de données vers Azure : 

```sql
ALTER DATABASE [database_name] REMOVE FILE [log_file_name]
```

Informations supplémentaires : [Options de base de données non prises en charge dans Azure SQL Managed Instance  ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#database-options)



## <a name="next-column"></a>Colonne Next<a id="NextColumn"></a>

**Titre : les tables et colonnes nommées NEXT provoquent une erreur dans Azure SQL Managed Instance.**    
**Catégorie**: Problème   

**Description**   
Des tables ou colonnes nommées NEXT ont été détectées. Les séquences introduites dans Microsoft SQL Server utilisent la fonction ANSI standard NEXT VALUE FOR. Si une table ou une colonne est nommée NEXT, que la colonne a pour alias VALUE et que l'ANSI standard AS est omis, l'instruction obtenue peut provoquer une erreur.


**Recommandation**   
Réécrivez les instructions pour inclure le mot clé ANSI standard AS lors de l'utilisation d'alias pour une table ou une colonne. Par exemple, quand une colonne est nommée NEXT et que cette colonne a pour alias VALUE, la requête SELECT NEXT VALUE FROM TABLE provoque une erreur et doit être réécrite sous la forme SELECT NEXT AS VALUE FROM TABLE. De même, quand une table est nommée NEXT et que cette table a pour alias VALUE, la requête SELECT col1 FROM NEXT VALUE provoque une erreur et doit être réécrite sous la forme SELECT col1 FROM NEXT AS VALUE.



## <a name="non-ansi-style-left-outer-join"></a>Jointure externe gauche de style non ANSI<a id="NonANSILeftOuterJoinSyntax"></a>

**Titre : La jointure externe gauche de style non ANSI n’est plus disponible et a été supprimée.**    
**Catégorie** : Avertissement   

**Description**   
La jointure externe gauche de style non-ANSI n’est plus disponible et a été supprimée dans Azure SQL Managed Instance. 


**Recommandation**   
Utilisez la syntaxe de jointure ANSI.

Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="non-ansi-style-right-outer-join"></a>Jointure externe droite de style non ANSI<a id="NonANSIRightOuterJoinSyntax"></a>

**Titre : La jointure externe droite de style non ANSI n’est plus disponible et a été supprimée.**    
**Catégorie** : Avertissement   

**Description**   
La jointure externe droite de style non-ANSI n’est plus disponible et a été supprimée dans Azure SQL Managed Instance. 



Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

**Recommandation**   
Utilisez la syntaxe de jointure ANSI.

## <a name="databases-exceed-100"></a>Nombre de bases de données supérieur à 100 <a id="NumDbExceeds100"></a>

**Titre : Azure SQL Managed Instance prend en charge un maximum de 100 bases de données par instance.**    
**Catégorie** : Avertissement   

**Description**   
Le nombre maximal de bases de données prises en charge dans Azure SQL Managed Instance est de 100, sauf si la limite de taille de stockage d’instance a été atteinte.



**Recommandation**   
Envisagez de migrer les bases de données vers différentes instances Azure SQL Managed ou vers SQL Server sur une machine virtuelle Azure si toutes les bases de données doivent exister sur la même instance. 

Informations supplémentaires : [Limites des ressources d’Azure SQL Managed Instance ](../../managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="openrowset-non-blob-data-source"></a>OPENROWSET (source de données non blob)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Titre : OpenRowSet utilisé dans une opération en bloc avec une source de données de stockage blob non Azure n’est pas pris en charge dans Azure SQL Managed Instance.**    
**Catégorie**: Problème   

**Description**   
OPENROWSET prend en charge les opérations en bloc par l’intermédiaire d’un fournisseur BULK intégré qui permet de lire les données d’un fichier et de les retourner sous la forme d’un ensemble de lignes. OPENROWSET avec une source de données de stockage blob non Azure n’est pas pris en charge dans Azure SQL Managed Instance. 



**Recommandation**   
La fonction OPENROWSET peut être utilisée pour exécuter des requêtes uniquement sur des instances SQL Server (gérées, locales ou sur des machines virtuelles). Seules les valeurs SQLNCLI, SQLNCLI11 et SQLOLEDB sont prises en charge en tant que fournisseurs. Par conséquent, l’action recommandée consiste à identifier les bases de données dépendantes à partir de serveurs non SQL distants et à envisager de les déplacer dans la base de données en cours de migration. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure

Informations supplémentaires : [Différences d’instruction Bulk Insert et d’OPENROWSET dans Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)

## <a name="openrowset-non-sql-provider"></a>OPENROWSET (fournisseur non SQL)<a id="OpenRowsetWithNonSQLProvider"></a>

**Titre : OpenRowSet avec un fournisseur non-SQL n’est pas pris en charge dans Azure SQL Managed Instance.**    
**Catégorie**: Problème   

**Description**   
Cette méthode est une autre façon d'accéder à des tables dans un serveur lié et constitue une méthode efficace pour vous connecter et accéder à des données distantes en utilisant OLE DB. OpenRowSet avec un fournisseur non-SQL n’est pas pris en charge dans Azure SQL Managed Instance. 



**Recommandation**   
La fonction OPENROWSET peut être utilisée pour exécuter des requêtes uniquement sur des instances SQL Server (gérées, locales ou sur des machines virtuelles). Seules les valeurs SQLNCLI, SQLNCLI11 et SQLOLEDB sont prises en charge en tant que fournisseurs. Par conséquent, l’action recommandée consiste à identifier les bases de données dépendantes à partir de serveurs non SQL distants et à envisager de les déplacer dans la base de données en cours de migration.

Informations supplémentaires : [Différences d’instruction Bulk Insert et d’OPENROWSET dans Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="powershell-job"></a>Travail PowerShell<a id="PowerShellJob"></a>

**Titre : L’étape de travail PowerShell n’est pas prise en charge dans Azure SQL Managed Instance.**    
**Catégorie** : Avertissement   

**Description**   
Il s’agit d’une étape de travail qui exécute un script PowerShell. L’étape de travail PowerShell n’est pas prise en charge dans Azure SQL Managed Instance. 



**Recommandation**   
Consultez la section Objets affectés dans Azure Migrate pour afficher tous les travaux utilisant l’étape de travail PowerShell et déterminer si l’étape de travail ou l’objet affecté peuvent être supprimés.  Évaluez si Azure Automation peut être utilisé. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure

Informations supplémentaires : [Différences de SQL Server Agent dans Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="queue-reader-job"></a>Travail Lecture de la file d’attente<a id="QueueReaderJob"></a>

**Titre : L’étape de travail Lecture de la file d’attente n’est pas prise en charge dans Azure SQL Managed Instance.**    
**Catégorie** : Avertissement   

**Description**   
Il s’agit d’une étape de travail qui active l’Agent de lecture de la file d’attente pour la réplication. L’Agent de lecture de la file d’attente de réplication est un fichier exécutable qui lit les messages stockés dans une file d’attente Microsoft SQL Server ou une file d’attente de messages Microsoft, puis qui applique ces messages au serveur de publication. L'Agent de lecture de la file d'attente est utilisé avec les publications transactionnelles et les publications d'instantané qui autorisent la mise à jour en attente. L’étape de travail Lecture de la file d’attente n’est pas prise en charge dans Azure SQL Managed Instance.


**Recommandation**   
Consultez la section Objets affectés dans Azure Migrate pour afficher tous les travaux utilisant l’étape de travail Lecture de la file d’attente et déterminer si l’étape de travail ou l’objet affecté peuvent être supprimés. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure.

Informations supplémentaires : [Différences de SQL Server Agent dans Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Titre : Les appels de RAISERROR de style hérité doivent être remplacés par des équivalents récents.**    
**Catégorie** : Avertissement   

**Description**   
Les appels RAISERROR comme dans l’exemple ci-dessous sont dits de style hérité parce qu’ils n’incluent pas les virgules et les parenthèses. RAISERROR 50001 ’ceci est un test’. Cette méthode d’appel de RAISERROR n’est plus disponible et a été supprimée dans Azure SQL Managed Instance.



**Recommandation**   
Réécrivez l’instruction en utilisant la syntaxe de RAISERROR actuelle ou vérifiez si l’approche moderne de `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` est applicable.

Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="service-broker"></a>Service Broker<a id="ServiceBrokerWithNonLocalAddress"></a>

**Titre : Le fonctionnalité Service Broker est partiellement prise en charge dans Azure SQL Managed Instance.**    
**Catégorie**: Problème   

**Description**   
SQL Server Service Broker assure la prise en charge native des applications de messagerie et de mise en file d’attente dans le Moteur de base de données SQL Server. Cette base de données a un Service Broker entre instances activé, qui n’est pas pris en charge dans Azure SQL Managed Instance. 


**Recommandation**   
Azure SQL Managed Instance ne prend pas en charge un Service Broker entre instances, c’est-à-dire quand l’adresse n’est pas locale. Vous devez désactiver le Service Broker à l’aide de la commande suivante avant de migrer cette base de données vers Azure : `ALTER DATABASE [database_name] SET DISABLE_BROKER`. En outre, il se peut que vous deviez également arrêter ou supprimer le point de terminaison du Service Broker pour empêcher les messages d’arriver dans l’instance SQL. Une fois la base de données migrée vers Azure, vous pouvez examiner les fonctionnalités Azure Service Bus permettant d’implémenter un système de messagerie générique basé sur le cloud au lieu du Service Broker. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure. 

Informations supplémentaires : [Différences de Service Broker dans Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker)

## <a name="sql-mail"></a>SQL Mail<a id="SqlMail"></a>

**Titre : SQL Mail n’est plus disponible.**    
**Catégorie** : Avertissement   


**Description**   
SQL Mail n’est plus disponible et a été supprimé dans Azure SQL Managed Instance.



**Recommandation**   
Utilisez la messagerie de la base de données.

Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>


**Titre : Instructions détectées qui référencent des procédures stockées système supprimées qui ne sont pas disponibles dans Azure SQL Managed Instance.**    
**Catégorie** : Avertissement   

**Description**   
Les procédures stockées étendues et système non prises en charge suivantes ne peuvent pas être utilisées dans Azure SQL Managed Instance - `sp_dboption`, `sp_addserver`, `sp_dropalias`,`sp_activedirectory_obj`, `sp_activedirectory_scp` et `sp_activedirectory_start`. 




**Recommandation**   
Supprimez les références aux procédures système non prises en charge qui ont été supprimées dans Azure SQL Managed Instance.

Informations supplémentaires : [Fonctionnalité du moteur de base de données abandonnée dans SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="transact-sql-job"></a>Travail Transact-SQL<a id="TransactSqlJob"></a>

**Titre : L’étape de travail TSQL inclut des commandes non prises en charge dans Azure SQL Managed Instance**   
**Catégorie** : Avertissement   


**Description**   
Il s’agit d’une étape de travail qui exécute des scripts TSQL à une heure planifiée. L’étape de travail TSQL inclut des commandes non prises en charge dans Azure SQL Managed Instance.



**Recommandation**   
Consultez la section Objets affectés dans Azure Migrate pour voir tous les travaux qui incluent des commandes non prises en charge dans Azure SQL Managed Instance et évaluer si l’étape de travail ou l’objet affecté peut être supprimés. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure.

Informations supplémentaires : [Différences de SQL Server Agent dans Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="trace-flags"></a>Indicateurs de trace<a id="TraceFlags"></a>

**Titre : Des indicateurs de trace non pris en charge dans Azure SQL Managed Instance ont été trouvés**   
**Catégorie** : Avertissement   


**Description**   
Azure SQL Managed Instance ne prend en charge qu’un nombre limité d’indicateurs de trace globaux. Les indicateurs de trace de session ne sont pas pris en charge.



**Recommandation**   
Consultez la section Objets affectés dans Azure Migrate pour afficher tous les indicateurs de trace qui ne sont pas pris en charge dans Azure SQL Managed Instance et évaluer s’ils peuvent être supprimés. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure.

Informations supplémentaires : [Indicateurs de trace](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql#trace-flags)


## <a name="windows-authentication"></a>Authentification Windows<a id="WindowsAuthentication"></a>

**Titre : Les utilisateurs de base de données mappés avec l’authentification Windows (sécurité intégrée) ne sont pas pris en charge dans Azure SQL Managed Instance**   
**Catégorie** : Avertissement   


**Description**   
Azure SQL Managed Instance prend en charge deux types d’authentifications : 
- l’authentification SQL, qui utilise un nom d’utilisateur et un mot de passe
- L’authentification Azure Active Directory, qui utilise des identités gérées par Azure Active Directory et qui est prise en charge pour des domaines gérés et intégrés. 

Les utilisateurs de base de données mappés avec l’authentification Windows (sécurité intégrée) ne sont pas pris en charge dans Azure SQL Managed Instance. 


**Recommandation**   
Fédérez l’Active Directory local avec Azure Active Directory. L’identité Windows peut ensuite être remplacée par les identités Azure Active Directory équivalentes. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure.

Informations supplémentaires : [Fonctionnalités de sécurité de SQL Managed instance](../../database/security-overview.md#authentication)


## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Titre : xp_cmdshell n’est pas pris en charge dans Azure SQL Managed Instance.**    
**Catégorie**: Problème   


**Description**   
Xp_cmdshell qui génère une interface de commande Windows et transmet une chaîne pour exécution n’est pas pris en charge dans Azure SQL Managed Instance. 



**Recommandation**   
Consultez la section Objets affectés dans Azure Migrate pour voir tous les objets qui utilisent xp_cmdshell, et évaluer si la référence à xp_cmdshell ou l’objet affecté peuvent être supprimés. Envisagez d’explorer Azure Automation qui fournit un service d’automatisation et de configuration basé sur le cloud. Vous pouvez également migrer vers SQL Server sur une machine virtuelle Azure.

Informations supplémentaires : [Différences de procédures stockées dans Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)

## <a name="next-steps"></a>Étapes suivantes

Pour commencer votre migration de SQL Server vers Azure SQL Managed Instance, consultez le [guide de migration de SQL Server vers Azure SQL Managed Instance](sql-server-to-managed-instance-guide.md).

- Pour obtenir une matrice des services et outils Microsoft et tiers qui peuvent vous aider dans les différents scénarios de migration de données et de base de données ainsi que leurs tâches spécialisées, consultez [Services et outils de migration de données](../../../dms/dms-tools-matrix.md).

- Pour en savoir plus sur Azure SQL Managed Instance, consultez :
   - [Niveaux de service dans Azure SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Différences entre SQL Server et Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Outil de calcul du coût total de possession Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Pour plus d’informations sur l’infrastructure et le cycle d’adoption pour les migrations cloud, consultez :
   -  [Cloud Adoption Framework pour Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Meilleures pratiques pour l’évaluation des coûts et le dimensionnement des charges de travail migrées vers Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Pour évaluer la couche d’accès aux applications, consultez [Data Access Migration Toolkit (préversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Pour savoir comment exécuter un test A/B sur la couche d’accès aux données, consultez [Assistant Expérimentation de base de données](/sql/dea/database-experimentation-assistant-overview).