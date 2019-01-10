---
title: Configurer et accéder aux journaux de serveur pour PostgreSQL à l’aide de l’interface de ligne de commande Azure
description: Cet article décrit comment configurer les journaux du serveur dans Azure Database pour PostgreSQL à l’aide de la ligne de commande Azure CLI et comment y accéder.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 938aadaa1d17135808cbeb34adac5139bd61f833
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545020"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configurer et accéder aux journaux du serveur à l’aide d’Azure CLI
Vous pouvez télécharger les journaux des erreurs du serveur PostgreSQL à l’aide de l’interface de ligne de commande (Azure CLI). Toutefois, l’accès aux journaux des transactions n’est pas pris en charge. 

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- [Un serveur Azure Database pour PostgreSQL](quickstart-create-server-database-azure-cli.md)
- L’utilitaire en ligne de commande [Azure CLI](/cli/azure/install-azure-cli) ou Azure Cloud Shell dans le navigateur

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Configuration de la journalisation pour Azure Database pour PostgreSQL
Vous pouvez configurer le serveur afin d’accéder aux journaux de requêtes et aux journaux d’erreurs. Les journaux des erreurs peuvent contenir des informations sur le nettoyage automatique, la connexion et les points de contrôle.
1. Activez la journalisation.
2. Pour activer la journalisation des requêtes, mettez à jour **log\_statement** et **log\_min\_duration\_statement**.
3. Mettez à jour la période de rétention.

Pour plus d’informations, consultez [Personnalisation des paramètres de configuration du serveur](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Répertorier les journaux pour le serveur Azure Database pour PostgreSQL
Pour répertorier les fichiers journaux disponibles pour votre serveur, exécutez la commande [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list).

Vous pouvez répertorier les fichiers journaux pour le serveur **mydemoserver.postgres.database.azure.com** sous le groupe de ressources **myresourcegroup**. Puis, dirigez la liste des fichiers journaux vers un fichier texte appelé **log\_files\_list.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Téléchargement des journaux localement à partir du serveur
Avec la commande [az postgres server-logs download](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) vous pouvez télécharger des fichiers journaux individuels pour votre serveur. 

Utilisez l’exemple suivant pour télécharger le fichier journal spécifique pour le serveur **mydemoserver.postgres.database.azure.com** sous le groupe de ressources **myresourcegroup** dans votre environnement local.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Étapes suivantes
- Pour en savoir plus sur les journaux de serveur, consultez [Journaux de serveur dans Azure Database pour PostgreSQL](concepts-server-logs.md).
- Pour plus d’informations sur les paramètres du serveur, consultez [Personnalisation des paramètres de configuration de serveur à l’aide de l’interface de ligne de commande Azure](howto-configure-server-parameters-using-cli.md).
