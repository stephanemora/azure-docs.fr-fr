---
title: Créer une machine virtuelle avec une adresse IP publique statique - Azure PowerShell | Microsoft Docs
description: Découvrez comment créer une machine virtuelle avec une adresse IP publique statique à l’aide de PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68656db0b76a29e7ab36fd6fa9ad4647712233ee
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38696581"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-powershell"></a>Créer une machine virtuelle avec une adresse IP publique statique à l’aide de PowerShell

> [!div class="op_single_selector"]
> * [Portail Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [interface de ligne de commande Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (classique)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md). Cet article traite de l’utilisation du modèle de déploiement Resource Manager que Microsoft recommande pour la plupart des nouveaux déploiements à la place du modèle de déploiement classique.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="start-your-script"></a>Démarrer votre script
Vous pouvez télécharger le script PowerShell complet utilisé [ici](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1). Suivez les étapes ci-dessous pour modifier le script afin qu’il fonctionne dans votre environnement.

Remplacez les valeurs des variables suivantes par celles que vous souhaitez utiliser pour votre déploiement. Les valeurs suivantes mappent au scénario utilisé dans cet article :

```powershell
# Set variables resource group
$rgName                = "IaaSStory"
$location              = "West US"

# Set variables for VNet
$vnetName              = "WTestVNet"
$vnetPrefix            = "192.168.0.0/16"
$subnetName            = "FrontEnd"
$subnetPrefix          = "192.168.1.0/24"

# Set variables for storage
$stdStorageAccountName = "iaasstorystorage"

# Set variables for VM
$vmSize                = "Standard_A1"
$diskSize              = 127
$publisher             = "MicrosoftWindowsServer"
$offer                 = "WindowsServer"
$sku                   = "2012-R2-Datacenter"
$version               = "latest"
$vmName                = "WEB1"
$osDiskName            = "osdisk"
$nicName               = "NICWEB1"
$privateIPAddress      = "192.168.1.101"
$pipName               = "PIPWEB1"
$dnsName               = "iaasstoryws1"
```

## <a name="create-the-necessary-resources-for-your-vm"></a>Créer les ressources nécessaires pour votre machine virtuelle
Avant de créer une machine virtuelle, vous devez mettre à sa disposition un groupe de ressources, un réseau virtuel, une adresse IP publique et une carte réseau.

1. Créez un groupe de ressources.

    ```powershell
    New-AzureRmResourceGroup -Name $rgName -Location $location
    ```

2. Créez le réseau virtuel et le sous-réseau.

    ```powershell
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
        -AddressPrefix $vnetPrefix -Location $location

    Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
        -VirtualNetwork $vnet -AddressPrefix $subnetPrefix

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

3. Créez la ressource IP publique. 

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
        -AllocationMethod Static -DomainNameLabel $dnsName -Location $location
    ```

4. Créez la carte réseau pour la machine virtuelle dans le sous-réseau créé ci-dessus, avec l’adresse IP publique. Notez que la première applet de commande récupère le réseau virtuel d’Azure. Cette opération est nécessaire dans la mesure où l’applet de commande `Set-AzureRmVirtualNetwork` a été exécutée pour modifier le réseau virtuel existant.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
        -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
        -PublicIpAddress $pip
    ```

5. Créez un compte de stockage pour héberger le lecteur du système d’exploitation de la machine virtuelle.

    ```powershell
    $stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
    -ResourceGroupName $rgName -Type Standard_LRS -Location $location
    ```

## <a name="create-the-vm"></a>Création de la machine virtuelle
Une fois toutes les ressources nécessaires en place, vous pouvez créer une machine virtuelle.

1. Créez l’objet de configuration pour la machine virtuelle.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    ```

2. Obtenez les informations d’identification du compte d’administrateur local de la machine virtuelle.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password for the local administrator account."
    ```

3. Créez un objet de configuration de machine virtuelle.

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```

4. Définissez l’image du système d’exploitation pour la machine virtuelle.

    ```powershell
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
        -Offer $offer -Skus $sku -Version $version
    ```

5. Configurez le disque du système d’exploitation.

    ```powershell
    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
    ```

6. Ajoutez la carte réseau à la machine virtuelle.

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary
    ```

7. Créez la machine virtuelle.

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location
    ```

8. Enregistrez le fichier de script.

## <a name="run-the-script"></a>Exécutez le script

Après avoir apporté les modifications nécessaires, exécutez le script précédent. La création de la machine virtuelle prend quelques minutes.

## <a name="set-ip-addresses-within-the-operating-system"></a>Définir des adresses IP au sein du système d’exploitation

Vous ne devez jamais assigner manuellement l’adresse IP publique assignée à une machine virtuelle Azure au sein du système d’exploitation de la machine virtuelle. Il est recommandé de ne pas assigner statiquement l’IP privée assignée à la machine virtuelle Azure au sein du système d’exploitation d’une machine virtuelle, sauf si nécessaire, par exemple lorsque [vous assignez plusieurs d’adresses IP à une machine virtuelle Windows](virtual-network-multiple-ip-addresses-powershell.md). Si vous définissez manuellement l’adresse IP privée dans le système d’exploitation, assurez-vous qu’il s’agit de la même adresse que l’adresse IP privée assignée à [l’interface réseau](virtual-network-network-interface-addresses.md#change-ip-address-settings) Azure, ou vous pouvez perdre la connectivité à la machine virtuelle. En savoir plus sur les paramètres [d’adresse IP privée](virtual-network-network-interface-addresses.md#private).

## <a name="next-steps"></a>Étapes suivantes

Tout le trafic réseau peut circuler vers et en provenance de la machine virtuelle créée dans le cadre de cet article. Vous pouvez définir des règles de sécurité entrantes et sortantes au sein d’un groupe de sécurité réseau qui limite le trafic vers et en provenance de l’interface réseau, du sous-réseau ou des deux. Pour en savoir plus sur les groupes de sécurité réseau, consultez [Vue d’ensemble du groupe de sécurité réseau](security-overview.md).