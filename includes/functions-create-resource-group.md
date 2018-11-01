---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: d44865dc3189a7f9dc05106baf9f4d120e5e8bf6
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133949"
---
## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure comme les Function Apps, les bases de données et les comptes de stockage sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé `myResourceGroup`.  
Si vous n’utilisez pas Cloud Shell, connectez-vous d’abord à l’aide de `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Vous créez généralement votre groupe de ressources et les ressources dans une région proche de chez vous. Pour afficher tous les emplacements pris en charge pour les plans App Service, exécutez la commande [az appservice list-locations](/cli/azure/appservice#az-appservice-list-locations).