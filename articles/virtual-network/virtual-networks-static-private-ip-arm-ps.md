---
title: Configurer des adresses IP privées pour des machines virtuelles - Azure PowerShell | Microsoft Docs
description: Découvrez comment configurer des adresses IP privées pour des machines virtuelles à l’aide de PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b0e8153f1d0cecd4efe66dc7cce64addd6ed62aa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-powershell"></a>Configurer des adresses IP privées pour une machine virtuelle à l’aide de PowerShell

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

Azure propose deux modèles de déploiement : Azure Resource Manager et classique. Microsoft recommande la création de ressources via le modèle de déploiement Resource Manager. Pour en savoir plus sur les différences entre deux modèles, lisez l’article [Comprendre les modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement de Resource Manager. Vous pouvez également [gérer une adresse IP privée statique dans le modèle de déploiement classique](virtual-networks-static-private-ip-classic-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Les exemples de commandes PowerShell ci-dessous supposent qu’un environnement simple a déjà été créé conformément au scénario décrit ci-dessous. Si vous souhaitez exécuter les commandes telles qu’elles sont affichées dans ce document, créez d’abord l’environnement de test décrit dans [Créer un réseau virtuel](quick-create-powershell.md).

## <a name="create-a-vm-with-a-static-private-ip-address"></a>Créer une machine virtuelle avec une adresse IP privée statique
Pour créer une machine virtuelle nommée *DNS01* dans le sous-réseau *FrontEnd* d’un réseau virtuel nommé *TestVNet* avec l’adresse IP privée statique *192.168.1.101*, procédez comme suit :

1. Définissez des variables pour le compte de stockage, l’emplacement, le groupe de ressources et les informations d’identification à utiliser. Vous devez entrer un nom d’utilisateur et un mot de passe pour la machine virtuelle. Le compte de stockage et le groupe de ressources doivent exister.

    ```powershell
    $stName  = "vnetstorage"
    $locName = "Central US"
    $rgName  = "TestRG"
    $cred    = Get-Credential -Message "Type the name and password of the local administrator account."
    ```

2. Récupérez le réseau virtuel et le sous-réseau dans lesquels vous voulez créer la machine virtuelle.

    ```powershell
    $vnet   = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    $subnet = $vnet.Subnets[0].Id
    ```

3. Si nécessaire, créez une adresse IP publique pour accéder à la machine virtuelle à partir d’Internet.

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name TestPIP -ResourceGroupName $rgName `
    -Location $locName -AllocationMethod Dynamic
    ```

4. Créez une carte réseau à l’aide de l’adresse IP privée statique que vous souhaitez affecter à la machine virtuelle. Assurez-vous que l’adresse IP fait partie de la plage de sous-réseau à laquelle vous ajoutez la machine virtuelle. Il s’agit de l’étape principale de cet article, dans laquelle vous définissez l’adresse IP privée comme statique.

    ```powershell
    $nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName `
    -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id `
    -PrivateIpAddress 192.168.1.101
    ```

5. Créer la machine virtuelle avec la carte réseau :

    ```powershell
    $vm = New-AzureRmVMConfig -VMName DNS01 -VMSize "Standard_A1"
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName DNS01 `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri `
    -CreateOption fromImage
    New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm 
    ```

Il est recommandé de ne pas statiquement assigner l’IP privée assignée à la machine virtuelle Azure au sein du système d’exploitation d’une machine virtuelle, sauf si nécessaire, par exemple lorsque [vous assignez plusieurs d’adresses IP à une machine virtuelle Windows](virtual-network-multiple-ip-addresses-powershell.md). Si vous définissez manuellement l’adresse IP privée dans le système d’exploitation, assurez-vous qu’il s’agit de la même adresse que l’adresse IP privée assignée à [l’interface réseau](virtual-network-network-interface-addresses.md#change-ip-address-settings) Azure ou vous pouvez perdre la connectivité à la machine virtuelle. En savoir plus sur les paramètres [d’adresse IP privée](virtual-network-network-interface-addresses.md#private). Vous ne devez jamais assigner manuellement l’adresse IP publique assignée à une machine virtuelle Azure au sein du système d’exploitation de la machine virtuelle.

## <a name="retrieve-static-private-ip-address-information-for-a-network-interface"></a>Récupérer les informations d’adresse IP privée statique d’une interface réseau
Pour visualiser les informations d’adresse IP privée statique relatives à la machine virtuelle créée avec le script ci-dessus, exécutez la commande PowerShell ci-après et examinez les valeurs de *PrivateIpAddress* et *PrivateIpAllocationMethod* :

```powershell
Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
```

Sortie attendue :

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    Etag                 : W/"[Id]"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"[Id]\"",
                               "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Static",
                               "Subnet": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="remove-a-static-private-ip-address-from-a-network-interface"></a>Supprimer une adresse IP privée statique d’une interface réseau
Pour supprimer l’adresse IP privée statique ajoutée à la machine virtuelle dans le script ci-dessus, exécutez les commandes PowerShell suivantes :

```powershell
$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Dynamic"
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Sortie attendue :

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    Etag                 : W/"[Id]"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WindowsVM"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"[Id]\"",
                               "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Dynamic",
                               "Subnet": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="add-a-static-private-ip-address-to-a-network-interface"></a>Ajouter une adresse IP privée statique à une interface réseau
Pour ajouter une adresse IP privée statique à la machine virtuelle créée à l’aide du script ci-dessus, exécutez la commande suivante :

```powershell
$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Static"
$nic.IpConfigurations[0].PrivateIpAddress = "192.168.1.101"
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Il est recommandé de ne pas statiquement assigner l’IP privée assignée à la machine virtuelle Azure au sein du système d’exploitation d’une machine virtuelle, sauf si nécessaire, par exemple lorsque [vous assignez plusieurs d’adresses IP à une machine virtuelle Windows](virtual-network-multiple-ip-addresses-powershell.md). Si vous définissez manuellement l’adresse IP privée dans le système d’exploitation, assurez-vous qu’il s’agit de la même adresse que l’adresse IP privée assignée à [l’interface réseau](virtual-network-network-interface-addresses.md#change-ip-address-settings) Azure ou vous pouvez perdre la connectivité à la machine virtuelle. En savoir plus sur les paramètres [d’adresse IP privée](virtual-network-network-interface-addresses.md#private). Vous ne devez jamais assigner manuellement l’adresse IP publique assignée à une machine virtuelle Azure au sein du système d’exploitation de la machine virtuelle.

## <a name="change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>Modifier la méthode d’allocation pour une adresse IP privée affectée à une interface réseau

Une adresse IP privée est affectée à une carte réseau avec la méthode d’allocation statique ou dynamique. Les adresses IP dynamiques peuvent changer après le démarrage d’une machine virtuelle qui présentait précédemment l’état Arrêté (désalloué). Cela peut entraîner des problèmes si la machine virtuelle héberge un service qui requiert la même adresse IP, même après le redémarrage à partir de l’état Arrêté (désalloué). Les adresses IP statiques sont conservées jusqu’à ce que la machine virtuelle soit supprimée. Pour modifier la méthode d’allocation d’une adresse IP, exécutez le script suivant, qui fait passer la méthode d’allocation de dynamique à statique. Si la méthode d’allocation pour l’adresse IP privée actuelle est statique, remplacez *Static* par *Dynamic* avant d’exécuter le script.

```powershell
$RG = "TestRG"
$NIC_name = "testnic1"

$nic = Get-AzureRmNetworkInterface -ResourceGroupName $RG -Name $NIC_name
$nic.IpConfigurations[0].PrivateIpAllocationMethod = 'Static'
Set-AzureRmNetworkInterface -NetworkInterface $nic 
$IP = $nic.IpConfigurations[0].PrivateIpAddress

Write-Host "The allocation method is now set to"$nic.IpConfigurations[0].PrivateIpAllocationMethod"for the IP address" $IP"." -NoNewline
```

Si vous ne connaissez pas le nom de la carte réseau, vous pouvez afficher la liste des cartes réseau d’un groupe de ressources en entrant la commande suivante :

```powershell
Get-AzureRmNetworkInterface -ResourceGroupName $RG | Where-Object {$_.ProvisioningState -eq 'Succeeded'} 
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la gestion des [paramètres d’adresse IP](virtual-network-network-interface-addresses.md).