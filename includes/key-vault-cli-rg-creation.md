---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 97fd969ddae2f2c222209259cccd6f6f55272524
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99070256"
---
Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Utilisez la commande [az group create](/cli/azure/group#az_group_create) pour créer un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```
