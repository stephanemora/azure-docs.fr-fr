---
title: Exemples de script Azure CLI
description: Exemples de script Azure CLI pour la création et la gestion de serveurs, de pools élastiques, de bases de données et de pare-feux Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 459a5ea69e11a8614c572f68fce039b6cabbb1ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061717"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Exemples Azure CLI pour Azure SQL Database

Vous pouvez configurer Azure SQL Database à l’aide d’<a href="/cli/azure">Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

# <a name="single-database--elastic-pools"></a>[Base de données unique et pools élastiques](#tab/single-database)

| | |
|---|---|
|**Créer une base de données unique et un pool élastique**||
| [Créer une base de données unique et configurer une règle de pare-feu](scripts/sql-database-create-and-configure-database-cli.md) | Crée une base de données Azure SQL et configure une règle de pare-feu au niveau du serveur. |
| [Créer des pools élastiques et déplacer les bases de données mises en pool](scripts/sql-database-move-database-between-pools-cli.md) | Crée des pools élastiques SQL, déplace des bases de données Azure SQL mises en pool et change les tailles de calcul. |
|**Mettre à l’échelle une base de données unique et un pool élastique**||
| [Mettre à l’échelle une base de données unique](scripts/sql-database-monitor-and-scale-database-cli.md) | Met à l’échelle une base de données Azure SQL vers une autre taille de calcul après avoir déterminé la taille de la base de données. |
| [Mettre à l’échelle un pool élastique](scripts/sql-database-scale-pool-cli.md) | Met à l’échelle un pool élastique SQL à une taille de calcul différente. |
|**Configurer la géoréplication et le basculement**||
| [Ajouter une base de données à un groupe de basculement](scripts/sql-database-add-single-db-to-failover-group-cli.md)| Crée une base de données et un groupe de basculement, ajoute la base de données au groupe de basculement, puis teste le basculement sur le serveur secondaire. |
| [Configurer un groupe de basculement pour un pool de bases de données élastique](scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Crée une base de données, l’ajoute à un pool élastique, ajoute le pool élastique à un groupe de basculement, puis teste le basculement sur le serveur secondaire. |
| [Configurer et basculer une base de données unique à l’aide de la géoréplication active](scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Configure la géoréplication active pour une base de données Azure SQL et la fait basculer vers le réplica secondaire. |
| [Configurer et basculer une base de données mise en pool à l’aide de la géoréplication active](scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Configure la géoréplication active pour une base de données Azure SQL dans un pool élastique SQL, puis la fait basculer vers le réplica secondaire. |
| **Audit et détection des menaces** |
| [Configurer l’audit et la détection des menaces](scripts/sql-database-auditing-and-threat-detection-cli.md)| Configure les stratégies d’audit et de détection des menaces pour une base de données Azure SQL. |
| **Sauvegarder, restaurer, copier et importer une base de données**||
| [Sauvegarder une base de données](scripts/sql-database-backup-database-cli.md)| Sauvegarde une base de données Azure SQL dans une sauvegarde de stockage Azure. |
| [Restaurer une base de données](scripts/sql-database-restore-database-cli.md)| Restaure une base de données Azure SQL à partir d’une sauvegarde géo-redondante et restaure une base de données Azure SQL supprimée dans la dernière sauvegarde. |
| [Copier une base de données sur un nouveau serveur](scripts/sql-database-copy-database-to-new-server-cli.md) | Crée une copie d’une base de données Azure SQL existante sur un nouveau serveur Azure SQL. |
| [Importer une base de données à partir d’un fichier bacpac](scripts/sql-database-import-from-bacpac-cli.md)| Importe une base de données vers un serveur Azure SQL à partir d’un fichier *.bacpac*. |
|||

Découvrez-en plus sur l’[API de base de données unique Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

# <a name="managed-instance"></a>[Managed Instance](#tab/managed-instance)

Le tableau suivant comprend des liens vers des exemples de scripts Azure CLI pour Azure SQL Database - Managed Instance.

| | |
|---|---|
| **Créer une instance gérée**||
| [Créer une instance gérée](scripts/sql-database-create-configure-managed-instance-cli.md)| Crée une instance managée. |
| **Configurer Transparent Data Encryption (TDE)**||
| [Gérer Transparent Data Encryption dans une instance managée à l’aide d’Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Configure Transparent Data Encryption (TDE) dans Azure SQL Managed Instance à l’aide d’Azure Key Vault avec différents scénarios clés. |
|**Configurer un groupe de basculement**||
| [Configurer un groupe de basculement pour une instance gérée](scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Crée deux instances managées, les ajoute à un groupe de basculement, puis teste le basculement de l’instance managée principale vers l’instance managée secondaire. |
|||

Pour obtenir des exemples supplémentaires sur les instances managées, consultez les scripts de [création](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), de [mise à jour](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), de [déplacement d’une base de données](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) et d’[utilisation](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

Découvrez-en plus sur l’[API d’instance managée Azure CLI](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances).

---
