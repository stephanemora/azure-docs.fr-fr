---
title: Mise hors service de la série Av1
description: Informations relatives à la mise hors service des tailles de machines virtuelles de la série AV1.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: mimckitt
ms.openlocfilehash: c382954cf54779350e78fdadef7d0f4738dca48f
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634663"
---
# <a name="av1-series-retirement"></a>Mise hors service de la série Av1

Le 31 août 2024, nous procèderons à la mise hors service des machines virtuelles des plans De base et Standard de la série A. Vous devrez donc migrer vos charges de travail, avant cette date, vers des machines virtuelles de la série AV2, dotées de davantage de mémoire par processeur virtuel et d’un système de stockage plus rapide, sur des disques SSD.

> [!NOTE]
> Dans certains cas, les utilisateurs devront libérer la machine virtuelle avant le redimensionnement. Cela peut se produire si la nouvelle taille n’est pas disponible sur le cluster matériel qui héberge actuellement la machine virtuelle.


## <a name="migrate-workloads-from-basic-and-standard-a-series-vms-to-av2-series-vms"></a>Migrer des charges de travail des machines virtuelles des plans De base et Standard de la série A vers des machines virtuelles de la série Av2 

Par exemple, vous pouvez redimensionner vos machines virtuelles vers la série Av2 en utilisant le [portail Azure](https://portal.azure.com), [PowerShell](windows/resize-vm.md) et [CLI](linux/change-vm-size.md). Vous trouverez ci-dessous des exemples de redimensionnement de votre machine virtuelle à l’aide du portail Azure et PowerShell. 

> [!IMPORTANT]
> Le redimensionnement de la machine virtuelle entraîne un redémarrage. Nous vous recommandons d’effectuer des actions qui entraîneront un redémarrage pendant les heures creuses de vos activités. 

### <a name="azure-portal"></a>Portail Azure 
1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Tapez **machines virtuelles** dans la zone de recherche.
1. Sous **Services**, sélectionnez **Machines virtuelles**.
1. Dans l'onglet **Machines virtuelles**, sélectionnez la machine virtuelle à redémarrer.
1. Dans le menu de gauche, sélectionnez **taille**.
1. Choisissez une nouvelle taille Av2 dans la liste des tailles disponibles, puis sélectionnez **Redimensionner**.

### <a name="azure-powershell"></a>Azure PowerShell
1. Définissez les variables relatives au groupe de ressources et au nom de la machine virtuelle. Remplacez les valeurs par les informations de la machine virtuelle que vous souhaitez redimensionner. 

    ```powershell
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    ```
2. Liste les tailles de machines virtuelles qui sont disponibles sur le cluster matériel sur lequel la machine virtuelle est hébergée.

    ```powershell
    Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName
    ```

3. Redimensionnez la machine virtuelle.

    ```powershell
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
    $vm.HardwareProfile.VmSize = "<newAv2VMsize>"
    Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
    ```

## <a name="help-and-support"></a>Aide et support

Si vous avez des questions, n’hésitez pas à contacter les experts de notre communauté sur [Microsoft Q&A](/answers/topics/azure-virtual-machines.html). Si vous disposez d’un plan de support et que vous avez besoin d’une aide technique, créez une [demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) :

1. En regard de Type de problème, spécifiez Technique.
1. Pour Abonnement, sélectionnez votre abonnement.
1. En regard de Service, cliquez sur Mes services.
1. En regard de Type de service, sélectionnez Machine virtuelle exécutant Windows/Linux.
1. En regard de Récapitulatif, entrez un résumé de votre demande.
1. En regard de Type de problème, sélectionnez Assistance pour redimensionner ma machine virtuelle.
1. En regard de Sous-type de problème, sélectionnez l’option qui vous concerne.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur les [machines virtuelles de la série Av2](av2-series.md)
