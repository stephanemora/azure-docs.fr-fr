---
title: Gérer des réplicas en lecture - Azure CLI, API REST - Azure Database pour PostgreSQL - Serveur unique
description: Découvrez comment gérer des réplicas en lecture dans Azure Database pour PostgreSQL - Serveur unique à partir d’Azure CLI ou de l’API REST.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: b10ac3b4bc9dacd723b8b1265911df721b781189
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774803"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Créer et gérer des réplicas en lecture à partir d’Azure CLI ou de l’API REST

Dans cet article, vous allez apprendre à créer et à gérer des réplicas en lecture dans Azure Database pour PostgreSQL à partir d’Azure CLI et de l’API REST. Pour en savoir plus sur les réplicas en lecture, consultez [vue d’ensemble](concepts-read-replicas.md).

## <a name="azure-cli"></a>Azure CLI
Vous pouvez créer et gérer des réplicas en lecture à l’aide d’Azure CLI.

### <a name="prerequisites"></a>Conditions préalables requises

- [Installation d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Un [serveur Azure Database pour PostgreSQL](quickstart-create-server-up-azure-cli.md) qui représente le serveur maître.


### <a name="prepare-the-master-server"></a>Préparer le serveur maître
Ces étapes permettent de préparer un serveur maître pour les niveaux à usage général ou à mémoire optimisée.

Le paramètre `azure.replication_support` doit avoir la valeur **REPLICA** sur le serveur maître. Quand vous changez ce paramètre statique, un redémarrage du serveur est nécessaire pour que le changement soit pris en compte.

1. Définissez `azure.replication_support` sur REPLICA.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

> [!NOTE]
> Si vous recevez l’erreur « Valeur non valide donnée » lors de la tentative de définition du paramètre azure.replication_support à partir d’Azure CLI, il est probable que REPLICA est déjà défini par défaut sur votre serveur. Un bogue empêche ce paramètre d’être correctement reflété sur les serveurs plus récents où REPLICA est la valeur interne par défaut. <br><br>
> Vous pouvez ignorer les étapes maîtresses de préparation et passer à la création du réplica. <br><br>
> Si vous souhaitez confirmer que votre serveur se trouve dans cette catégorie, accédez à la page de réplication du serveur dans le Portail Azure. L’option « Désactiver la réplication » sera grisée et celle « Ajouter un réplica » sera active dans la barre d’outils.

2. Redémarrez le serveur pour appliquer les modifications.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Créer un réplica en lecture

La commande [az postgres server replica create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) exige les paramètres suivants :

| Paramètre | Valeur d'exemple | Description  |
| --- | --- | --- |
| resource-group | myResourceGroup |  Groupe de ressources dans lequel le serveur réplica sera créé.  |
| name | mydemoserver-replica | Nom du nouveau serveur réplica créé. |
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

### <a name="list-replicas"></a>Lister les réplicas
Vous pouvez afficher la liste des réplicas d’un serveur maître en utilisant la commande [az postgres server replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list).

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Arrêter la réplication vers un serveur réplica
Vous pouvez arrêter la réplication entre un serveur maître et un réplica en lecture à l’aide de la commande [az postgres server replica stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop).

Une fois que vous avez arrêté la réplication entre un serveur maître et un réplica en lecture, vous ne pouvez plus l’annuler. Le réplica en lecture devient un serveur autonome qui prend en charge les lectures et les écritures. Le serveur autonome ne peut pas être retransformé en réplica.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>Supprimer un serveur maître ou réplica
Pour supprimer un serveur maître ou réplica, vous utilisez la commande [az postgres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete).

Quand vous supprimez un serveur maître, la réplication est arrêtée sur tous les réplicas en lecture. Les réplicas en lecture deviennent des serveurs autonomes qui prennent désormais en charge les lectures et les écritures.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>API REST
Vous pouvez créer et gérer des réplicas en lecture à l’aide de l’[API REST Azure](/rest/api/azure/).

### <a name="prepare-the-master-server"></a>Préparer le serveur maître
Ces étapes permettent de préparer un serveur maître pour les niveaux à usage général ou à mémoire optimisée.

Le paramètre `azure.replication_support` doit avoir la valeur **REPLICA** sur le serveur maître. Quand vous changez ce paramètre statique, un redémarrage du serveur est nécessaire pour que le changement soit pris en compte.

1. Définissez `azure.replication_support` sur REPLICA.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [Redémarrez le serveur](/rest/api/postgresql/servers/restart) pour appliquer les modifications.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Créer un réplica en lecture
Vous pouvez créer un réplica en lecture à l’aide de l’[API de création](/rest/api/postgresql/servers/create) :

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Pour en savoir plus sur les régions dans lesquelles vous pouvez créer un réplica, consultez l’article [Concepts relatifs aux réplicas en lecture](concepts-read-replicas.md). 

Si vous n’avez pas affecté au paramètre `azure.replication_support` la valeur **REPLICA** sur un serveur maître à usage général ou à mémoire optimisée, et que vous avez redémarré le serveur, vous recevez une erreur. Effectuez ces deux étapes avant de créer un réplica.

Le réplica doit être créé en utilisant les mêmes paramètres de calcul et de stockage que le serveur maître. Une fois le réplica créé, vous pouvez changer plusieurs paramètres indépendamment du serveur maître : génération de calcul, vCores, stockage et période de conservation de la sauvegarde. Le niveau tarifaire peut également être changé indépendamment, sauf vers ou depuis le niveau De base.


> [!IMPORTANT]
> Avant de modifier un paramètre du serveur maître, remplacez la valeur du paramètre du réplica par une valeur supérieure ou égale à celle du serveur maître. Vous garantissez ainsi l’alignement du réplica sur les changements apportés au serveur maître.

### <a name="list-replicas"></a>Lister les réplicas
Vous pouvez afficher la liste des réplicas d’un serveur maître à l’aide de l’[API de liste de réplicas](/rest/api/postgresql/replicas/listbyserver) :

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Arrêter la réplication vers un serveur réplica
Vous pouvez arrêter la réplication entre un serveur maître et un réplica en lecture à l’aide de l’[API de mise à jour](/rest/api/postgresql/servers/update).

Une fois que vous avez arrêté la réplication entre un serveur maître et un réplica en lecture, vous ne pouvez plus l’annuler. Le réplica en lecture devient un serveur autonome qui prend en charge les lectures et les écritures. Le serveur autonome ne peut pas être retransformé en réplica.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Supprimer un serveur maître ou réplica
Pour supprimer un serveur maître ou réplica, vous devez utiliser l’[API de suppression](/rest/api/postgresql/servers/delete) :

Quand vous supprimez un serveur maître, la réplication est arrêtée sur tous les réplicas en lecture. Les réplicas en lecture deviennent des serveurs autonomes qui prennent désormais en charge les lectures et les écritures.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Étapes suivantes
* Découvrez-en plus sur les [réplicas en lecture dans Azure Database pour PostgreSQL](concepts-read-replicas.md).
* Découvrez comment [créer et gérer des réplicas en lecture dans le portail Azure](howto-read-replicas-portal.md).
