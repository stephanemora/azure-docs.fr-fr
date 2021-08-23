---
title: Restaurer un serveur flexible Azure Database pour MySQL avec l’interface Azure CLI
description: Cet article explique comment effectuer des opérations de restauration dans Azure Database pour MySQL à l’aide de l’interface Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: eff4268991924ee089a9bba67842fcaf30cf2510
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122641105"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-with-azure-cli"></a>Restauration à un instant dans le passé d’un serveur flexible Azure Database pour MySQL avec l’interface Azure CLI

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database pour MySQL - Serveur flexible est actuellement en préversion publique.

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

- Créez un serveur flexible MySQL si vous n’en avez pas encore créé à l’aide de la commande ```az mysql flexible-server create```.

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="restore-a-server-from-backup-to-a-new-server"></a>Restaurer un serveur à partir d’une sauvegarde sur un nouveau serveur

Vous pouvez exécuter la commande suivante pour restaurer un serveur vers la sauvegarde existante la plus ancienne.

**Utilisation**
```azurecli
az mysql flexible-server restore --restore-time
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
az mysql server restore \
--name mydemoserver-restored \
--resource-group myresourcegroup \
--restore-point-in-time "2021-03-03T13:10:00Z" \
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
En savoir plus sur la [continuité de l’activité](concepts-business-continuity.md)

