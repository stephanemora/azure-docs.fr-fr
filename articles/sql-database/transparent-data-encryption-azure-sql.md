---
title: Transparent Data Encryption pour Azure SQL Database et Data Warehouse | Microsoft Docs
description: Présentation de la technologie Transparent Data Encryption pour SQL Database et Data Warehouse. Ce document en décrit les avantages et les options de configuration, notamment le chiffrement transparent des données géré par le service et la prise en charge du service Bring Your Own Key.
services: sql-database
author: becczhang
manager: craigg
ms.prod: ''
ms.reviewer: carlrab
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: aliceku
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: 0ed05fd2d55f1c4c80bec9f64925be2eddddc067
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043372"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Transparent Data Encryption pour SQL Database et Data Warehouse

Transparent Data Encryption (TDE) est une technologie de chiffrement transparent des données qui contribue à protéger Azure SQL Database et Azure Data Warehouse contre les menaces d’activités malveillantes. Il assure le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux des transactions au repos, sans que cela nécessite de modifier l’application. Par défaut, TDE est activé pour tous les services Azure SQL Database nouvellement déployés. TDE ne permet pas de chiffrer la base de données **MASTER** logique dans SQL Database.  La base de données **MASTER** contient les objets nécessaires à l’exécution des opérations TDE sur les bases de données utilisateur.

Vous devrez activer TDE manuellement pour les anciennes bases de données ou pour Azure SQL Data Warehouse.  

Le chiffrement transparent des données chiffre le stockage d’une base de données entière, à l’aide d’une clé symétrique appelée clé de chiffrement de base de données. Cette clé de chiffrement de base de données est protégée par le protecteur du chiffrement transparent des données. Le protecteur est soit un certificat géré par le service (chiffrement transparent des données géré par le service), soit une clé asymétrique stockée dans Azure Key Vault (Bring Your Own Key). Vous définissez le protecteur du chiffrement transparent des données au niveau du serveur. 

Au démarrage de la base de données, la clé de chiffrement de base de données chiffrée est déchiffrée, puis utilisée pour le déchiffrement et le rechiffrement des fichiers de base de données dans le processus du Moteur de base de données SQL Server. Le chiffrement transparent des données effectue le chiffrement et le déchiffrement d’E/S en temps réel des données au niveau de la page. Chaque page est déchiffrée lorsqu’elle est lue en mémoire, puis chiffrée avant d’être écrite sur le disque. Pour obtenir une description générale du chiffrement transparent des données, consultez l’article [Chiffrement transparent des données (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server exécuté sur une machine virtuelle Azure peut également utiliser une clé asymétrique provenant de Key Vault. La procédure de configuration diffère de celle qui s’applique en cas d’utilisation d’une clé asymétrique dans SQL Database. Pour plus d’informations, consultez l’article [Gestion de clés extensible à l’aide d’Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Chiffrement transparent des données géré par le service

Pour le chiffrement transparent des données dans Azure, la clé de chiffrement de base de données est protégée par défaut par un certificat de serveur intégré. Le certificat de serveur intégré est propre à chaque serveur. Si une base de données figure dans une relation de géoréplication, la base de données primaire et la base de données géo-secondaire sont toutes deux protégées par la clé du serveur parent de la base de données primaire. Si deux bases de données sont connectées au même serveur, elles partagent le même certificat intégré. Microsoft alterne automatiquement ces certificats au moins tous les 90 jours.

En outre, Microsoft déplace et gère en toute transparence les clés en fonction des besoins en matière de géoréplication et de restaurations. 

> [!IMPORTANT]
> Toutes les bases de données SQL nouvellement créées sont chiffrées par défaut à l’aide du chiffrement transparent des données géré par le service. Les bases de données antérieures à mai 2017 et celles créées par le biais de la restauration, de la géoréplication et de la copie de base de données ne sont pas chiffrées par défaut.
>

## <a name="bring-your-own-key"></a>Service Bring Your Own Key (BYOK)

La prise en charge de BYOK vous permet de prendre le contrôle de vos clés de chiffrement transparent des données et de déterminer qui peut accéder à ces clés et quand. Key Vault, le système cloud Azure de gestion des clés externes, est le premier service de gestion des clés intégré au chiffrement transparent des données avec prise en charge de BYOK. Grâce à la prise en charge de BYOK, la clé de chiffrement de base de données est protégée par une clé asymétrique stockée dans Key Vault. La clé asymétrique ne quitte jamais Key Vault. Une fois que le serveur dispose d’autorisations d’accès à un coffre de clés, il envoie à ce dernier des demandes d’opérations de clé de base par le biais de Key Vault. Vous définissez la clé asymétrique au niveau du serveur, et toutes les bases de données de ce serveur héritent alors de cette clé.

La prise en charge du service BYOK vous permet désormais de contrôler les tâches de gestion des clés, telles que les rotations de clés et les autorisations d’accès aux coffres de clés. Vous pouvez également supprimer des clés et activer les fonctions d’audit et de génération de rapports sur toutes les clés de chiffrement. Key Vault centralise la gestion des clés et utilise des modules de sécurité matériels étroitement surveillés. Key Vault promeut la séparation de la gestion des clés et des données pour favoriser la conformité aux réglementations. Pour en savoir plus sur Key Vault, consultez la [page de documentation concernant Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

Pour plus d’informations sur le chiffrement transparent des données avec prise en charge du service BYOK pour SQL Database et Data Warehouse, consultez l’article [Transparent data encryption with Bring Your Own Key support](transparent-data-encryption-byok-azure-sql.md) (Chiffrement transparent des données avec prise en charge du service Bring Your Own Key).

Pour commencer à utiliser le chiffrement transparent des données avec prise en charge du service BYOK, consultez le guide de procédure [Turn on transparent data encryption by using your own key from Key Vault by using PowerShell](transparent-data-encryption-byok-azure-sql-configure.md) (Activer le chiffrement transparent des données en utilisant votre propre clé Key Vault à l’aide de PowerShell).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Déplacer une base de données protégée par le chiffrement transparent des données

Vous n’avez pas besoin de déchiffrer les bases de données pour leur appliquer des opérations dans Azure. Les paramètres de chiffrement transparent des données définis sur la base de données source ou la base de données primaire sont hérités de manière transparente sur la cible. Les opérations incluses sont les suivantes :
- géorestauration ;
- limite de restauration dans le temps en libre-service ;
- restauration d’une base de données supprimée ;
- géoréplication active ;
- création d’une copie de base de données.

Lorsque vous exportez une base de données protégée par le chiffrement transparent des données, le contenu exporté de la base de données n’est pas chiffré. Ce contenu exporté est stocké dans des fichiers BACPAC non chiffrés. Veillez à protéger les fichiers BACPAC de façon appropriée et à activer le chiffrement transparent des données une fois la nouvelle base de données importée.

Par exemple, si le fichier BACPAC est exporté à partir d’une instance SQL Server locale, le contenu importé de la nouvelle base de données n’est pas chiffré automatiquement. De même, si le fichier BACPAC est exporté vers une instance SQL Server locale, la nouvelle base de données n’est pas chiffrée automatiquement.

La seule exception survient lorsque vous procédez à une exportation à destination ou en provenance d’une base de données SQL. Le chiffrement transparent des données est activé dans la nouvelle base de données, mais le fichier BACPAC proprement dit n’est toujours pas chiffré.

## <a name="manage-transparent-data-encryption-in-the-azure-portal"></a>Gérer le chiffrement transparent des données dans le Portail Azure

Pour configurer le chiffrement transparent des données par le biais du Portail Azure, vous devez être connecté en tant que Propriétaire, Contributeur ou Gestionnaire de sécurité SQL Azure. 

Vous définissez le chiffrement transparent des données au niveau de la base de données. Pour activer le chiffrement transparent des données sur une base de données, accédez au [Portail Azure](https://portal.azure.com) et connectez-vous à l’aide de votre compte Administrateur ou Contributeur Azure. Recherchez les paramètres de chiffrement transparent des données pour votre base de données utilisateur. Le chiffrement transparent des données géré par le service est utilisé par défaut. Un certificat de chiffrement transparent des données est automatiquement généré pour le serveur qui contient la base de données. 

![Chiffrement transparent des données géré par le service](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Vous définissez la clé principale du chiffrement transparent des données, également désignée sous le nom de protecteur du chiffrement transparent des données, au niveau du serveur. Pour utiliser le chiffrement transparent des données avec prise en charge de Bring Your Own Key et protéger vos bases de données avec une clé Key Vault, consultez les paramètres de chiffrement transparent des données pour votre serveur. 

![Chiffrement transparent des données avec prise en charge de Bring Your Own Key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png) 

## <a name="manage-transparent-data-encryption-by-using-powershell"></a>Gérer le chiffrement transparent des données à l’aide de PowerShell

Pour configurer le chiffrement transparent des données par le biais de PowerShell, vous devez être connecté en tant que Propriétaire, Contributeur ou Gestionnaire de sécurité SQL Azure. 

| Applet de commande | Description |
| --- | --- |
| [Set-AzureRmSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) |Active ou désactive le chiffrement transparent des données pour une base de données|
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) |Obtient l’état du chiffrement transparent des données pour une base de données |
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption-Activity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) |Vérifie l’état d’avancement du chiffrement d’une base de données |
| [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) |Ajoute une clé Key Vault à une instance SQL Server |
| [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) |Obtient les clés Key Vault pour une instance SQL Server  |
| [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) |Définit le protecteur du chiffrement transparent des données pour une instance SQL Server |
| [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) |Obtient le protecteur du chiffrement transparent des données |
| [Remove-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) |Supprime une clé Key Vault d’une instance SQL Server |
|  | |

## <a name="manage-transparent-data-encryption-by-using-transact-sql"></a>Gérer le chiffrement transparent des données à l’aide de Transact-SQL

Connectez-vous à la base de données à l’aide d’un ID de connexion d’administrateur ou de membre du rôle **dbmanager** dans la base de données MASTER.

| Commande | Description |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF chiffre ou déchiffre une base de données |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Renvoie des informations sur l’état de chiffrement d’une base de données et sur ses clés de chiffrement de base de données associées |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Renvoie des informations sur l’état de chiffrement de chaque nœud d’entrepôt de données et sur ses clés de chiffrement de base de données associées | 
|  | |

Vous ne pouvez pas basculer le protecteur du chiffrement transparent des données vers une clé provenant de Key Vault à l’aide de Transact-SQL. Utilisez PowerShell ou le Portail Azure.

## <a name="manage-transparent-data-encryption-by-using-the-rest-api"></a>Gérer le chiffrement transparent des données à l’aide de l’API REST
 
Pour configurer le chiffrement transparent des données par le biais de l’API REST, vous devez être connecté en tant que Propriétaire, Contributeur ou Gestionnaire de sécurité SQL Azure. 

| Commande | Description |
| --- | --- |
|[Créer ou mettre à jour un serveur](/rest/api/sql/servers/createorupdate)|Ajoute une identité Azure Active Directory à une instance SQL Server (permettant d’accorder l’accès à Key Vault)|
|[Créer ou mettre à jour une clé de serveur](/rest/api/sql/serverkeys/createorupdate)|Ajoute une clé Key Vault à une instance SQL Server|
|[Supprimer une clé de serveur](/rest/api/sql/serverkeys/delete)|Supprime une clé Key Vault d’une instance SQL Server|
|[Obtenir des clés de serveur](/rest/api/sql/serverkeys/get)|Obtient une clé Key Vault spécifique d’une instance SQL Server|
|[Répertorier les clés de serveur par serveur](/rest/api/sql/serverkeys/listbyserver)|Obtient les clés Key Vault pour une instance SQL Server |
|[Créer ou mettre à jour le protecteur du chiffrement](/rest/api/sql/encryptionprotectors/createorupdate)|Définit le protecteur du chiffrement transparent des données pour une instance SQL Server|
|[Obtenir le protecteur du chiffrement](/rest/api/sql/encryptionprotectors/get)|Obtient le protecteur du chiffrement transparent des données pour une instance SQL Server|
|[Répertorier les protecteurs du chiffrement par serveur](/rest/api/sql/encryptionprotectors/listbyserver)|Obtient les protecteurs du chiffrement transparent des données pour une instance SQL Server |
|[Créer ou mettre à jour la configuration du chiffrement transparent des données](/rest/api/sql/transparentdataencryptions/createorupdate)|Active ou désactive le chiffrement transparent des données pour une base de données|
|[Obtenir la configuration du chiffrement transparent des données](/rest/api/sql/transparentdataencryptions/get)|Obtient la configuration du chiffrement transparent des données pour une base de données|
|[Répertorier les résultats de configuration du chiffrement transparent des données](/rest/api/sql/transparentdataencryptionactivities/ListByConfiguration)|Obtient le résultat du chiffrement d’une base de données|

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une description générale du chiffrement transparent des données, consultez l’article [Chiffrement transparent des données (TDE)](https://docs.microsoft.com/sql/relational-databases/security/transparent-data-encryption).
- Pour plus d’informations sur le chiffrement transparent des données avec prise en charge du service BYOK pour SQL Database et Data Warehouse, consultez l’article [Transparent data encryption with Bring Your Own Key support](transparent-data-encryption-byok-azure-sql.md) (Chiffrement transparent des données avec prise en charge du service Bring Your Own Key).
- Pour commencer à utiliser le chiffrement transparent des données avec prise en charge du service BYOK, consultez le guide de procédure [Turn on transparent data encryption by using your own key from Key Vault by using PowerShell](transparent-data-encryption-byok-azure-sql-configure.md) (Activer le chiffrement transparent des données en utilisant votre propre clé Key Vault à l’aide de PowerShell).
- Pour plus d’informations sur Key Vault, consultez la [page de documentation concernant Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
