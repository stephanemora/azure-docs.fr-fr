---
title: Générer une chaîne de connexion avec PowerShell - Azure Database pour MySQL
description: Ce guide de démarrage rapide fournit un exemple Azure PowerShell pour générer une chaîne de connexion pour la connexion à Azure Database pour MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: quickstart
ms.date: 8/3/2020
ms.openlocfilehash: 9de569e09e1b8bcf29f47077cbe15f0a6c40ddd1
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544916"
---
# <a name="azure-database-for-mysql-generate-a-connection-string-with-powershell"></a>Azure Database pour MySQL : Générer une chaîne de connexion avec PowerShell

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
> [Créer une base de données Azure Database pour MySQL à l’aide de PowerShell](tutorial-design-database-using-powershell.md)
