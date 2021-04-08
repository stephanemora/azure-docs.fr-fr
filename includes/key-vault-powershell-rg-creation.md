---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 872164dfee9f35881fca97b0339bd95c64a2f5ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99070189"
---
Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Utilisez l’applet de commande Azure PowerShell [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) pour créer un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*. 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```
