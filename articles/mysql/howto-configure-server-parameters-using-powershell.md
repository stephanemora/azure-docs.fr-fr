---
title: Configurer les paramètres de serveur - Azure PowerShell - Azure Database pour MySQL
description: Cet article décrit comment configurer les paramètres de service dans Azure Database pour MySQL à l’aide de PowerShell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 4/29/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cb17c8631392972c429220b846720447be48cecf
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495809"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-powershell"></a>Configurer les paramètres de serveur dans Azure Database pour MySQL à l’aide de PowerShell

Vous pouvez lister, afficher et mettre à jour les paramètres de configuration d’un serveur Azure Database pour MySQL à l’aide de PowerShell. Un sous-ensemble de configurations de moteur est exposé au niveau du serveur et peut être modifié.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce guide pratique, il vous faut :

- Le [module Az PowerShell](/powershell/azure/install-az-ps) installé localement ou [Azure Cloud Shell](https://shell.azure.com/) dans le navigateur
- Un [serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Tant que le module PowerShell Az.MySql est en préversion, vous devez l’installer séparément du module Az PowerShell à l’aide de la commande suivante : `Install-Module -Name Az.MySql -AllowPrerelease`.
> Une fois le module PowerShell Az.MySql généralement disponible, il devient partie intégrante des versions futures du module Az PowerShell et disponible en mode natif dans Azure Cloud Shell.

Si vous choisissez d’utiliser PowerShell localement, connectez-vous à votre compte Azure à l’aide de l’applet de commande [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Répertorier les paramètres de configuration de serveur pour Azure Database pour MySQL

Pour lister tous les paramètres modifiables sur un serveur et leurs valeurs, exécutez l’applet de commande `Get-AzMySqlConfiguration`.

L’exemple suivant liste les paramètres de configuration du serveur **mydemoserver** dans le groupe de ressources **myresourcegroup**.

```azurepowershell-interactive
Get-AzMySqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Pour obtenir la définition de chacun des paramètres répertoriés, consultez la section de référence MySQL dans [Server System Variables (Variables système du serveur)](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Affichage des détails des paramètres de configuration du serveur

Pour afficher les détails d’un paramètre de configuration particulier d’un serveur, exécutez l’applet de commande `Get-AzMySqlConfiguration` et spécifiez le paramètre **Name**.

Cet exemple montre les détails du paramètre de configuration **slow\_query\_log** pour le serveur **mydemoserver** dans le groupe de ressources **myresourcegroup**.

```azurepowershell-interactive
Get-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modifier une valeur de paramètre de configuration de serveur

Vous pouvez également modifier la valeur d’un paramètre de configuration de serveur, ce qui a pour effet de mettre à jour la valeur de configuration sous-jacente du moteur du serveur MySQL. Pour mettre à jour la configuration, utilisez l’applet de commande `Update-AzMySqlConfiguration`.

Mettez à jour le paramètre de configuration **slow\_query\_log** du serveur **mydemoserver** dans le groupe de ressources **myresourcegroup**.

```azurepowershell-interactive
Update-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Croissance automatique du stockage sur le serveur Azure Database pour MySQL à l’aide de PowerShell](howto-auto-grow-storage-powershell.md)
