---
title: Script Azure CLI - Mettre un pool élastique SQL à l’échelle dans Azure SQL Database | Microsoft Docs
description: Exemple de script Azure CLI pour mettre à l’échelle un pool élastique SQL dans Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: b378bef296f7cf6546887bcf760a4e14ed66a385
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569824"
---
# <a name="use-cli-to-scale-an-elastic-pool-in-azure-sql-database"></a>Utiliser l’interface CLI pour mettre à l’échelle un pool élastique dans Azure SQL Database

Cet exemple de script Azure CLI crée des pools élastiques, déplace des bases de données mises en pool et modifie les tailles de calcul d’un pool élastique.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Move database between pools")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, un serveur SQL Database, une instance de base de données et des règles de pare-feu SQL Database. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az-sql-server-create) | Crée un serveur SQL Database qui héberge des bases de données uniques et des pools élastiques. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create) | Crée un pool élastique. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create) | Crée une base de données unique ou mise en pool. |
| [az sql elastic-pools update](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) | Met à jour un pool élastique : dans cet exemple, modifie l’eDTU affecté. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI SQL Database dans [Documentation Azure SQL Database](../sql-database-cli-samples.md).
