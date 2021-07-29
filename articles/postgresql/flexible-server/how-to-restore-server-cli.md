---
title: Restaurer un serveur flexible Azure Database pour PostgreSQL avec l’interface Azure CLI
description: Cet article explique comment effectuer des opérations de restauration dans Azure Database pour PostgreSQL à l’aide de l’interface Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 05/29/2021
ms.openlocfilehash: f14bae80cad6fd9fc6ee534e8c8d1d9b6df617db
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025815"
---
# <a name="point-in-time-restore-of-a-azure-database-for-postgresql---flexible-server-with-azure-cli"></a>Restauration à un instant dans le passé d’un serveur flexible Azure Database pour PostgreSQL avec l’interface Azure CLI


> [!IMPORTANT]
> Azure Database pour PostgreSQL - Le serveur flexible est actuellement en préversion publique.

Cet article indique la procédure pas-à-pas à suivre pour effectuer des restaurations à un instant dans le passé sur un serveur flexible à l’aide de sauvegardes.

## <a name="prerequisites"></a>Prérequis
- Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Installez ou mettez à niveau Azure CLI vers la dernière version. Consultez [Installer Azure CLI](/cli/azure/install-azure-cli).
-  Connectez-vous au compte Azure à l’aide de la commande [az login](/cli/azure/reference-index#az_login). Notez la propriété **id**, qui fait référence à l’**ID d’abonnement** pour votre compte Azure.

    ```azurecli-interactive
    az login
    ````

- Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel vous souhaitez créer le serveur à l’aide de la commande ```az account set```.
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Créez un serveur flexible PostgreSQL si vous n’en avez pas encore créé à l’aide de la commande ```az postgres flexible-server create```.

    ```azurecli
    az postgres flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="restore-a-server-from-backup-to-a-new-server"></a>Restaurer un serveur à partir d’une sauvegarde sur un nouveau serveur

Vous pouvez exécuter la commande suivante pour restaurer un serveur vers la sauvegarde existante la plus ancienne.

**Utilisation**
```azurecli
az postgres flexible-server restore --restore-time
                                 --source-server
                                 [--ids]
                                 [--location]
                                 [--name]
                                 [--no-wait]
                                 [--resource-group]
                                 [--subscription]
```

**Exemple :** Restaurer un serveur à partir de cet instantané de sauvegarde ```2021-03-03T13:10:00Z```.

```azurecli
az postgres server restore \
--name mydemoserver-restored \
--resource-group myresourcegroup \
--restore-point-in-time "2021-05-05T13:10:00Z" \
--source-server mydemoserver
```

Le temps nécessaire à la restauration dépend de la taille des données stockées sur le serveur.

## <a name="perform-post-restore-tasks"></a>Effectuer des tâches de post-restauration
Une fois la restauration terminée, vous devez effectuer les tâches suivantes afin que les utilisateurs et les applications soient de nouveau opérationnels :

- Si le nouveau serveur est destiné à remplacer le serveur d’origine, redirigez les clients et les applications clientes vers le nouveau serveur
- Vérifiez que les règles de réseau virtuel appropriées sont en place pour permettre aux utilisateurs de se connecter. Ces règles ne sont pas copiées à partir du serveur d’origine.
- Assurez-vous que les connexions et les autorisations appropriées au niveau de la base de données sont en place
- Configurez les alertes comme il convient pour le serveur nouvellement restauré

## <a name="next-steps"></a>Étapes suivantes
* Découvrir la [continuité de l’activité](concepts-business-continuity.md)
* Découvrez [la sauvegarde et la récupération](concepts-backup-restore.md)  

