---
title: Fichier Include
description: Fichier Include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0e2acb346fad87e0c1c7fd7de1389d8fc86206d0
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68641534"
---
### <a name="create-a-user-assigned-identity"></a>Créer une identité attribuée par l’utilisateur

Créez une identité nommée *myACRTasksId* dans votre abonnement à l’aide de la commande [az identity create][az-identity-create]. Vous pouvez utiliser le groupe de ressources qui vous a servi à créer un registre de conteneurs ou un autre groupe.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Pour configurer l’identité affectée par l’utilisateur aux étapes suivantes, utilisez la commande [az identity show][az-identity-show] pour stocker l’ID de principal de service, l’ID client et l’ID de ressource de l’identité dans des variables.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show