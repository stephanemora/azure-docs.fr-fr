---
title: Accéder aux journaux des requêtes lentes - Azure CLI - Azure Database for MariaDB
description: Cet article décrit comment accéder aux journaux des requêtes lentes dans Azure Database for MariaDB à l’aide de l’utilitaire de ligne de commande Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: f33a02ff0e287c135a7d63277cf3d8d3c0cd13d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527654"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Configurer et consulter les journaux des requêtes lentes à l’aide d’Azure CLI
Vous pouvez télécharger les journaux des requêtes lentes Azure Database for MariaDB à l’aide d’Azure CLI, l’utilitaire en ligne de commande Azure.

## <a name="prerequisites"></a>Conditions préalables requises
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- [Serveur Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) ou Azure Cloud Shell dans le navigateur

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Configuration de la journalisation pour Azure Database for MariaDB
Vous pouvez configurer le serveur afin d’accéder au journal des requêtes lentes MariaDB, comme suit :
1. Activez la journalisation en définissant le paramètre **journal\_des requêtes\_lentes** sur ON.
2. Réglez les autres paramètres tels que **durée\_longue\_d’une requête** et **instructions\_admin\_lentes\_du journal**.

Pour savoir comment définir la valeur de ces paramètres via Azure CLI, consultez [Comment configurer les paramètres du serveur](howto-configure-server-parameters-cli.md).

Par exemple, la commande CLI suivante active le journal des requêtes lentes, définit la durée de requête longue sur 10 secondes et désactive la journalisation de l’instruction admin lente. Enfin, elle répertorie les options de configuration à vérifier.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Répertorier les journaux d’activité de serveur pour Azure Database for MariaDB
Pour lister les fichiers journaux des requêtes lentes disponibles pour votre serveur, exécutez la commande [az mariadb server-logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list).

Vous pouvez répertorier les fichiers journaux pour le serveur **mydemoserver.mariadb.database.azure.com** sous le groupe de ressources **myresourcegroup**. Puis, dirigez la liste des fichiers journaux vers un fichier texte appelé **log\_files\_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Télécharger des journaux d’activité à partir du serveur
La commande [az mariadb server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) permet de télécharger des fichiers journaux individuels pour votre serveur.

Utilisez l’exemple suivant pour télécharger le fichier journal spécifique pour le serveur **mydemoserver.mariadb.database.azure.com** sous le groupe de ressources **myresourcegroup** dans votre environnement local.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Étapes suivantes
- Découvrez les [journaux des requêtes lentes dans Azure Database for MariaDB](concepts-server-logs.md).
