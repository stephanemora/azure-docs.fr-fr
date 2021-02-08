---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 872164dfee9f35881fca97b0339bd95c64a2f5ff
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070189"
---
Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Utilisez l’applet de commande Azure PowerShell [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) pour créer un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*. 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```
