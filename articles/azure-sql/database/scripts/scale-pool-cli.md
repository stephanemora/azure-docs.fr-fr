---
title: 'L’interface de ligne de commande Azure CLI : Mettre à l’échelle un pool élastique'
description: Utilisez un exemple de script Azure CLI pour mettre à l’échelle un pool élastique dans Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: 30862a85796fbe4373a88d80d9a6d26c8cf98901
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84196732"
---
# <a name="use-the-azure-cli-to-scale-an-elastic-pool-in-azure-sql-database"></a>Utiliser Azure CLI pour mettre à l’échelle un pool élastique dans Azure SQL Database

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Cet exemple de script Azure CLI crée des pools élastiques dans Azure SQL Database, déplace des bases de données mises en pool et modifie les tailles de calcul d’un pool élastique.

Si vous choisissez d’installer et d’utiliser localement Azure CLI, cette rubrique vous impose d’exécuter Azure CLI version 2.0 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemple de script

### <a name="sign-in-to-azure"></a>Connexion à Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Exécuter le script

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Move database between pools")]

### <a name="clean-up-deployment"></a>Nettoyer le déploiement

Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Informations de référence sur l’exemple

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| | |
|---|---|
| [az sql server](/cli/azure/sql/server) | Commandes de serveur. |
| [az sql db](/cli/azure/sql/db) | Commandes de base de données. |
| [az sql elastic-pools](/cli/azure/sql/elastic-pool) | Commandes de pool élastique. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI SQL Database dans [Documentation Azure SQL Database](../az-cli-script-samples-content-guide.md).
