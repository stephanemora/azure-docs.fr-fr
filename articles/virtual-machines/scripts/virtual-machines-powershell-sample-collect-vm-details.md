---
title: Collecter plus d’informations sur toutes les machines virtuelles dans un abonnement avec PowerShell
description: Collecter plus d’informations sur toutes les machines virtuelles dans un abonnement avec PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: v-miegge
manager: dcscontentpm
editor: v-miegge
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/01/2019
ms.author: v-miegge
ms.custom: mvc
ms.openlocfilehash: a45f0a882c58c7035badcc1270c66bd9c6fb252a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526909"
---
# <a name="collect-details-about-all-vms-in-a-subscription-with-powershell"></a>Collecter plus d’informations sur toutes les machines virtuelles dans un abonnement avec PowerShell

Ce script crée un fichier csv qui contient le nom de la machine virtuelle, le nom du groupe de ressources, la région, la taille de machine virtuelle, le réseau virtuel, le sous-réseau, l’adresse IP privée, le type de système d’exploitation et l’adresse IP publique des machines virtuelles dans l’abonnement fourni.

Si vous n’avez pas [d’abonnement Azure](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. 

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez également lancer Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur Entrée pour les exécuter.

## <a name="sample-script"></a>Exemple de script

```azurepowershell-interactive
#Provide the subscription Id where the VMs reside
$subscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

#Provide the name of the csv file to be exported
$reportName = "myReport.csv"

Select-AzSubscription $subscriptionId
$report = @()
$vms = Get-AzVM
$publicIps = Get-AzPublicIpAddress 
$nics = Get-AzNetworkInterface | ?{ $_.VirtualMachine -NE $null} 
foreach ($nic in $nics) { 
    $info = "" | Select VmName, ResourceGroupName, Region, VmSize, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
    $vm = $vms | ? -Property Id -eq $nic.VirtualMachine.id 
    foreach($publicIp in $publicIps) { 
        if($nic.IpConfigurations.id -eq $publicIp.ipconfiguration.Id) {
            $info.PublicIPAddress = $publicIp.ipaddress
            } 
        } 
        $info.OsType = $vm.StorageProfile.OsDisk.OsType 
        $info.VMName = $vm.Name 
        $info.ResourceGroupName = $vm.ResourceGroupName 
        $info.Region = $vm.Location 
        $info.VmSize = $vm.HardwareProfile.VmSize
        $info.VirturalNetwork = $nic.IpConfigurations.subnet.Id.Split("/")[-3] 
        $info.Subnet = $nic.IpConfigurations.subnet.Id.Split("/")[-1] 
        $info.PrivateIpAddress = $nic.IpConfigurations.PrivateIpAddress 
        $report+=$info 
    } 
$report | ft VmName, ResourceGroupName, Region, VmSize, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
$report | Export-CSV "$home/$reportName"
```

## <a name="script-explanation"></a>Explication du script
Ce script utilise les commandes suivantes pour créer une exportation csv des détails des machines virtuelles dans un abonnement. Chaque commande du tableau renvoie à une documentation spécifique.

|Commande|Notes|
|-|-|
|[Select-AzSubscription](/powershell/module/az.accounts/set-azcontext)|Définit le client, l’abonnement et l’environnement pour les applets de commande à utiliser dans la session actuelle.|
|[Get-AzVM](/powershell/module/az.compute/get-azvm)|Obtient les propriétés d’une machine virtuelle.|
|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)|Obtient une adresse IP publique.|
|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface)|Obtient une interface réseau.|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell de machine virtuelle dans la [documentation relative aux machines virtuelles Windows Azure](../windows/powershell-samples.md?toc=/azure/virtual-machines/windows/toc.json).
