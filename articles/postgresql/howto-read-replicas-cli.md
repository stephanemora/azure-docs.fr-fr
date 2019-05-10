---
title: Gérer des réplicas en lecture pour Azure Database pour PostgreSQL - seul serveur à partir de l’interface CLI Azure
description: Découvrez comment gérer des réplicas en lecture dans la base de données Azure pour PostgreSQL - seul serveur à partir de l’interface CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 9730faf3191ef2e2bd0b6c3caddefa0492b33fc5
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510248"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Créer et gérer des réplicas en lecture à partir de l’interface CLI Azure

Dans cet article, vous allez apprendre à créer et gérer des réplicas en lecture dans Azure Database pour PostgreSQL à partir de l’interface CLI. Pour en savoir plus sur les réplicas en lecture, consultez [vue d’ensemble](concepts-read-replicas.md).

> [!NOTE]
> Azure CLI ne prend pas en charge Création de réplicas dans une autre région à partir du serveur maître. Pour créer un réplica entre régions, utilisez le [Azure portal](howto-read-replicas-portal.md).

## <a name="prerequisites"></a>Conditions préalables
- Un [serveur Azure Database pour PostgreSQL](quickstart-create-server-up-azure-cli.md) qui représente le serveur maître.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Pour afficher la version installée, exécutez la commande `az --version`. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli). 


## <a name="prepare-the-master-server"></a>Préparer le serveur maître
Ces étapes permettent de préparer un serveur maître pour les niveaux à usage général ou à mémoire optimisée.

Le paramètre `azure.replication_support` doit avoir la valeur **REPLICA** sur le serveur maître. Lorsque ce paramètre statique est modifié, un redémarrage du serveur est requis pour que la modification prenne effet.

1. Définissez `azure.replication_support` au RÉPLICA.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Redémarrer pour appliquer la modification sur le serveur.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>Créer un réplica en lecture

Le [az postgres server réplica créer](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) commande requiert les paramètres suivants :

| Paramètre | Exemple de valeur | Description   |
| --- | --- | --- |
| resource-group | myResourceGroup |  Le groupe de ressources où sera créé le serveur de réplication.  |
| name | mydemoserver-réplica | Nom du nouveau serveur réplica créé. |
| source-server | mydemoserver | Le nom ou ID ressource du serveur maître pour répliquer à partir de. |

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Si vous n’avez pas le `azure.replication_support` paramètre **RÉPLICA** sur un usage général ou mémoire optimisé serveur maître et redémarré le serveur, vous recevez une erreur. Effectuez ces deux étapes avant de créer un réplica.

Un réplica est créé à partir de la même configuration que celle du serveur maître. Une fois le réplica créé, vous pouvez changer plusieurs paramètres indépendamment du serveur maître : génération de calcul, vCores, stockage et période de conservation de la sauvegarde. Le niveau tarifaire peut également être changé indépendamment, sauf vers ou depuis le niveau De base.

> [!IMPORTANT]
> Avant de mettre à jour une configuration de serveur maître avec de nouvelles valeurs, mettez à jour la configuration du réplica avec des valeurs égales ou supérieures. Ainsi, vous avez la garantie que le réplica peut suivre les changements apportés au maître.

## <a name="list-replicas"></a>Liste des réplicas
Vous pouvez afficher la liste des réplicas d’un serveur maître à l’aide de [liste des réplicas az postgres server](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) commande.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>Arrêter la réplication vers un serveur réplica
Vous pouvez arrêter la réplication entre un serveur maître et un réplica en lecture à l’aide de [stop de réplica az postgres server](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) commande.

Une fois que vous avez arrêté la réplication entre un serveur maître et un réplica en lecture, vous ne pouvez plus l’annuler. Le réplica en lecture devient un serveur autonome qui prend en charge les lectures et les écritures. Le serveur autonome ne peut pas être retransformé en réplica.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>Supprimer un serveur principal ou réplica
Pour supprimer un serveur principal ou réplica, vous utilisez le [delete de az postgres server](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) commande.

Quand vous supprimez un serveur maître, la réplication est arrêtée sur tous les réplicas en lecture. Les réplicas en lecture deviennent des serveurs autonomes qui prennent désormais en charge les lectures et les écritures.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Étapes suivantes
Découvrez-en plus sur les [réplicas en lecture dans Azure Database pour PostgreSQL](concepts-read-replicas.md).