---
title: Gérer des réplicas en lecture - Azure PowerShell – Azure Database pour MySQL
description: Découvrez comment configurer et gérer des réplicas en lecture dans Azure Database pour MySQL à l’aide de PowerShell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 8/24/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c85af0f4078010fa5b6a1d116b3bfda942c0490c
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816930"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-powershell"></a>Créer et gérer des réplicas en lecture dans Azure Database pour MySQL à l’aide de PowerShell

Dans cet article, apprenez à créer et à gérer des réplicas en lecture dans le service Azure Database pour MySQL à l’aide de PowerShell. Pour en savoir plus sur les réplicas en lecture, consultez [vue d’ensemble](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Vous pouvez créer et gérer des réplicas en lecture à l’aide de PowerShell.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce guide pratique, il vous faut :

- Le [module Az PowerShell](/powershell/azure/install-az-ps) installé localement ou [Azure Cloud Shell](https://shell.azure.com/) dans le navigateur
- Un [serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Tant que le module Az.MySql PowerShell est en préversion, vous devez l’installer séparément du module Az PowerShell à l’aide de la commande suivante : `Install-Module -Name Az.MySql -AllowPrerelease`.
> Une fois que le module Az.MySql PowerShell est en disponibilité générale, il devient partie intégrante des versions futures du module Az PowerShell et disponible en mode natif dans Azure Cloud Shell.

Si vous choisissez d’utiliser PowerShell en local, connectez-vous à votre compte Azure à l’aide de la cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> La fonctionnalité de réplica en lecture est disponible uniquement pour les serveurs Azure Database pour MySQL dans les niveaux tarifaires Usage général ou Mémoire optimisée. Vérifiez que le serveur maître se trouve dans l’un de ces niveaux tarifaires.

### <a name="create-a-read-replica"></a>Créer un réplica en lecture

> [!IMPORTANT]
> Lorsque vous créez un réplica pour un serveur maître qui ne dispose d’aucun réplica existant, le serveur maître commence par redémarrer pour se préparer pour la réplication. Tenez-en compte et effectuez ces opérations en période creuse.

Un serveur réplica en lecture peut être créé en utilisant la commande suivante :

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

La commande `New-AzMySqlReplica` requiert les paramètres suivants :

| Paramètre | Valeur d'exemple | Description  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  Groupe de ressources où le serveur réplica est créé.  |
| Nom | mydemoreplicaserver | Nom du nouveau serveur réplica créé. |

Pour créer un réplica en lecture entre régions, utilisez le paramètre **Location**. L’exemple suivant crée un réplica dans la région **USA Ouest**.

```azurepowershell-interactive
Get-AzMySqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Pour en savoir plus sur les régions dans lesquelles vous pouvez créer un réplica, consultez l’article [Concepts relatifs aux réplicas en lecture](concepts-read-replicas.md).

Par défaut, les réplicas en lecture sont créés avec la même configuration de serveur que le serveur maître, sauf si le paramètre **Sku** est spécifié.

> [!NOTE]
> Il est recommandé que la configuration du serveur réplica soit maintenue à des valeurs égales ou supérieures à celles du serveur maître pour garantir que le serveur réplica est à la hauteur du serveur maître.

### <a name="list-replicas-for-a-master-server"></a>Répertorier les réplicas d'un serveur maître

Pour afficher tous les réplicas d'un serveur maître donné, exécutez la commande suivante :

```azurepowershell-interactive
Get-AzMySqlReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

La commande `Get-AzMySqlReplica` requiert les paramètres suivants :

| Paramètre | Valeur d'exemple | Description  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  Groupe de ressources dans lequel le serveur réplica sera créé.  |
| ServerName | mydemoserver | Nom ou ID du serveur maître. |

### <a name="delete-a-replica-server"></a>Supprimer un serveur réplica

La suppression d’un serveur réplica en lecture peut être effectuée en exécutant la cmdlet `Remove-AzMySqlServer`.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-master-server"></a>Supprimer un serveur maître

> [!IMPORTANT]
> La suppression d’un serveur maître arrête la réplication vers tous les serveurs réplicas et supprime le serveur maître lui-même. Les serveurs réplicas deviennent des serveurs autonomes qui prennent désormais en charge la lecture et les écritures.

Pour supprimer un serveur maître, vous pouvez exécuter la cmdlet `Remove-AzMySqlServer`.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Redémarrer un serveur Azure Database pour MySQL à l’aide de PowerShell](howto-restart-server-powershell.md)
