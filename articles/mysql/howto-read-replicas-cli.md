---
title: Créer et gérer des réplicas en lecture dans Azure Database pour MySQL
description: Cet article explique comment configurer et gérer des réplicas en lecture dans Azure Database pour MySQL à l’aide de l'interface de ligne de commande Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: ba8af55f7467e361136e4b0c57c97b4fa187cec0
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304958"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli"></a>Créer et gérer des réplicas en lecture dans Azure Database pour MySQL à l’aide de l'interface de ligne de commande Azure

Dans cet article, vous allez apprendre à créer et à gérer des réplicas en lecture dans la même région Azure comme serveur maître dans le service Azure Database pour MySQL à l’aide de l'interface de ligne de commande Azure.

> [!IMPORTANT]
> Vous pouvez créer un réplica en lecture dans la même région que votre serveur principal, ou dans n’importe quelle autre région Azure de votre choix. La réplication entre les régions est actuellement en version préliminaire publique.

## <a name="prerequisites"></a>Conditions préalables

- [Installation d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Un [serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) qui sera utilisé comme serveur maître. 

> [!IMPORTANT]
> La fonctionnalité de réplica en lecture est disponible uniquement pour les serveurs Azure Database pour MySQL dans les niveaux tarifaires Usage général ou Mémoire optimisée. Vérifiez que le serveur maître se trouve dans l’un de ces niveaux tarifaires.

## <a name="create-a-read-replica"></a>Créer un réplica en lecture

Un serveur réplica en lecture peut être créé en utilisant la commande suivante :

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

La commande `az mysql server replica create` requiert les paramètres suivants :

| Paramètre | Exemple de valeur | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Groupe de ressources dans lequel le serveur réplica sera créé.  |
| name | mydemoreplicaserver | Nom du nouveau serveur réplica créé. |
| source-server | mydemoserver | Nom ou ID du serveur maître à partir duquel le serveur réplica sera créé. |

Pour créer une croix région lire le réplica, utilisez le `--location` paramètre. L’exemple CLI suivant crée le réplica dans l’ouest des États-Unis.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Les réplicas en lecture sont créés avec la même configuration de serveur que le serveur maître. La configuration du serveur réplica peut être modifiée après la création de ce dernier. Il est recommandé que la configuration du serveur réplica soit maintenue à des valeurs égales ou supérieures à celles du serveur maître pour garantir que le serveur réplica est à la hauteur du serveur maître.

## <a name="stop-replication-to-a-replica-server"></a>Arrêter la réplication vers un serveur réplica

> [!IMPORTANT]
> L’arrêt de la réplication vers un serveur est irréversible. Une fois la réplication entre un serveur maître et un serveur réplica arrêtée, elle ne peut pas être annulée. Le serveur réplica devient un serveur autonome et prend désormais en charge la lecture et les écritures. Ce serveur ne peut pas être à nouveau transformé en réplica.

La réplication d'un serveur réplica en lecture peut être arrêtée en utilisant la commande suivante :

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

La commande `az mysql server replica stop` requiert les paramètres suivants :

| Paramètre | Exemple de valeur | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Groupe de ressources où se trouve le serveur réplica.  |
| name | mydemoreplicaserver | Nom du serveur réplica pour lequel arrêter la réplication. |

## <a name="delete-a-replica-server"></a>Supprimer un serveur réplica

La suppression d’un serveur réplica en lecture peut être effectuée en exécutant la commande **[az mysql server delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

## <a name="delete-a-master-server"></a>Supprimer un serveur maître

> [!IMPORTANT]
> La suppression d’un serveur maître arrête la réplication vers tous les serveurs réplicas et supprime le serveur maître lui-même. Les serveurs réplicas deviennent des serveurs autonomes qui prennent désormais en charge la lecture et les écritures.

Pour supprimer un serveur maître, vous pouvez exécuter la commande **[az mysql server delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="list-replicas-for-a-master-server"></a>Répertorier les réplicas d'un serveur maître

Pour afficher tous les réplicas d'un serveur maître donné, exécutez la commande suivante : 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

La commande `az mysql server replica list` requiert les paramètres suivants :

| Paramètre | Exemple de valeur | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Groupe de ressources dans lequel le serveur réplica sera créé.  |
| server-name | mydemoserver | Nom ou ID du serveur maître. |

## <a name="next-steps"></a>Étapes suivantes

- Découvrir plus en détail les [réplicas en lecture](concepts-read-replicas.md)
