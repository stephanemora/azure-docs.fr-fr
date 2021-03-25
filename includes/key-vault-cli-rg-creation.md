---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 97fd969ddae2f2c222209259cccd6f6f55272524
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99070256"
---
Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Utilisez la commande [az group create](/cli/azure/group#az_group_create) pour créer un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```
