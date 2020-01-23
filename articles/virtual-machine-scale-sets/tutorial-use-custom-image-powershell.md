---
title: Tutoriel - Utiliser une image de machine virtuelle personnalisée dans un groupe identique avec Azure PowerShell
description: Découvrez comment utiliser Azure PowerShell afin de créer une image de machine virtuelle personnalisée que vous pouvez utiliser pour déployer un groupe de machines virtuelles identiques
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: daef03b411a451fc3e5b73e46091672810b0f9bd
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278292"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Tutoriel : Créer et utiliser une image personnalisée pour des groupes de machines virtuelles identiques avec Azure PowerShell

Lorsque vous créez un groupe identique, vous spécifiez une image à utiliser lors du déploiement des instances de machine virtuelle. Pour réduire le nombre de tâches une fois que les instances de machine virtuelle sont déployées, vous pouvez utiliser une image de machine virtuelle personnalisée. Cette image de machine virtuelle personnalisée inclut les configurations ou installations des applications requises. Toutes les instances de machine virtuelle créées dans le groupe identique utilisent l’image de machine virtuelle personnalisée et sont prêtes à répondre au trafic des applications. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer et personnaliser une machine virtuelle
> * Déprovisionner et généraliser la machine virtuelle
> * Créer une image de machine virtuelle personnalisée à partir de la machine virtuelle source
> * Déployer un groupe identique qui utilise l’image de machine virtuelle personnalisée

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-and-configure-a-source-vm"></a>Créer et configurer une machine virtuelle source

>[!NOTE]
> Ce tutoriel vous sert de guide pour créer et utiliser une image de machine virtuelle généralisée. La création d’un groupe identique depuis un disque dur virtuel spécialisé n’est pas prise en charge.

Tout d’abord, créez un groupe de ressources avec [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup), puis créez une machine virtuelle avec [New-AzVM](/powershell/module/az.compute/new-azvm). Cette machine virtuelle est ensuite utilisée comme source d’une image de machine virtuelle personnalisée. L’exemple suivant montre la création d’une machine virtuelle nommée *myCustomVM* dans le groupe de ressources nommé *myResourceGroup*. Lorsque vous y êtes invité, entrez un nom d’utilisateur et un mot de passe qui serviront d’informations d’identification pour ouvrir une session de la machine virtuelle :

```azurepowershell-interactive
# Create a resource a group
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"

# Create a Windows Server 2016 Datacenter VM
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Name "myCustomVM" `
  -ImageName "Win2016Datacenter" `
  -OpenPorts 3389
```

Pour vous connecter à votre machine virtuelle, obtenez l’adresse IP publique avec [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) comme suit :

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Créez une connexion distante avec la machine virtuelle. Si vous utilisez Azure Cloud Shell, effectuez cette étape à partir d’une invite de commandes PowerShell locale ou du client Bureau à distance. Fournissez votre propre adresse IP à partir de la commande précédente. À l’invite, saisissez les informations d’identification que vous avez utilisées lors de la création de la machine virtuelle au moment de la première étape :

```powershell
mstsc /v:<IpAddress>
```

Pour personnaliser votre machine virtuelle, nous allons installer un serveur web de base. Lorsque l’instance de machine virtuelle dans le groupe identique est déployée, elle doit avoir tous les packages nécessaires pour exécuter une application web. Ouvrez une invite de commandes PowerShell locale sur la machine virtuelle et installez le serveur web IIS avec [Install-WindowsFeature](/powershell/module/servermanager/install-windowsfeature) comme suit :

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

La dernière étape de préparation de votre machine virtuelle pour une utilisation en tant qu’image personnalisée consiste à généraliser la machine virtuelle. Sysprep supprime toutes les informations et les configurations de votre compte personnel, et réinitialise l’état de la machine virtuelle pour les déploiements futurs. Pour plus d’informations, consultez [Utilisation de Sysprep : de Sysprep](https://technet.microsoft.com/library/bb457073.aspx).

Pour généraliser la machine virtuelle, exécutez Sysprep et définissez la machine virtuelle dans l’optique d’une expérience prête à l’emploi. Lorsque vous avez terminé, demandez à Sysprep d’arrêter la machine virtuelle :

```powershell
C:\Windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown
```

La connexion distante à la machine virtuelle est fermée automatiquement lorsque Sysprep termine le processus et que la machine virtuelle est arrêtée.


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Créer une image de machine virtuelle personnalisée à partir de la machine virtuelle source
La machine virtuelle source est à présent personnalisée avec le serveur web IIS installé. Nous allons créer l’image de machine virtuelle personnalisée à utiliser avec un groupe identique.

Pour créer une image, la machine virtuelle doit être libérée. Libérez la machine virtuelle avec la commande [Stop-AzVm](/powershell/module/az.compute/stop-azvm). Définissez ensuite l’état de la machine virtuelle comme généralisé avec [Set-AzVm](/powershell/module/az.compute/set-azvm) afin que la plateforme Azure sache que la machine virtuelle est prête pour l’utilisation d’une image personnalisée. Vous pouvez uniquement créer une image à partir d’une machine virtuelle généralisée :

```azurepowershell-interactive
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Force
Set-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Generalized
```

Quelques minutes peuvent être nécessaires pour libérer et généraliser la machine virtuelle.

Maintenant, créez une image de la machine virtuelle avec [New-AzImageConfig](/powershell/module/az.compute/new-azimageconfig) et [New-AzImage](/powershell/module/az.compute/new-azimage). L’exemple suivant crée une image nommée *myImage* à partir de votre machine virtuelle :

```azurepowershell-interactive
# Get VM object
$vm = Get-AzVM -Name "myCustomVM" -ResourceGroupName "myResourceGroup"

# Create the VM image configuration based on the source VM
$image = New-AzImageConfig -Location "EastUS" -SourceVirtualMachineId $vm.ID 

# Create the custom VM image
New-AzImage -Image $image -ImageName "myImage" -ResourceGroupName "myResourceGroup"
```

## <a name="configure-the-network-security-group-rules"></a>Configurer des règles de groupe de sécurité réseau
Avant de créer le groupe identique, nous devons configurer des règles de groupe de sécurité réseau pour autoriser l’accès HTTP, RDP et Remoting. 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$rule2 = New-AzNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP" -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389

$rule3 = New-AzNetworkSecurityRuleConfig -Name remoting-rule -Description "Allow PS Remoting" -Access Allow -Protocol Tcp -Direction Inbound -Priority 120 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 5985

New-AzNetworkSecurityGroup -Name "myNSG" -ResourceGroupName "myResourceGroup" -Location "EastUS" -SecurityRules $rule1,$rule2,$rule3
```

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Créer un groupe identique à partir de l’image de machine virtuelle personnalisée
Créez à présent un groupe identique avec [New-AzVmss](/powershell/module/az.compute/new-azvmss) qui utilise le paramètre `-ImageName` pour définir l’image de machine virtuelle personnalisée créée à l’étape précédente. Pour distribuer le trafic aux différentes instances de machine virtuelle, un équilibreur de charge est également créé. L’équilibreur de charge inclut des règles pour distribuer le trafic sur le port TCP 80, ainsi que pour autoriser le trafic Bureau à distance sur le port TCP 3389 et le trafic Accès distant PowerShell sur le port TCP 5985. Lorsque vous y êtes invité, fournissez vos propres informations d’identification d’administration souhaitées pour les instances de machine virtuelle dans le groupe identique :

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -SecurityGroupName "myNSG"
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "myImage"
```

La création et la configuration des l’ensemble des ressources et des machines virtuelles du groupe identique prennent quelques minutes.


## <a name="test-your-scale-set"></a>Tester votre groupe identique
Pour voir votre groupe identique en action, obtenez l’adresse IP publique de votre équilibreur de charge avec [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress), comme suit :


```azurepowershell-interactive
Get-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Name "myPublicIPAddress" | Select IpAddress
```

Entrez l’adresse IP publique dans votre navigateur web. La page web IIS par défaut s’affiche comme dans l’exemple suivant :

![IIS s’exécutant à partir d’une image de machine virtuelle personnalisée](media/tutorial-use-custom-image-powershell/default-iis-website.png)


## <a name="clean-up-resources"></a>Nettoyer les ressources
Pour supprimer votre groupe identique et d’autres ressources, supprimez le groupe de ressources et toutes ses ressources avec [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Le paramètre `-Force` confirme que vous souhaitez supprimer les ressources sans passer par une invite supplémentaire à cette fin. Le paramètre `-AsJob` retourne le contrôle à l’invite de commandes sans attendre que l’opération se termine.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à créer et utiliser une image de machine virtuelle personnalisée pour vos groupes identiques au moyen d’Azure PowerShell :

> [!div class="checklist"]
> * Créer et personnaliser une machine virtuelle
> * Déprovisionner et généraliser la machine virtuelle
> * Créer une image de machine virtuelle personnalisée
> * Déployer un groupe identique qui utilise l’image de machine virtuelle personnalisée

Passez au didacticiel suivant pour apprendre à déployer des applications dans votre groupe identique.

> [!div class="nextstepaction"]
> [Déployer des applications dans vos groupes identiques](tutorial-install-apps-powershell.md)
