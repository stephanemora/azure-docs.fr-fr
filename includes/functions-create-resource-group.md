---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0fba49ec5a610d03ece2dce7fa5bb0d9d094ab4c
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203154"
---
## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure comme les Function Apps, les bases de données et les comptes de stockage sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé `myResourceGroup`.  
Si vous n’utilisez pas Cloud Shell, connectez-vous d’abord à l’aide de `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Vous créez généralement votre groupe de ressources et les ressources dans une [région](https://azure.microsoft.com/global-infrastructure/regions/) proche de chez vous. 
