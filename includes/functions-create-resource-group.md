---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: a2994602f857c2c8ff9f935b649a8d3e94c10dca
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444111"
---
## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure comme les Function Apps, les bases de données et les comptes de stockage sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé `myResourceGroup`.  
Si vous n’utilisez pas Cloud Shell, connectez-vous d’abord à l’aide de `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Vous créez généralement votre groupe de ressources et les ressources dans une [région](https://azure.microsoft.com/global-infrastructure/regions/) proche de chez vous. 
