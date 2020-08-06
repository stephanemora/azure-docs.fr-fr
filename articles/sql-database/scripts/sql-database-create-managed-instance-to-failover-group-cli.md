---
title: 'Azure CLI : Ajouter une instance managée à un groupe de basculement'
description: Exemple de script Azure CLI permettant de créer une instance Azure SQL Managed Instance, l’ajouter à un groupe de basculement, puis tester le basculement.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: b29fcd33c8181ba61f57d7105d08e7e4956d80bc
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497254"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance-to-a-failover-group"></a>Utiliser l’interface CLI pour créer une instance Azure SQL Managed Instance pour un groupe de basculement

Cet exemple Azure CLI crée deux instances managées, les ajoute à un groupe de basculement, puis teste le basculement de l’instance managée principale vers l’instance managée secondaire.

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-scripts"></a>Exemples de scripts

### <a name="sign-in-to-azure"></a>Connexion à Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Exécuter le script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Nettoyer le déploiement

Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées. Vous devez supprimer le groupe de ressources à deux reprises. La première suppression du groupe de ressources entraîne la suppression de l’instance gérée et des clusters virtuels, mais échouera avec le message d’erreur `az group delete : Long running operation failed with status 'Conflict'.`. Exécutez la commande az group delete une deuxième fois pour supprimer toutes les ressources résiduelles ainsi que le groupe de ressources.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Informations de référence sur l’exemple

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Description |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | Commandes de réseau virtuel.  |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | Commandes de sous-réseau de réseau virtuel. |
| [az network nsg](/cli/azure/network/nsg) | Commandes de groupe de sécurité réseau. |
| [az network nsg rule](/cli/azure/network/nsg/rule)| Commandes de règle de sécurité réseau. |
| [az network route-table](/cli/azure/network/route-table) | Commandes de table de route. |
| [az sql mi](/cli/azure/sql/mi) | Commandes SQL Managed Instance. |
| [az network public-ip](/cli/azure/network/public-ip) | Commandes d’adresse IP publique réseau. |
| [az network vnet-gateway](/cli/azure/network/vnet-gateway) | Commandes de passerelle de réseau virtuel |
| [az sql instance-failover-group](/cli/azure/sql/instance-failover-group) | Commandes de groupe de basculement SQL Managed Instance. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI SQL Database dans [Documentation Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
