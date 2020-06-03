---
title: Exemple CLI – Groupe de basculement – Pool élastique Azure SQL Database
description: Exemple de script Azure CLI pour créer un pool élastique Azure SQL Database, l’ajouter à un groupe de basculement et tester le basculement.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 42cc10398fce831b6343f69df03104d428700ac3
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84022775"
---
# <a name="use-cli-to-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Utiliser CLI pour ajouter un pool élastique Azure SQL Database à un groupe de basculement

Cet exemple de script Azure CLI crée une base de données unique, y ajoute un pool élastique, crée un groupe de basculement, puis teste le basculement.

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemple de script

### <a name="sign-in-to-azure"></a>Connexion à Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Exécuter le script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-cli.sh "Add elastic pool to a failover group")]

### <a name="clean-up-deployment"></a>Nettoyer le déploiement

Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Informations de référence sur l’exemple

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| | |
|---|---|
| [az sql elastic-pool](/cli/azure/sql/elastic-pool) | Commandes de pool élastique. |
| [az sql failover-group ](/cli/azure/sql/failover-group) | Commandes de groupe de basculement. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure CLI SQL Database dans la page [Scripts Azure CLI Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
