---
title: Accéder aux journaux d’activité du serveur dans Azure Database pour MySQL à l’aide d’Azure CLI
description: Cet article décrit comment accéder aux journaux d’activité du serveur dans Azure Database pour MySQL à l’aide de l’utilitaire de ligne de commande Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 207e9965f6600477e1df93845bc41bd33b5c028c
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547029"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configurer et accéder aux journaux d’activité du serveur à l’aide d’Azure CLI
Vous pouvez télécharger les journaux d’activité du serveur Azure Database pour MySQL à l’aide d’Azure CLI, l’utilitaire de ligne de commande Azure.

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- [Serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) ou Azure Cloud Shell dans le navigateur

## <a name="configure-logging-for-azure-database-for-mysql"></a>Configuration de la journalisation pour Azure Database pour MySQL
Vous pouvez configurer le serveur afin d’accéder au journal des requêtes lentes MySQL, comme suit :
1. Activez la journalisation en définissant le paramètre **journal\_des requêtes\_lentes** sur ON.
2. Réglez les autres paramètres tels que **durée\_longue\_d’une requête**  et  **instructions\_admin\_lentes\_du journal**.

Pour savoir comment définir la valeur de ces paramètres via Azure CLI, consultez [Comment configurer les paramètres du serveur](howto-configure-server-parameters-using-cli.md). 

Par exemple, la commande CLI suivante active le journal des requêtes lentes, définit la durée de requête longue sur 10 secondes et désactive la journalisation de l’instruction admin lente. Enfin, elle répertorie les options de configuration à vérifier.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Répertorier les journaux d’activité pour le serveur Azure Database pour MySQL
Pour répertorier les fichiers journaux disponibles pour votre serveur, exécutez la commande [az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list).

Vous pouvez répertorier les fichiers journaux pour le serveur **mydemoserver.mysql.database.azure.com** sous le groupe de ressources **myresourcegroup**. Puis, dirigez la liste des fichiers journaux vers un fichier texte appelé **log\_files\_list.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Télécharger des journaux d’activité à partir du serveur
Avec la commande [az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) vous pouvez télécharger des fichiers journaux individuels pour votre serveur. 

Utilisez l’exemple suivant pour télécharger le fichier journal spécifique pour le serveur **mydemoserver.mysql.database.azure.com** sous le groupe de ressources **myresourcegroup** dans votre environnement local.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [journaux d’activité du serveur dans Azure Database pour MySQL](concepts-server-logs.md).
