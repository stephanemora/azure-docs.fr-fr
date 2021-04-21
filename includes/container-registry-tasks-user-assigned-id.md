---
title: Fichier include
description: Fichier include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 70bd79ef944e5918d750a130bd2e2b2c2b656bf4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781118"
---
### <a name="create-a-user-assigned-identity"></a>Créer une identité attribuée par l’utilisateur

Créez une identité nommée *myACRTasksId* dans votre abonnement à l’aide de la commande [az identity create][az-identity-create]. Vous pouvez utiliser le groupe de ressources qui vous a servi à créer un registre de conteneurs ou un autre groupe.

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

Pour configurer l’identité affectée par l’utilisateur aux étapes suivantes, utilisez la commande [az identity show][az-identity-show] pour stocker l’ID du principal, l’ID de client et l’ID de ressource de l’identité dans des variables.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
