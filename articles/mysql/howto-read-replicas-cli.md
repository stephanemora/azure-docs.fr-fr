---
title: Gérer des réplicas en lecture - AZURE CLI, API REST - Azure Database pour MySQL
description: Découvrez comment configurer et gérer des réplicas en lecture dans Azure Database pour MySQL à partir d’Azure CLI ou de l’API REST.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 41c12dcb2e4c5e3b280bb349e81bcb58a0bafd01
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495759"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli-and-rest-api"></a>Comment créer et gérer des réplicas en lecture dans Azure Database pour MySQL à l’aide d’Azure CLI et de l’API REST

Dans cet article, vous allez apprendre à créer et à gérer des réplicas en lecture dans le service Azure Database pour MySQL à l’aide d’Azure CLI et de l’API REST. Pour en savoir plus sur les réplicas en lecture, consultez [vue d’ensemble](concepts-read-replicas.md).

## <a name="azure-cli"></a>Azure CLI
Vous pouvez créer et gérer des réplicas en lecture à l’aide d’Azure CLI.

### <a name="prerequisites"></a>Prérequis

- [Installation d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Un [serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) qui sera utilisé comme serveur maître. 

> [!IMPORTANT]
> La fonctionnalité de réplica en lecture est disponible uniquement pour les serveurs Azure Database pour MySQL dans les niveaux tarifaires Usage général ou Mémoire optimisée. Vérifiez que le serveur maître se trouve dans l’un de ces niveaux tarifaires.

### <a name="create-a-read-replica"></a>Créer un réplica en lecture

> [!IMPORTANT]
> Lorsque vous créez un réplica pour un serveur maître qui ne dispose d’aucun réplica existant, le serveur maître commence par redémarrer pour se préparer pour la réplication. Tenez-en compte et effectuez ces opérations en période creuse.

Un serveur réplica en lecture peut être créé en utilisant la commande suivante :

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

La commande `az mysql server replica create` requiert les paramètres suivants :

| Paramètre | Valeur d'exemple | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Groupe de ressources dans lequel le serveur réplica sera créé.  |
| name | mydemoreplicaserver | Nom du nouveau serveur réplica créé. |
| source-server | mydemoserver | Nom ou ID du serveur maître à partir duquel le serveur réplica sera créé. |

Pour créer un réplica en lecture entre régions, utilisez le paramètre `--location`. L’exemple CLI ci-dessous crée le réplica dans la région USA Ouest.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Pour en savoir plus sur les régions dans lesquelles vous pouvez créer un réplica, consultez l’article [Concepts relatifs aux réplicas en lecture](concepts-read-replicas.md). 

> [!NOTE]
> Les réplicas en lecture sont créés avec la même configuration de serveur que le serveur maître. La configuration du serveur réplica peut être modifiée après la création de ce dernier. Il est recommandé que la configuration du serveur réplica soit maintenue à des valeurs égales ou supérieures à celles du serveur maître pour garantir que le serveur réplica est à la hauteur du serveur maître.


### <a name="list-replicas-for-a-master-server"></a>Répertorier les réplicas d'un serveur maître

Pour afficher tous les réplicas d'un serveur maître donné, exécutez la commande suivante : 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

La commande `az mysql server replica list` requiert les paramètres suivants :

| Paramètre | Valeur d'exemple | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Groupe de ressources dans lequel le serveur réplica sera créé.  |
| server-name | mydemoserver | Nom ou ID du serveur maître. |

### <a name="stop-replication-to-a-replica-server"></a>Arrêter la réplication vers un serveur réplica

> [!IMPORTANT]
> L’arrêt de la réplication vers un serveur est irréversible. Une fois la réplication entre un serveur maître et un serveur réplica arrêtée, elle ne peut pas être annulée. Le serveur réplica devient un serveur autonome et prend désormais en charge la lecture et les écritures. Ce serveur ne peut pas être à nouveau transformé en réplica.

La réplication d'un serveur réplica en lecture peut être arrêtée en utilisant la commande suivante :

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

La commande `az mysql server replica stop` requiert les paramètres suivants :

| Paramètre | Valeur d'exemple | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Groupe de ressources où se trouve le serveur réplica.  |
| name | mydemoreplicaserver | Nom du serveur réplica pour lequel arrêter la réplication. |

### <a name="delete-a-replica-server"></a>Supprimer un serveur réplica

La suppression d’un serveur réplica en lecture peut être effectuée en exécutant la commande **[az mysql server delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Supprimer un serveur maître

> [!IMPORTANT]
> La suppression d’un serveur maître arrête la réplication vers tous les serveurs réplicas et supprime le serveur maître lui-même. Les serveurs réplicas deviennent des serveurs autonomes qui prennent désormais en charge la lecture et les écritures.

Pour supprimer un serveur maître, vous pouvez exécuter la commande **[az mysql server delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```


## <a name="rest-api"></a>API REST
Vous pouvez créer et gérer des réplicas en lecture à l’aide de l’[API REST Azure](/rest/api/azure/).

### <a name="create-a-read-replica"></a>Créer un réplica en lecture
Vous pouvez créer un réplica en lecture à l’aide de l’[API de création](/rest/api/mysql/servers/create) :

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}"
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
Vous pouvez afficher la liste des réplicas d’un serveur maître à l’aide de l’[API de liste de réplicas](/rest/api/mysql/replicas/listbyserver) :

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Arrêter la réplication vers un serveur réplica
Vous pouvez arrêter la réplication entre un serveur maître et un réplica en lecture à l’aide de l’[API de mise à jour](/rest/api/mysql/servers/update).

Une fois que vous avez arrêté la réplication entre un serveur maître et un réplica en lecture, vous ne pouvez plus l’annuler. Le réplica en lecture devient un serveur autonome qui prend en charge les lectures et les écritures. Le serveur autonome ne peut pas être retransformé en réplica.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Supprimer un serveur maître ou réplica
Pour supprimer un serveur maître ou réplica, vous devez utiliser l’[API de suppression](/rest/api/mysql/servers/delete) :

Quand vous supprimez un serveur maître, la réplication est arrêtée sur tous les réplicas en lecture. Les réplicas en lecture deviennent des serveurs autonomes qui prennent désormais en charge les lectures et les écritures.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Étapes suivantes

- Découvrir plus en détail les [réplicas en lecture](concepts-read-replicas.md)
