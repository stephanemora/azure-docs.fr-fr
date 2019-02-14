---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: c20f86fe7fdcfc7ecc940923a8c98fa1fbf4cf65
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246874"
---
## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure comme les Function Apps, les bases de données et les comptes de stockage sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé `myResourceGroup`.  
Si vous n’utilisez pas Cloud Shell, connectez-vous d’abord à l’aide de `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Vous créez généralement votre groupe de ressources et les ressources dans une région proche de chez vous. Pour afficher tous les emplacements pris en charge pour les plans App Service, exécutez la commande [az appservice list-locations](/cli/azure/appservice#az-appservice-list-locations).
