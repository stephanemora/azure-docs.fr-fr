---
title: Guide pratique pour étiqueter une ressource de machine virtuelle Windows dans Azure
description: Découvrez comment baliser une machine virtuelle Windows créée dans Azure à l’aide du modèle de déploiement de Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: b646b1a14d6cedcafa662192229daa570a0d2441
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616427"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Balisage d’une machine virtuelle Windows dans Azure
Cet article décrit différentes façons d’ajouter des balises à une machine virtuelle Windows dans Azure à l’aide du modèle de déploiement Resource Manager. Les balises sont des paires clé/valeur définies par l’utilisateur, qui peuvent être placées directement sur une ressource ou sur un groupe de ressources. Azure prend actuellement en charge jusqu’à 50 étiquettes par ressource et par groupe de ressources. Les balises peuvent être placées sur une ressource au moment de la création ou bien ajoutées à une ressource existante. Notez que les balises ne sont prises en charge que pour les ressources créées via le modèle de déploiement Resource Manager. Si vous souhaitez baliser une machine virtuelle Linux, consultez l’article [How to tag a Linux virtual machine in Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)(Balisage d’une machine virtuelle Linux dans Azure).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Balisage avec PowerShell
Pour créer, ajouter et supprimer des balises via PowerShell, vous devez d’abord configurer votre [environnement PowerShell avec Azure Resource Manager][PowerShell environment with Azure Resource Manager]. Une fois que vous avez terminé la configuration, vous pouvez placer des balises sur les ressources Calcul, Réseau et Stockage au moment de la création ou après la création de la ressource via PowerShell. Cet article se concentre sur l’affichage et la modification des balises placées sur des machines virtuelles.

 

Accédez d’abord à une machine virtuelle via l’applet de commande `Get-AzVM` .

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Si votre machine virtuelle contient déjà des balises, vous verrez toutes les balises sur votre ressource :

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Si vous voulez ajouter des balises via PowerShell, vous pouvez utiliser la commande `Set-AzResource` . Notez que lors de la mise à jour des balises via PowerShell, les balises sont mises à jour comme un tout. Ainsi, si vous ajoutez une balise à une ressource qui a déjà des balises, vous devez inclure toutes les balises que vous voulez placer sur la ressource. Voici un exemple montrant comment ajouter des balises supplémentaires à une ressource via des applets de commande PowerShell.

Cette première applet de commande fait pointer toutes les balises placées sur *MyTestVM* vers la variable *$tags*, à l’aide des propriétés `Get-AzResource` et `Tags`.

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

La deuxième commande affiche les balises pour la variable donnée.

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

La troisième commande ajoute une balise supplémentaire à la variable *$tags* . Notez l'utilisation de **+=** pour ajouter la nouvelle paire clé/valeur à la liste *$tags* .

        PS C:\> $tags += @{Location="MyLocation"}

La quatrième commande définit toutes les balises définies dans la variable *$tags* sur la ressource donnée. Dans ce cas, il s’agit de MyTestVM.

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

La cinquième commande affiche toutes les balises sur la ressource. Comme vous pouvez le voir, *Emplacement* est maintenant défini en tant que balise avec la valeur *MyLocation*.

```
    PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
    Location      MyLocation
```

Pour en savoir plus sur le balisage dans PowerShell, consultez les [applets de commande des ressources Azure][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur le balisage de vos ressources Azure, consultez [Présentation d’Azure Resource Manager][Azure Resource Manager Overview] et [Organisation des ressources Azure à l’aide de balises][Using Tags to organize your Azure Resources].
* Pour voir en quoi les balises peuvent vous aider à gérer votre utilisation des ressources Azure, consultez [Comprendre votre facture Azure][Understanding your Azure Bill] et [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/management/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
