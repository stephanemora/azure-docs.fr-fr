---
title: Chiffrement transparent des données
description: Présentation de la technologie Transparent Data Encryption pour SQL Database et Data Warehouse. Ce document en décrit les avantages et les options de configuration, notamment le chiffrement transparent des données géré par le service et la prise en charge du service Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 11/01/2019
ms.openlocfilehash: 381dfb4fca7476d5805bff92d58ecbbf49679346
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979968"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Transparent Data Encryption pour SQL Database et Data Warehouse

Transparent Data Encryption (TDE) est une technologie de chiffrement transparent des données qui contribue à protéger Azure SQL Database, Azure SQL Managed Instance et Azure Data Warehouse contre les menaces d’activités hors connexion malveillantes en chiffrant les données au repos. Il assure le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux des transactions au repos, sans que cela nécessite de modifier l’application. Par défaut, TDE est activé pour toutes les bases de données Azure SQL nouvellement déployées. TDE ne permet pas de chiffrer la base de données **MASTER** logique dans SQL Database.  La base de données **MASTER** contient les objets nécessaires à l’exécution des opérations TDE sur les bases de données utilisateur.

Le chiffrement transparent des données doit être activé manuellement pour les bases de données plus anciennes Azure SQL Database, Azure SQL Managed Instance ou Azure SQL Data Warehouse.
Les bases de données Managed Instance créées par le biais de la restauration héritent de l’état de chiffrement de la base de données source.

Le chiffrement transparent des données chiffre le stockage d’une base de données entière, à l’aide d’une clé symétrique appelée clé de chiffrement de base de données. Cette clé de chiffrement de base de données est protégée par le protecteur du chiffrement transparent des données. Le protecteur est soit un certificat géré par le service (chiffrement transparent des données géré par le service), soit une clé asymétrique stockée dans Azure Key Vault (Bring Your Own Key). Définissez le protecteur de chiffrement transparent des données au niveau du serveur pour Azure SQL Database et Data Warehouse, et au niveau de l’instance pour Azure SQL Managed Instance. Le terme *serveur* fait référence à la fois au serveur et à l’instance tout au long de ce document, sauf indication contraire.

Au démarrage de la base de données, la clé de chiffrement de base de données chiffrée est déchiffrée, puis utilisée pour le déchiffrement et le rechiffrement des fichiers de base de données dans le processus du Moteur de base de données SQL Server. Le chiffrement transparent des données effectue le chiffrement et le déchiffrement d’E/S en temps réel des données au niveau de la page. Chaque page est déchiffrée lorsqu’elle est lue en mémoire, puis chiffrée avant d’être écrite sur le disque. Pour obtenir une description générale du chiffrement transparent des données, consultez l’article [Chiffrement transparent des données (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server exécuté sur une machine virtuelle Azure peut également utiliser une clé asymétrique provenant de Key Vault. La procédure de configuration diffère de celle qui s’applique en cas d’utilisation d’une clé asymétrique dans SQL Database et SQL Managed Instance. Pour plus d’informations, consultez l’article [Gestion de clés extensible à l’aide d’Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Chiffrement transparent des données géré par le service

Pour le chiffrement transparent des données dans Azure, la clé de chiffrement de base de données est protégée par défaut par un certificat de serveur intégré. Le certificat de serveur intégré est unique pour chaque serveur et l’algorithme de chiffrement utilisé est AES 256. Si une base de données figure dans une relation de géoréplication, la base de données primaire et la base de données géo-secondaire sont toutes deux protégées par la clé du serveur parent de la base de données primaire. Si deux bases de données sont connectées au même serveur, elles partagent également le même certificat intégré.  Microsoft fait alterner automatiquement ces certificats conformément à la stratégie de sécurité interne et la clé racine est protégée par un magasin des secrets interne Microsoft.  Les clients peuvent vérifier la conformité de SQL Database avec des stratégies de sécurité internes, dans des rapports d’audit tiers indépendants disponibles dans le [Centre de gestion de la confidentialité Microsoft](https://servicetrust.microsoft.com/).

En outre, Microsoft déplace et gère en toute transparence les clés en fonction des besoins en matière de géoréplication et de restaurations.

> [!IMPORTANT]
> Toutes les bases de données SQL et les bases de données Managed Instance nouvellement créées sont chiffrées par défaut à l’aide du chiffrement transparent des données géré par le service. Les bases de données SQL existantes créées avant mai 2017 et les bases de données SQL créées via restauration, géoréplication et copie de base de données, ne sont pas chiffrées par défaut. Les bases de données Managed Instance existantes créées avant février 2019 ne sont pas chiffrées par défaut. Les bases de données Managed Instance créées par le biais de la restauration héritent de l’état de chiffrement de la source.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Chiffrement transparent des données géré par le client - Bring Your Own Key

[TDE avec clés managées dans Azure Key Vault](transparent-data-encryption-byok-azure-sql.md) vous permet de chiffrer la clé de chiffrement de la base de données (DEK) avec une clé asymétrique managée par le client et appelée protecteur TDE.  Cela s’appelle aussi généralement la prise en charge Bring Your Own Key (BYOK) pour Transparent Data Encryption. Dans le scénario BYOK, le protecteur TDE est stocké dans un coffre géré appartenant au client, [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), le système Azure de gestion de clés externes sur le cloud. Le protecteur TDE peut être [généré par le coffre de clés ou transféré vers le coffre de clés](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) à partir d’un appareil HSM local. La DEK de TDE, stockée dans la page de démarrage d’une base de données, est chiffrée et déchiffrée par le protecteur TDE, qui est stocké dans Azure Key Vault et qui ne quitte jamais le coffre de clés.  La base de données SQL doit être autorisée à déchiffrer et chiffrer la DEK dans le coffre de clés appartenant au client. Si des autorisations d’accès du serveur SQL logique au coffre de clés sont supprimées, une base de données devient inaccessible alors que toutes les données sont chiffrées. Pour Azure SQL Database, le protecteur TDE est défini au niveau du serveur SQL logique et est hérité par toutes les bases de données associées à ce serveur. Pour [Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance), le protecteur TDE est défini au niveau de l’instance et il est hérité par toutes les bases de données *chiffrées* sur cette instance. Le terme *serveur* fait référence à la fois au serveur et à l’instance tout au long de ce document, sauf indication contraire.

Avec l’intégration de TDE à Azure Key Vault, les utilisateurs peuvent contrôler les tâches de gestion de clés, y compris les rotations de clés, les autorisations de coffre de clés, les sauvegardes de clés, ainsi que l’audit et le rapport sur tous les protecteurs TDE à l’aide de la fonctionnalité Azure Key Vault. Key Vault centralise la gestion des clés, utilise des modules de sécurité matériels étroitement surveillés (HSM) et permet la séparation des responsabilités entre la gestion de clés et des données pour aider à répondre aux exigences des stratégies de sécurité.
Pour plus d’informations sur le chiffrement transparent des données avec intégration Azure Key Vault (prise en charge du service BYOK) pour Azure SQL Database, SQL Managed Instance et Data Warehouse, consultez [Intégration du chiffrement transparent des données avec Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).

Pour commencer à utiliser le chiffrement transparent des données avec l’intégration d’Azure Key Vault (prise en charge du service BYOK), consultez le guide de procédure [Turn on transparent data encryption by using your own key from Key Vault by using PowerShell](transparent-data-encryption-byok-azure-sql-configure.md) (Activer le chiffrement transparent des données en utilisant votre propre clé Key Vault à l’aide de PowerShell).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Déplacer une base de données protégée par le chiffrement transparent des données

Vous n’avez pas besoin de déchiffrer les bases de données pour leur appliquer des opérations dans Azure. Les paramètres de chiffrement transparent des données définis sur la base de données source ou la base de données primaire sont hérités de manière transparente sur la cible. Les opérations incluses sont les suivantes :

- La géorestauration
- Restauration à un moment donné en libre-service
- Restauration d’une base de données supprimée
- La géoréplication active
- Création d’une copie de base de données
- Restauration du fichier de sauvegarde vers Azure SQL Managed Instance

> [!IMPORTANT]
> La sauvegarde manuelle COPY-ONLY d’une base de données chiffrée par un TDE géré par le service n’est pas autorisée dans Azure SQL Managed Instance, car le certificat utilisé pour le chiffrement n’est pas accessible. Utilisez la fonctionnalité de restauration dans le temps pour déplacer ce type de base de données vers une autre instance gérée.

Lorsque vous exportez une base de données protégée par le chiffrement transparent des données, le contenu exporté de la base de données n’est pas chiffré. Ce contenu exporté est stocké dans des fichiers BACPAC non chiffrés. Veillez à protéger les fichiers BACPAC de façon appropriée et à activer le chiffrement transparent des données une fois la nouvelle base de données importée.

Par exemple, si le fichier BACPAC est exporté à partir d’une instance SQL Server locale, le contenu importé de la nouvelle base de données n’est pas chiffré automatiquement. De même, si le fichier BACPAC est exporté vers une instance SQL Server locale, la nouvelle base de données n’est pas chiffrée automatiquement.

La seule exception survient lorsque vous procédez à une exportation à destination ou en provenance d’une base de données SQL. Le chiffrement transparent des données est activé dans la nouvelle base de données, mais le fichier BACPAC proprement dit n’est toujours pas chiffré.


## <a name="manage-transparent-data-encryption"></a>Gérer Transparent Data Encryption
# <a name="portaltabazure-portal"></a>[Portail](#tab/azure-portal)
Gérez Transparent Data Encryption sur le Portail Azure.

Pour configurer le chiffrement transparent des données par le biais du Portail Azure, vous devez être connecté en tant que Propriétaire, Contributeur ou Gestionnaire de sécurité SQL Azure.

Vous activez/désactivez le chiffrement transparent des données au niveau de la base de données. Pour activer le chiffrement transparent des données sur une base de données, accédez au [Portail Azure](https://portal.azure.com) et connectez-vous à l’aide de votre compte Administrateur ou Contributeur Azure. Recherchez les paramètres de chiffrement transparent des données pour votre base de données utilisateur. Le chiffrement transparent des données géré par le service est utilisé par défaut. Un certificat de chiffrement transparent des données est automatiquement généré pour le serveur qui contient la base de données. Pour Azure SQL Managed Instance, utilisez T-SQL pour activer et désactiver le chiffrement transparent des données sur une base de données.

![Chiffrement transparent des données géré par le service](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)

Vous définissez la clé principale du chiffrement transparent des données, également désignée sous le nom de protecteur du chiffrement transparent des données, au niveau du serveur. Pour utiliser le chiffrement transparent des données avec prise en charge de Bring Your Own Key et protéger vos bases de données avec une clé Key Vault, ouvrez les paramètres de chiffrement transparent des données pour votre serveur.

![Chiffrement transparent des données avec prise en charge de Bring Your Own Key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Gérez Transparent Data Encryption avec PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Pour ces cmdlets, voir [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm.

Pour configurer le chiffrement transparent des données par le biais de PowerShell, vous devez être connecté en tant que Propriétaire, Contributeur ou Gestionnaire de sécurité SQL Azure.

### <a name="cmdlets-for-azure-sql-database-and-data-warehouse"></a>Applets de commande pour Azure SQL Database et Data Warehouse

Utilisez les applets de commande suivantes pour Azure SQL Database et Data Warehouse :

| Applet de commande | Description |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Active ou désactive le chiffrement transparent des données pour une base de données|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Obtient l’état du chiffrement transparent des données pour une base de données |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Vérifie l’état d’avancement du chiffrement d’une base de données |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Ajoute une clé Key Vault à une instance SQL Server |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Obtient les clés Key Vault d’un serveur Azure SQL Database  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Définit le protecteur du chiffrement transparent des données pour une instance SQL Server |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Obtient le protecteur du chiffrement transparent des données |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Supprime une clé Key Vault d’une instance SQL Server |
|  | |

> [!IMPORTANT]
> Pour Azure SQL Managed Instance, utilisez la commande T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) pour activer et désactiver le chiffrement transparent des données au niveau de la base de données, et [l’exemple de script PowerShell](transparent-data-encryption-byok-azure-sql-configure.md) pour gérer le chiffrement transparent des données au niveau de l’instance.

# <a name="transact-sqltabazure-transactsql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Gérez Transparent Data Encryption avec Transact-SQL.

Connectez-vous à la base de données à l’aide d’un ID de connexion d’administrateur ou de membre du rôle **dbmanager** dans la base de données MASTER.

| Commande | Description |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF chiffre ou déchiffre une base de données |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Renvoie des informations sur l’état de chiffrement d’une base de données et sur ses clés de chiffrement de base de données associées |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Renvoie des informations sur l’état de chiffrement de chaque nœud d’entrepôt de données et sur ses clés de chiffrement de base de données associées |
|  | |

Vous ne pouvez pas basculer le protecteur du chiffrement transparent des données vers une clé provenant de Key Vault à l’aide de Transact-SQL. Utilisez PowerShell ou le Portail Azure.

# <a name="rest-apitabazure-restapi"></a>[REST API](#tab/azure-RESTAPI)
Gérez Transparent Data Encryption avec l’API REST.

Pour configurer le chiffrement transparent des données par le biais de l’API REST, vous devez être connecté en tant que Propriétaire, Contributeur ou Gestionnaire de sécurité SQL Azure.
Utilisez l’ensemble de commande suivant pour Azure SQL Database et Data Warehouse :

| Commande | Description |
| --- | --- |
|[Créer ou mettre à jour un serveur](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Ajoute une identité Azure Active Directory à une instance SQL Server (permettant d’accorder l’accès à Key Vault)|
|[Créer ou mettre à jour une clé de serveur](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Ajoute une clé Key Vault à une instance SQL Server|
|[Supprimer une clé de serveur](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Supprime une clé Key Vault d’une instance SQL Server|
|[Obtenir des clés de serveur](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Obtient une clé Key Vault spécifique d’une instance SQL Server|
|[Répertorier les clés de serveur par serveur](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Obtient les clés Key Vault pour une instance SQL Server |
|[Créer ou mettre à jour le protecteur du chiffrement](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Définit le protecteur du chiffrement transparent des données pour une instance SQL Server|
|[Obtenir le protecteur du chiffrement](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Obtient le protecteur du chiffrement transparent des données pour une instance SQL Server|
|[Répertorier les protecteurs du chiffrement par serveur](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Obtient les protecteurs du chiffrement transparent des données pour une instance SQL Server |
|[Créer ou mettre à jour la configuration du chiffrement transparent des données](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Active ou désactive le chiffrement transparent des données pour une base de données|
|[Obtenir la configuration du chiffrement transparent des données](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Obtient la configuration du chiffrement transparent des données pour une base de données|
|[Répertorier les résultats de configuration du chiffrement transparent des données](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Obtient le résultat du chiffrement d’une base de données|

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une description générale du chiffrement transparent des données, consultez l’article [Chiffrement transparent des données (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Pour plus d’informations sur le chiffrement transparent des données avec prise en charge du service BYOK pour Azure SQL Database, Azure SQL Managed Instance et Data Warehouse, consultez [Chiffrement transparent des données avec prise en charge du service Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).
- Pour commencer à utiliser le chiffrement transparent des données avec prise en charge du service BYOK, consultez le guide de procédure [Turn on transparent data encryption by using your own key from Key Vault by using PowerShell](transparent-data-encryption-byok-azure-sql-configure.md) (Activer le chiffrement transparent des données en utilisant votre propre clé Key Vault à l’aide de PowerShell).
- Pour plus d’informations sur Key Vault, consultez la [page de documentation concernant Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
