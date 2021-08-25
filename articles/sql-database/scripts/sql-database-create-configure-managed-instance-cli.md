---
title: 'Azure CLI : Créer une instance gérée'
description: Exemple de script Azure CLI permettant de créer une instance managée dans Azure SQL Managed Instance
services: sql-managed-instance
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma
ms.date: 03/25/2019
ms.openlocfilehash: 479eb8f343011748aa3f89b6416c9f4b8cfe9b38
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747107"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance"></a>Utiliser l’interface CLI pour créer une instance Azure SQL Managed Instance

Cet exemple de script Azure CLI crée une instance Azure SQL Managed Instance dans un sous-réseau dédié au sein d’un nouveau réseau virtuel. Il configure également une table de routage et un groupe de sécurité réseau pour le réseau virtuel. Une fois le script correctement exécuté, l’instance managée est accessible à partir du réseau virtuel ou d’un environnement local. Consultez [Configurer une machine virtuelle Azure pour qu’elle se connecte à une instance Azure SQL Managed Instance]../../azure-sql/managed-instance/connect-vm-instance-configure.md) et [Configurer une connexion point à site à une instance Azure SQL Managed Instance à partir d’un emplacement local](../../azure-sql/managed-instance/point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Pour connaître les limitations, consultez les [régions prises en charge](../../azure-sql/managed-instance/resource-limits.md#supported-regions) et les [types d’abonnement pris en charge](../../azure-sql/managed-instance/resource-limits.md#supported-subscription-types).

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

| Commande | Description |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | Commandes de réseau virtuel. |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | Commandes de sous-réseau de réseau virtuel. |
| [az network route-table](/cli/azure/network/route-table) | Commandes de table de route réseau. |
| [az sql mi](/cli/azure/sql/mi) | Commandes SQL Managed Instance. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI SQL Database dans [Documentation Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
