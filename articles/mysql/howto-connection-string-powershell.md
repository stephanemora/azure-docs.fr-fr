---
title: Générer une chaîne de connexion avec PowerShell - Azure Database pour MySQL
description: Cet article fournit un exemple Azure PowerShell pour générer une chaîne de connexion à Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: 83a33f1f2f22af6aafbe207c56cdc022a4b9510a
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122641158"
---
# <a name="how-to-generate-an-azure-database-for-mysql-connection-string-with-powershell"></a>Comment générer une chaîne de connexion à Azure Database for MySQL avec PowerShell

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Cet article montre comment générer une chaîne de connexion pour un serveur Azure Database pour MySQL. Vous pouvez utiliser une chaîne de connexion pour vous connecter à une base de données Azure Database pour MySQL depuis de nombreuses applications différentes.

## <a name="requirements"></a>Spécifications

Cet article utilise comme point de départ les ressources créées dans le guide suivant :

* [Démarrage rapide : Créer un serveur Azure Database pour MySQL avec PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Obtenir la chaîne de connexion

L’applet de commande `Get-AzMySqlConnectionString` est utilisée afin de générer une chaîne de connexion pour connecter des applications à Azure Database pour MySQL. L’exemple suivant retourne la chaîne de connexion pour un client PHP depuis **mydemoserver**.

```azurepowershell-interactive
Get-AzMySqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mysql.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
```

Les valeurs valides pour le paramètre `Client` sont :

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Personnaliser les paramètres de serveur Azure Database pour MySQL à l’aide de PowerShell](howto-configure-server-parameters-using-powershell.md)
