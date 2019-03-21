---
title: Exemples de scripts Azure CLI pour SQL Database | Microsoft Docs
description: Exemples de script Azure CLI pour la création et la gestion de serveurs, de pools élastiques, de bases de données et de pare-feux Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/03/2019
ms.openlocfilehash: 74d322c9e7d45a26f9e095bd7ae9f2bbe506ba49
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306933"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Exemples Azure CLI pour Azure SQL Database

Vous pouvez configurer Azure SQL Database à l’aide d’<a href="/cli/azure">Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="single-database--elastic-pools"></a>Base de données unique et pools élastiques

Le tableau suivant comprend des liens vers des exemples de scripts Azure CLI pour Azure SQL Database.

| |  |
|---|---|
|**Créer une base de données unique et un pool élastique**||
| [Créer une base de données unique et configurer une règle de pare-feu](scripts/sql-database-create-and-configure-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cet exemple de script CLI crée une base de données SQL Azure et configure une règle de pare-feu au niveau du serveur. |
| [Créer des pools élastiques et déplacer les bases de données mises en pool](scripts/sql-database-move-database-between-pools-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cet exemple de script CLI crée des pools élastiques SQL, déplace des bases de données SQL Azure mises en pool et modifie les tailles de calcul.|
|**Mettre à l’échelle une base de données unique et un pool élastique**||
| [Mettre à l’échelle une base de données unique](scripts/sql-database-monitor-and-scale-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cet exemple de script CLI met à l’échelle une base de données Azure SQL vers une autre taille de calcul après avoir déterminé la taille de la base de données. |
| [Mettre à l’échelle un pool élastique](scripts/sql-database-scale-pool-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cet exemple de script CLI met à l’échelle un pool élastique SQL à une taille de calcul différente.  |
|||

Découvrez-en plus sur l’[API de base de données unique Azure CLI ](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>Instance gérée

Le tableau suivant comprend des liens vers des exemples de scripts Azure CLI pour Azure SQL Database - Managed Instance.

| |  |
|---|---|
| [Créer une instance gérée](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/) | Ce script CLI montre comment créer une instance managée. |
| [Mettre à jour une instance managée](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) | Ce script CLI montre comment mettre à jour une instance managée. |
| [Déplacer une base de données vers une autre instance managée](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) | Ce script CLI montre comment restaurer une sauvegarde de base de données d’une instance vers une autre. |
|||

Découvrez-en plus sur l’[API Managed Instance Azure CLI](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances) et consultez [d’autres exemples ici](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).
