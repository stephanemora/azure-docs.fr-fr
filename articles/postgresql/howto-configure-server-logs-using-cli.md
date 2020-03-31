---
title: Gérer les journaux - Azure CLI - Azure Database pour PostgreSQL - Serveur unique
description: Cet article décrit comment configurer et accéder aux journaux du serveur (fichiers .log) dans Azure Database pour PostgreSQL - Serveur unique à l’aide d’Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: be679be91d49516bd2f6c672eb53640cfad2ae2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763570"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configurer et accéder aux journaux d’activité du serveur à l’aide d’Azure CLI
Vous pouvez télécharger les journaux d’activité des erreurs du serveur PostgreSQL à l’aide de l’interface de ligne de commande (Azure CLI). Toutefois, l’accès aux journaux d’activité des transactions n’est pas pris en charge. 

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- [Un serveur Azure Database pour PostgreSQL](quickstart-create-server-database-azure-cli.md)
- L’utilitaire en ligne de commande [Azure CLI](/cli/azure/install-azure-cli) ou Azure Cloud Shell dans le navigateur

## <a name="configure-logging"></a>Configuration de la journalisation
Vous pouvez configurer le serveur afin d’accéder aux journaux d’activité de requêtes et aux journaux d’activité d’erreurs. Les journaux d’activité des erreurs peuvent contenir des informations sur le nettoyage automatique, la connexion et les points de contrôle.
1. Activez la journalisation.
2. Pour activer la journalisation des requêtes, mettez à jour **l’instruction\_du journal** et **l’instruction\_durée\_minimale\_du journal**.
3. Mettez à jour la période de rétention.

Pour plus d’informations, consultez [Personnalisation des paramètres de configuration du serveur](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Répertorier les journaux
Pour répertorier les fichiers journaux disponibles pour votre serveur, exécutez la commande [az postgres server-logs list](/cli/azure/postgres/server-logs).

Vous pouvez répertorier les fichiers journaux pour le serveur **mydemoserver.postgres.database.azure.com** sous le groupe de ressources **myresourcegroup**. Puis, dirigez la liste des fichiers journaux vers un fichier texte appelé **log\_files\_list.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Téléchargement des journaux d’activité localement à partir du serveur
Avec la commande [az postgres server-logs download](/cli/azure/postgres/server-logs) vous pouvez télécharger des fichiers journaux individuels pour votre serveur. 

Utilisez l’exemple suivant pour télécharger le fichier journal spécifique pour le serveur **mydemoserver.postgres.database.azure.com** sous le groupe de ressources **myresourcegroup** dans votre environnement local.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Étapes suivantes
- Pour en savoir plus sur les journaux d’activité de serveur, consultez [Journaux d’activité de serveur dans Azure Database pour PostgreSQL](concepts-server-logs.md).
- Pour plus d’informations sur les paramètres du serveur, consultez [Personnalisation des paramètres de configuration de serveur à l’aide de l’interface de ligne de commande Azure](howto-configure-server-parameters-using-cli.md).
