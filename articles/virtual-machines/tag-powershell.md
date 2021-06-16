---
title: Comment baliser une machine virtuelle à l’aide de PowerShell
description: En savoir plus sur le balisage d’une machine virtuelle à l’aide de PowerShell
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ed1201bf90a7178d59a6f7e287dd144c14790838
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110668073"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>Comment baliser une machine virtuelle dans Azure à l’aide de PowerShell

Cet article explique comment baliser une machine virtuelle dans Azure à l’aide de PowerShell. Les balises sont des paires clé/valeur définies par l’utilisateur, qui peuvent être placées directement sur une ressource ou sur un groupe de ressources. Azure prend actuellement en charge jusqu’à 50 étiquettes par ressource et par groupe de ressources. Les balises peuvent être placées sur une ressource au moment de la création ou bien ajoutées à une ressource existante. Si vous souhaitez baliser une machine virtuelle à l’aide d’Azure CLI, consultez [Comment baliser une machine virtuelle dans Azure à l’aide d’Azure CLI](tag-cli.md).

Utilisez l’applet de commande `Get-AzVM` pour afficher la liste actuelle des balises de votre machine virtuelle.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

Si votre machine virtuelle contient déjà des balises, vous verrez toutes les balises sous forme de liste.

Pour ajouter des balises, utilisez la commande `Set-AzResource`. Lors de la mise à jour des balises via PowerShell, les balises sont mises à jour comme un tout. Si vous ajoutez une balise à une ressource qui a déjà des balises, vous devez inclure toutes les balises que vous voulez placer sur la ressource. Voici un exemple montrant comment ajouter des balises supplémentaires à une ressource via des applets de commande PowerShell.

Affectez toutes les balises actuelles pour la machine virtuelle à la variable `$tags` à l’aide des propriétés `Get-AzResource` et `Tags`.

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

Pour afficher les balises actuelles, entrez la variable.

```azurepowershell-interactive
$tags
```

Voici à quoi peut ressembler le résultat :

```output
Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

Dans l’exemple suivant, nous ajoutons une balise `Location` avec la valeur `myLocation`. Utilisez `+=` pour ajouter la nouvelle paire clé/valeur à la liste `$tags`.

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

Utilisez `Set-AzResource` pour définir toutes les balises définies dans la variable *$tags* sur la machine virtuelle.

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

Utilisez `Get-AzResource` pour afficher toutes les balises sur la ressource.

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

La sortie doit ressembler à ceci, incluant la nouvelle balise :

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```

### <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le balisage de vos ressources Azure, consultez [Présentation d’Azure Resource Manager](../azure-resource-manager/management/overview.md) et [Organisation des ressources Azure à l’aide de balises](../azure-resource-manager/management/tag-resources.md).
- Pour voir comment les balises peuvent vous aider à gérer votre utilisation des ressources Azure, consultez [Comprendre votre facture Azure](../cost-management-billing/understand/review-individual-bill.md).
