---
title: Déployer des machines virtuelles dans un groupe à haute disponibilité à l’aide d’Azure PowerShell
description: Découvrez comment utiliser Azure PowerShell pour déployer des machines virtuelles hautement disponibles dans des groupes à haute disponibilité
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: f94b69614c32c09be275780b3a56e1e8f9a2d687
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110668090"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-powershell"></a>Créer et déployer des machines virtuelles dans un groupe à haute disponibilité à l’aide d’Azure PowerShell

Dans ce tutoriel, vous allez apprendre à augmenter la disponibilité et la fiabilité de vos machines virtuelles à l’aide de groupes à haute disponibilité. Les groupes à haute disponibilité garantissent que les machines virtuelles que vous déployez sur Azure sont distribuées, sur plusieurs nœuds matériels isolés, dans un cluster. 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un groupe à haute disponibilité
> * Créer une machine virtuelle dans un groupe à haute disponibilité
> * Vérifier les tailles de machines virtuelles disponibles
> * Vérifier Azure Advisor


## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. 

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez également lancer Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur Entrée pour les exécuter.

## <a name="create-an-availability-set"></a>Créer un groupe à haute disponibilité

Le matériel situé à un emplacement est divisé en plusieurs domaines de mise à jour et d’erreur. Un **domaine de mise à jour** est un groupe de machines virtuelles et d’équipements physiques sous-jacents pouvant être redémarrés en même temps. Les machines virtuelles d’un même **domaine d’erreur** partagent un espace de stockage commun ainsi qu’une source d’alimentation et un commutateur réseau.  

Vous pouvez créer un groupe à haute disponibilité avec la commande [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). Dans cet exemple, le nombre de domaines de mise à jour et d’erreur s’élève à *2* et le groupe à haute disponibilité est nommé *myAvailabilitySet*.

Créez un groupe de ressources.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Créez un groupe à haute disponibilité managé à l’aide de [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) avec le paramètre `-sku aligned`.

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Créer des machines virtuelles dans un groupe à haute disponibilité
Vous devez créer des machines virtuelles au sein du groupe à haute disponibilité pour vous assurer qu’elles sont correctement réparties dans le matériel. Vous ne pouvez pas ajouter une machine virtuelle existante à un groupe à haute disponibilité après sa création. 


Quand vous créez une machine virtuelle à l’aide de [New-AzVM](/powershell/module/az.compute/new-azvm), vous utilisez le paramètre `-AvailabilitySetName` pour spécifier le nom du groupe à haute disponibilité.

Tout d’abord, définissez un nom d’utilisateur administrateur et un mot de passe pour la machine virtuelle avec [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) :

```azurepowershell-interactive
$cred = Get-Credential
```

Créez maintenant deux machines virtuelles avec [New-AzVM](/powershell/module/az.compute/new-azvm) dans le groupe à haute disponibilité.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

La création et la configuration des deux machines virtuelles prennent quelques minutes. Quand vous avez terminé, vous disposez de deux machines virtuelles réparties sur le matériel sous-jacent. 

Si vous examinez le groupe à haute disponibilité dans le portail en accédant à **Groupes de ressources** > **myResourceGroupAvailability** > **myAvailabilitySet**, vous devez voir la façon dont les machines virtuelles sont réparties entre les deux domaines d’erreur et de mise à jour.

![Groupe à haute disponibilité dans le portail](./media/tutorial-availability-sets/fd-ud.png)

> [!NOTE]
> Dans certaines circonstances, deux machines virtuelles dans le même AvailabilitySet peuvent partager le même FaultDomain. Pour confirmer cela, accédez à votre groupe à haute disponibilité et vérifiez la colonne Domaine d'erreur Cela peut être dû à la séquence suivante lors du déploiement des machines virtuelles :
> 1. Déployer la première machine virtuelle
> 1. Arrêtez/libérez la première machine virtuelle
> 1. Déployez la deuxième machine virtuelle Dans ces circonstances, le disque du système d’exploitation de la deuxième machine virtuelle peut être créé sur le même domaine d’erreur que la première, et la deuxième machine virtuelle atterrira également sur le même FaultDomain. Pour éviter ce problème, il est recommandé de ne pas arrêter ou libérer la machine virtuelle entre ses déploiements.

## <a name="check-for-available-vm-sizes"></a>Vérifier les tailles de machines virtuelles disponibles 

Lorsque vous créez une machine virtuelle à l’intérieur d’un groupe à haute disponibilité, vous devez connaître les tailles de machine virtuelle qui sont disponibles sur le matériel. Utilisez la commande [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) afin d’obtenir toutes les tailles disponibles pour les machines virtuelles que vous pouvez déployer dans le groupe à haute disponibilité.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Vérifier Azure Advisor 

Vous pouvez également utiliser Azure Advisor pour obtenir plus d’informations sur les façons d’améliorer la disponibilité de vos machines virtuelles. Azure Advisor analyse votre configuration et vos données de télémétrie d’utilisation, puis recommande des solutions qui peuvent vous aider à améliorer la rentabilité, les performances, la disponibilité et la sécurité de vos ressources Azure.

Connectez-vous au [portail Azure](https://portal.azure.com), sélectionnez **Tous les services** et saisissez **Advisor**. Le tableau de bord Advisor propose des recommandations personnalisées pour l’abonnement sélectionné. Pour plus d’informations, consultez [Bien démarrer avec Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un groupe à haute disponibilité
> * Créer une machine virtuelle dans un groupe à haute disponibilité
> * Vérifier les tailles de machines virtuelles disponibles
> * Vérifier Azure Advisor

Passez au didacticiel suivant pour en savoir plus sur les groupes de machines virtuelles identiques.

> [!div class="nextstepaction"]
> [Créer un groupe de machines virtuelles identiques](tutorial-create-vmss.md)
