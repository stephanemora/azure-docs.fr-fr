---
title: Configurer les paramètres du serveur - Azure PowerShell - Azure Database for MariaDB
description: Cet article explique comment configurer les paramètres de service d'Azure Database for MariaDB à l'aide de PowerShell.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: conceptual
ms.date: 5/26/2020
ms.openlocfilehash: d7a6f87b704a2e366e0a4e1fc647e83ed88c486c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84039410"
---
# <a name="customize-azure-database-for-mariadb-server-parameters-using-powershell"></a>Personnaliser les paramètres du serveur Azure Database for MariaDB à l'aide de PowerShell

Vous pouvez lister, afficher et mettre à jour les paramètres de configuration d'un serveur Azure Database for MariaDB à l'aide de PowerShell. Un sous-ensemble de configurations de moteur est exposé au niveau du serveur et peut être modifié.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce guide pratique, il vous faut :

- Le [module Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) installé localement ou [Azure Cloud Shell](https://shell.azure.com/) dans le navigateur
- Un [serveur Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Tant que le module PowerShell Az.MariaDb est en préversion, vous devez l'installer séparément du module Az PowerShell à l'aide de la commande suivante : `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Une fois le module PowerShell Az.MariaDb généralement disponible, il devient partie intégrante des versions futures du module Az PowerShell et disponible en mode natif dans Azure Cloud Shell.

Si vous choisissez d’utiliser PowerShell en local, connectez-vous à votre compte Azure à l’aide de la cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Répertorier les paramètres de configuration de serveur pour Azure Database pour MariaDB

Pour lister tous les paramètres modifiables sur un serveur et leurs valeurs, exécutez l’applet de commande `Get-AzMariaDbConfiguration`.

L’exemple suivant liste les paramètres de configuration du serveur **mydemoserver** dans le groupe de ressources **myresourcegroup**.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Pour obtenir la définition de chacun des paramètres répertoriés, consultez la section de référence MariaDB dans [Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html) (Variables système du serveur).

## <a name="show-server-configuration-parameter-details"></a>Affichage des détails des paramètres de configuration du serveur

Pour afficher les détails d’un paramètre de configuration particulier d’un serveur, exécutez l’applet de commande `Get-AzMariaDbConfiguration` et spécifiez le paramètre **Name**.

Cet exemple montre les détails du paramètre de configuration **slow\_query\_log** pour le serveur **mydemoserver** dans le groupe de ressources **myresourcegroup**.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modifier une valeur de paramètre de configuration de serveur

Vous pouvez également modifier la valeur d’un paramètre de configuration de serveur, ce qui a pour effet de mettre à jour la valeur de configuration sous-jacente du moteur du serveur MariaDB. Pour mettre à jour la configuration, utilisez l’applet de commande `Update-AzMariaDbConfiguration`.

Mettez à jour le paramètre de configuration **slow\_query\_log** du serveur **mydemoserver** dans le groupe de ressources **myresourcegroup**.

```azurepowershell-interactive
Update-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Croissance automatique du stockage sur le serveur Azure Database for MariaDB à l'aide de PowerShell](howto-auto-grow-storage-powershell.md)
