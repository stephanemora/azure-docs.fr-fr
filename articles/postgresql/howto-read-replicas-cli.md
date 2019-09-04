---
title: Gérer des réplicas en lecture pour Azure Database pour PostgreSQL (serveur unique) à partir de l’interface Azure CLI
description: Découvrez comment gérer des réplicas en lecture pour Azure Database pour PostgreSQL (serveur unique) à partir de l’interface Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 63a8acad3c393a4c4d9c6a3b6750f1f934dad43d
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907441"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Créer et gérer des réplicas en lecture à partir de l’interface Azure CLI

Dans cet article, vous allez apprendre à créer et à gérer des réplicas en lecture dans Azure Database pour PostgreSQL à partir de l’interface Azure CLI. Pour en savoir plus sur les réplicas en lecture, consultez [vue d’ensemble](concepts-read-replicas.md).

> [!IMPORTANT]
> Vous pouvez créer un réplica en lecture dans la même région que votre serveur maître ou dans n’importe quelle autre région Azure de votre choix. La réplication entre plusieurs régions est actuellement disponible en préversion publique.

## <a name="prerequisites"></a>Prérequis
- Un [serveur Azure Database pour PostgreSQL](quickstart-create-server-up-azure-cli.md) qui représente le serveur maître.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Pour afficher la version installée, exécutez la commande `az --version`. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli). 


## <a name="prepare-the-master-server"></a>Préparer le serveur maître
Ces étapes permettent de préparer un serveur maître pour les niveaux à usage général ou à mémoire optimisée.

Le paramètre `azure.replication_support` doit avoir la valeur **REPLICA** sur le serveur maître. Quand vous changez ce paramètre statique, un redémarrage du serveur est nécessaire pour que le changement soit pris en compte.

1. Définissez `azure.replication_support` sur REPLICA.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Redémarrez pour appliquer le changement au serveur.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>Créer un réplica en lecture

La commande [az postgres server replica create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) exige les paramètres suivants :

| Paramètre | Exemple de valeur | Description  |
| --- | --- | --- |
| resource-group | myResourceGroup |  Groupe de ressources dans lequel le serveur réplica sera créé.  |
| Nom | mydemoserver-replica | Nom du nouveau serveur réplica créé. |
| source-server | mydemoserver | Nom ou ID de ressource du serveur maître existant à partir duquel effectuer la réplication. |

Dans l’exemple CLI ci-dessous, le réplica est créé dans la même région que le maître.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Pour créer un réplica en lecture entre régions, utilisez le paramètre `--location`. L’exemple CLI ci-dessous crée le réplica dans la région USA Ouest.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Pour en savoir plus sur les régions dans lesquelles vous pouvez créer un réplica, consultez l’article [Concepts relatifs aux réplicas en lecture](concepts-read-replicas.md). 

Si vous n’avez pas affecté au paramètre `azure.replication_support` la valeur **REPLICA** sur un serveur maître à usage général ou à mémoire optimisée, et que vous avez redémarré le serveur, vous recevez une erreur. Effectuez ces deux étapes avant de créer un réplica.

Le réplica doit être créé en utilisant les mêmes paramètres de calcul et de stockage que le serveur maître. Une fois le réplica créé, vous pouvez changer plusieurs paramètres indépendamment du serveur maître : génération de calcul, vCores, stockage et période de conservation de la sauvegarde. Le niveau tarifaire peut également être changé indépendamment, sauf vers ou depuis le niveau De base.

> [!IMPORTANT]
> Avant de modifier un paramètre du serveur maître, remplacez la valeur du paramètre du réplica par une valeur supérieure ou égale à celle du serveur maître. Vous garantissez ainsi l’alignement du réplica sur les changements apportés au serveur maître.

## <a name="list-replicas"></a>Lister les réplicas
Vous pouvez afficher la liste des réplicas d’un serveur maître en utilisant la commande [az postgres server replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list).

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>Arrêter la réplication vers un serveur réplica
Vous pouvez arrêter la réplication entre un serveur maître et un réplica en lecture à l’aide de la commande [az postgres server replica stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop).

Une fois que vous avez arrêté la réplication entre un serveur maître et un réplica en lecture, vous ne pouvez plus l’annuler. Le réplica en lecture devient un serveur autonome qui prend en charge les lectures et les écritures. Le serveur autonome ne peut pas être retransformé en réplica.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>Supprimer un serveur maître ou réplica
Pour supprimer un serveur maître ou réplica, vous utilisez la commande [az postgres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete).

Quand vous supprimez un serveur maître, la réplication est arrêtée sur tous les réplicas en lecture. Les réplicas en lecture deviennent des serveurs autonomes qui prennent désormais en charge les lectures et les écritures.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Étapes suivantes
* Découvrez-en plus sur les [réplicas en lecture dans Azure Database pour PostgreSQL](concepts-read-replicas.md).
* Découvrez comment [créer et gérer des réplicas en lecture dans le portail Azure](howto-read-replicas-portal.md).