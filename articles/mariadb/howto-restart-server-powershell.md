---
title: Redémarrer un serveur – Azure PowerShell - Azure Database for MariaDB
description: Cet article explique comment redémarrer un serveur Azure Database for MariaDB à l’aide de PowerShell.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 37fb724b83e80c1265755e6440f152143d419051
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503070"
---
# <a name="restart-azure-database-for-mariadb-server-using-powershell"></a>Redémarrer un serveur Azure Database for MariaDB à l’aide de PowerShell

Cette rubrique explique comment redémarrer un serveur Azure Database for MariaDB. Vous pouvez avoir besoin de redémarrer votre serveur pour des raisons de maintenance, ce qui entraîne une brève interruption pendant l’opération.

Le redémarrage du serveur est bloqué si le service est occupé. Par exemple, le service peut traiter une opération précédemment demandée, telle que la mise à l’échelle de vCores.

Le temps nécessaire à un redémarrage varie selon le processus de récupération de MariaDB. Pour réduire le délai de redémarrage, nous vous recommandons de diminuer la quantité d’activités se produisant sur le serveur avant le redémarrage.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce guide pratique, il vous faut :

- Le [module Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) installé localement ou [Azure Cloud Shell](https://shell.azure.com/) dans le navigateur
- Un [serveur Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Tant que le module PowerShell Az.MariaDb est en préversion, vous devez l'installer séparément du module Az PowerShell à l'aide de la commande suivante : `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Une fois le module PowerShell Az.MariaDb généralement disponible, il devient partie intégrante des versions futures du module Az PowerShell et disponible en mode natif dans Azure Cloud Shell.

Si vous choisissez d’utiliser PowerShell en local, connectez-vous à votre compte Azure à l’aide de la cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Redémarrez le serveur

Redémarrez le serveur en utilisant la commande suivante :

```azurepowershell-interactive
Restart-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer un serveur Azure Database for MariaDB à l’aide de PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md)
