---
title: Gérer des réplicas en lecture - Azure PowerShell – Azure Database pour MySQL
description: Découvrez comment configurer et gérer des réplicas en lecture dans Azure Database pour MySQL à l’aide de PowerShell.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 06/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f4980692be64c2a8b3918d2dbaab5ef9c983f453
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531368"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-powershell"></a>Créer et gérer des réplicas en lecture dans Azure Database pour MySQL à l’aide de PowerShell

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

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
> La fonctionnalité de réplica en lecture est disponible uniquement pour les serveurs Azure Database pour MySQL dans les niveaux tarifaires Usage général ou Mémoire optimisée. Vérifiez que le serveur source se trouve dans l’un de ces niveaux tarifaires.
>
>Si le GTID est activé sur un serveur primaire (`gtid_mode` = ON), il sera également activé sur les réplicas nouvellement créés, et ceux-ci utiliseront la réplication basée sur GTID. Pour en savoir plus, reportez-vous à [Identificateur de transaction global (GTID)](concepts-read-replicas.md#global-transaction-identifier-gtid)

### <a name="create-a-read-replica"></a>Créer un réplica en lecture

> [!IMPORTANT]
> Lorsque vous créez un réplica pour un serveur source qui n’en a pas, ce dernier commence par redémarrer afin de se préparer à la réplication. Tenez-en compte et effectuez ces opérations en période creuse.

Un serveur réplica en lecture peut être créé en utilisant la commande suivante :

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

La commande `New-AzMySqlReplica` requiert les paramètres suivants :

| Paramètre | Valeur d'exemple | Description  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  Groupe de ressources où le serveur réplica est créé.  |
| Nom | mydemoreplicaserver | Nom du nouveau serveur réplica créé. |

Pour créer un réplica en lecture entre régions, utilisez le paramètre **Location**. L’exemple suivant crée un réplica dans la région **USA Ouest**.

```azurepowershell-interactive
Get-AzMySqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

> [!NOTE]
> Pour en savoir plus sur les régions dans lesquelles vous pouvez créer un réplica, consultez l’article [Concepts relatifs aux réplicas en lecture](concepts-read-replicas.md). 

Par défaut, les réplicas en lecture sont créés avec la même configuration de serveur que le serveur source, sauf si le paramètre **Sku** est spécifié.

> [!NOTE]
> Il est recommandé de maintenir la configuration du serveur réplica à des valeurs égales ou supérieures à celles du serveur source pour garantir que le réplica sera à la hauteur du serveur maître.

### <a name="list-replicas-for-a-source-server"></a>Répertorier les réplicas d'un serveur source

Pour afficher tous les réplicas d'un serveur source donné, exécutez la commande suivante :

```azurepowershell-interactive
Get-AzMySqlReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

La commande `Get-AzMySqlReplica` requiert les paramètres suivants :

| Paramètre | Valeur d'exemple | Description  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  Groupe de ressources dans lequel le serveur réplica sera créé.  |
| ServerName | mydemoserver | Nom ou ID du serveur source. |

### <a name="delete-a-replica-server"></a>Supprimer un serveur réplica

La suppression d’un serveur réplica en lecture peut être effectuée en exécutant la cmdlet `Remove-AzMySqlServer`.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-source-server"></a>Supprimer un serveur source

> [!IMPORTANT]
> La suppression d’un serveur source arrête la réplication vers tous les serveurs réplicas et supprime le serveur source proprement dit. Les serveurs réplicas deviennent des serveurs autonomes qui prennent désormais en charge la lecture et les écritures.

Pour supprimer un serveur source, vous pouvez exécuter la cmdlet `Remove-AzMySqlServer`.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

### <a name="known-issue"></a>Problème connu

Il existe deux générations de stockage que les serveurs des niveaux Usage général et À mémoire optimisée utilisent : le stockage Usage général v1 (prend en charge jusqu’à 4 To) et le stockage Usage général v2 (prend en charge jusqu’à 16 To).
Le serveur source et le serveur réplica doivent avoir le même type de stockage. Le [stockage v2 universel](./concepts-pricing-tiers.md#general-purpose-storage-v2-supports-up-to-16-tb-storage) n’étant pas disponible dans toutes les régions, veillez à choisir la région de réplica appropriée lorsque vous utilisez l’emplacement avec PowerShell pour la création de réplica en lecture. Pour savoir comment identifier le type de stockage de votre serveur source, consultez le lien [Comment puis-je déterminer le type de stockage sur lequel mon serveur s’exécute ?](./concepts-pricing-tiers.md#how-can-i-determine-which-storage-type-my-server-is-running-on) 

Si vous choisissez une région dans laquelle vous ne pouvez pas créer de réplica en lecture pour votre serveur source, vous rencontrerez le problème suivant : le déploiement continuera à s’exécuter comme indiqué dans la figure ci-dessous, puis expirera avec l’erreur *« L’opération d’approvisionnement en ressources ne s’est pas terminée dans le délai imparti »* .

[ :::image type="content" source="media/howto-read-replicas-powershell/replcia-ps-known-issue.png" alt-text="Erreur CLI d’un réplica en lecture":::](media/howto-read-replicas-powershell/replcia-ps-known-issue.png#lightbox)


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Redémarrer un serveur Azure Database pour MySQL à l’aide de PowerShell](howto-restart-server-powershell.md)
