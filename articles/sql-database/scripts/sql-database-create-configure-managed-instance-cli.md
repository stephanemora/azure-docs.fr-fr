---
title: Exemple d’interface CLI - Créer une instance managée dans Azure SQL Database
description: Exemple de script Azure CLI afin de créer une instance managée dans Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 998bd8e39863dd9520d1a05e7fff52095c3be6c1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067447"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>Utiliser CLI pour créer une instance managée Azure SQL Database

Cet exemple de script Azure CLI crée une instance managée Azure SQL Database dans un sous-réseau dédié au sein d’un réseau virtuel. Il configure également une table de routage et un groupe de sécurité réseau pour le réseau virtuel. Une fois le script correctement exécuté, l’instance managée est accessible à partir du réseau virtuel ou d’un environnement local. Consultez [Configurer la machine virtuelle Azure pour qu’elle se connecte à une instance managée Azure SQL Database](../sql-database-managed-instance-configure-vm.md) et [Configurer une connexion point à site à une instance managée Azure SQL Database à partir d’un emplacement local](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Pour connaître les limitations, consultez les [régions prises en charge](../sql-database-managed-instance-resource-limits.md#supported-regions) et les [types d’abonnement pris en charge](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemple de script

### <a name="sign-in-to-azure"></a>Connexion à Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Exécuter le script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Nettoyer le déploiement

Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Informations de référence sur l’exemple

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| | |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | Commandes de réseau virtuel. |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | Commandes de sous-réseau de réseau virtuel. |
| [az network route-table](/cli/azure/network/route-table) | Commandes de table de route réseau. |
| [az sql mi](/cli/azure/sql/mi) | Commandes d’instance managée. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI SQL Database dans [Documentation Azure SQL Database](../sql-database-cli-samples.md).
