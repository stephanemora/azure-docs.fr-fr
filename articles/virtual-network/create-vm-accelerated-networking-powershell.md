---
title: Créer une machine virtuelle Azure avec mise en réseau accélérée - Azure PowerShell
description: Apprenez à créer une machine virtuelle Linux avec mise en réseau accélérée.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: gsilva
ms.openlocfilehash: 3807c1434e3758eafe299da7b30769b41d3ede87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106302"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking-using-azure-powershell"></a>Créer une machine virtuelle Windows avec mise en réseau accélérée à l’aide d’Azure PowerShell

Ce didacticiel explique comment créer une machine virtuelle Windows (VM) avec mise en réseau accélérée. Pour créer une machine virtuelle Linux avec mise en réseau accélérée, consultez [Créer une machine virtuelle Linux avec mise en réseau accélérée](create-vm-accelerated-networking-cli.md). Une mise en réseau accélérée permet d’opérer une virtualisation d’E/S d’une racine unique (SR-IOV) sur une machine virtuelle, ce qui améliore considérablement les performances de mise en réseau. Cette voie hautement performante court-circuite l’hôte à partir du chemin d’accès aux données, réduisant ainsi la latence, l’instabilité et l’utilisation du processeur pour servir les charges de travail réseau les plus exigeantes sur les types de machines virtuelles pris en charge. L’illustration suivante montre la communication entre deux machines virtuelles avec ou sans mise en réseau accélérée :

![Comparaison](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sans mise en réseau accélérée, tout le trafic réseau en direction et en provenance de la machine virtuelle doit transiter par l’hôte et le commutateur virtuel. Le commutateur virtuel fournit au trafic réseau toutes les stratégies, telles que les groupes de sécurité réseau, les listes de contrôle d’accès, l’isolation et d’autres services de réseau virtualisé. Pour plus d’informations sur les commutateurs virtuels, consultez [Virtualisation de réseau et commutateur virtuel Hyper-V](https://technet.microsoft.com/library/jj945275.aspx).

Dans le cas d’une mise en réseau accélérée, le trafic réseau parvient à la carte réseau de la machine virtuelle avant d’être transféré vers celle-ci. Toutes les stratégies réseau que le commutateur virtuel applique sont déchargées et appliquées dans le matériel. L’application de la stratégie au niveau du matériel permet à la carte réseau de transférer le trafic directement à la machine virtuelle, en ignorant l’hôte et le commutateur virtuel, tout en conservant toutes les stratégies qu’il a appliquées dans l’hôte.

Les avantages d’une mise en réseau accélérée s’appliquent uniquement à la machine virtuelle activée. Pour des résultats optimaux, il convient d’activer cette fonctionnalité sur au moins deux machines virtuelles connectées au même réseau virtuel Azure Virtual Network. Lors de la communication entre réseaux virtuels ou d’une connexion locale, cette fonctionnalité a une incidence minimale sur la latence globale.

## <a name="benefits"></a>Avantages
* **Latence plus faible/Nombre supérieur de paquets par seconde (pps) :** la suppression du commutateur virtuel du chemin de données évite que les paquets liés au traitement des stratégies séjournent sur l'hôte, ce qui augmente le nombre de paquets pouvant être traités dans la machine virtuelle.
* **Instabilité réduite :** le traitement du commutateur virtuel dépend de la stratégie à appliquer et de la charge de travail du processeur qui effectue le traitement. Le déchargement des stratégies sur le matériel supprime cette variabilité en fournissant des paquets directement à la machine virtuelle, en supprimant l’hôte dans la communication de la machine virtuelle et toutes les interruptions de logiciel et les changements de contexte.
* **Utilisation réduite du processeur :** en contournant le commutateur virtuel de l'hôte, le processeur utilise moins de ressources pour traiter le trafic réseau.

## <a name="limitations-and-constraints"></a>Limitations et restrictions

### <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge
Les distributions suivantes sont prises en charge sans configuration supplémentaire à partir de la galerie Azure :
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**
* **Windows Server 2019 Datacenter**

### <a name="supported-vm-instances"></a>Instances de machines virtuelles prises en charge
La mise en réseau accélérée est prise en charge dans la plupart des instances d’usage général et optimisées pour le calcul (2 processeurs virtuels ou plus).  Séries prises en charge : D/DSv2 et F/Fs

Dans des instances qui acceptent l’hyperthreading, la mise en réseau accélérée est prise en charge dans des instances de machine virtuelle comptant au minimum 4 processeurs virtuels. Séries prises en charge : D/Dsv3, D/Dsv4, E/Esv3, Ea/Easv4, Fsv2, Lsv2, Ms/Mms et Ms/Mmsv2.

Pour plus d’informations sur les instances de machine virtuelle, consultez la section [Tailles des machines virtuelles Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Régions
Disponible dans toutes les régions Azure publiques et le dans le cloud Azure Government.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Activation de la mise en réseau accélérée sur une machine virtuelle en cours d’exécution
Cette fonctionnalité peut être activée sur une machine virtuelle sans mise en réseau accélérée uniquement si la machine virtuelle a une taille prise en charge et si elle est arrêtée/libérée.

### <a name="deployment-through-azure-resource-manager"></a>Déploiement par le biais d’Azure Resource Manager
Le déploiement de machines virtuelles (classiques) avec mise en réseau accélérée n’est pas possible.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Créer une machine virtuelle Windows avec mise en réseau accélérée Azure
## <a name="portal-creation"></a>Création de portail
Bien que cet article fournit des étapes pour créer une machine virtuelle avec mise en réseau accélérée à l’aide d’Azure PowerShell, vous pouvez également [Créer une machine virtuelle avec mise en réseau accélérée via le portail Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Lorsque vous créez une machine virtuelle dans le portail, dans le panneau **Créer une machine virtuelle**, choisissez l’onglet **Mise en réseau**.  Dans cet onglet, il existe une option de **Mise en réseau accélérée**.  Si vous avez choisi un [système d’exploitation pris en charge](#supported-operating-systems) et une [taille de machine virtuelle](#supported-vm-instances), cette option est automatiquement définie sur « Activé ».  Dans le cas contraire, l’option est définie sur « Désactivé » pour la Mise en réseau accélérée et l’utilisateur est informé de la raison pour laquelle elle ne peut pas être activée.   
* *Remarque :* Seuls les systèmes d’exploitation pris en charge peuvent être activés via le portail.  Si vous utilisez une image personnalisée, et que votre image prend en charge la Mise en réseau accélérée, créez votre machine virtuelle à l’aide de CLI ou Powershell. 

Une fois la machine virtuelle créée, vous pouvez confirmer l’activation de la Mise en réseau accélérée en suivant les instructions de la section Confirmer l’activation de la Mise en réseau accélérée.

## <a name="powershell-creation"></a>Création PowerShell
## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installez [Azure PowerShell](/powershell/azure/install-az-ps), version 1.0.0 ou ultérieure. Pour connaître la version actuellement installée, exécutez `Get-Module -ListAvailable Az`. Si vous devez installer ou mettre à niveau le module Az, installez la dernière version du module à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/Az). Dans une session PowerShell, connectez-vous à un compte Azure à l’aide de la commande [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Les noms de paramètre sont par exemple *myResourceGroup*, *myNic* et *myVM*.

Créez un groupe de ressources avec [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *centralus* :

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Tout d’abord, créez une configuration de sous-réseau à l’aide de la commande [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). L’exemple suivant permet de créer un sous-réseau nommé *mySubnet* :

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Créez un réseau virtuel à l’aide de la commande [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork), avec le sous-réseau *mySubnet*.

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Créez d’abord un groupe de sécurité réseau avec [New-AzNetworkSecurityRuleGroup](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

```powershell
$rdp = New-AzNetworkSecurityRuleConfig `
    -Name 'Allow-RDP-All' `
    -Description 'Allow RDP' `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 100 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389
```

Créez un groupe de sécurité réseau avec [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) et attribuez-lui la règle de sécurité *Allow-RDP-All*. Outre la règle *Allow-RDP-All*, le groupe de sécurité réseau contient plusieurs règles par défaut. Une règle par défaut désactive tous les accès entrant d’Internet, c’est pourquoi la règle *Allow-RDP-All* est affectée au groupe de sécurité réseau, afin que vous puissiez vous connecter à distance à l’ordinateur virtuel une fois celui-ci créé.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Ajoutez le groupe de sécurité réseau au sous-réseau *mySubnet* à l’aide de la commande [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). La règle du groupe de sécurité réseau est appliquée à toutes les ressources déployées dans le sous-réseau.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Créer une interface réseau avec mise en réseau accélérée
Créez une adresse IP publique avec [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Une adresse IP publique n’est pas nécessaire si vous ne prévoyez pas d’accéder à l’ordinateur virtuel depuis Internet. Par contre, elle l’est pour procéder selon les étapes mentionnées dans cet article.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Créez une interface réseau à l’aide de la commande [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) avec la mise en réseau accélérée activée et attribuez l’adresse IP publique à l’interface réseau. L’exemple suivant permet de créer une interface réseau nommée *myNic* dans le sous-réseau *mySubnet* du réseau virtuel *myVnet* et de lui affecter l’adresse IP publique *myPublicIp* :

```powershell
$nic = New-AzNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>Créer la machine virtuelle

Définissez les informations d’identification de votre machine virtuelle sur la variable `$cred` via la commande [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) :

```powershell
$cred = Get-Credential
```

Commencez par définir votre machine virtuelle avec la commande [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). L’exemple suivant crée une machine virtuelle nommée *myVM* d’une taille compatible avec la mise en réseau accélérée (*Standard_DS4_v2*) :

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Pour obtenir la liste de toutes les tailles de machine virtuelle et leurs caractéristiques, consultez [Tailles de machines virtuelles Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Créez le reste de la configuration de votre machine virtuelle avec les commandes [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) et [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). L’exemple suivant crée une machine virtuelle Windows Server 2016 R2 :

```powershell
$vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

Associez les cartes d’interface réseau que vous avez créées précédemment à l’aide de la commande [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) :

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Enfin, créez votre machine virtuelle avec la commande [New-AzVM](/powershell/module/az.compute/new-azvm) :

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Vérifier que le pilote est installé dans le système d’exploitation

Une fois la machine virtuelle créée dans Azure, connectez-vous à cette dernière et vérifiez que le pilote est installé dans Windows.

1. Dans un navigateur Internet, ouvrez le [portail](https://portal.azure.com) Azure et connectez-vous avec votre compte Azure.
2. Dans la boîte de dialogue contenant le texte *Rechercher des ressources* en haut du portail Azure, tapez *myVm*. Lorsque **myVm** apparaît dans les résultats de recherche, cliquez dessus. Si **Création** est visible sous le bouton **Connexion**, cela signifie qu’Azure n’a pas encore fini de créer la machine virtuelle. Ne cliquez sur **Connexion** dans le coin supérieur gauche de la vue d’ensemble que lorsque la mention **Création** disparaît sous le bouton **Connexion**.
3. Entrez le nom d’utilisateur et le mot de passe que vous avez entrés à l’étape de [création de la machine virtuelle](#create-the-virtual-machine). Si vous ne vous êtes jamais connecté à une machine virtuelle dans Azure, consultez la section [Se connecter à une machine virtuelle](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Cliquez avec le bouton droit sur le bouton Démarrer de Windows, puis cliquez sur **Gestionnaire de périphériques**. Développez le nœud **Cartes réseau**. Vérifiez que la mention **Carte Ethernet Mellanox ConnectX-3 fonction virtuelle** s’affiche comme dans l’image suivante :

    ![Gestionnaire de périphériques](./media/create-vm-accelerated-networking/device-manager.png)

La mise en réseau accélérée est maintenant activée pour votre machine virtuelle.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Activer la mise en réseau accélérée sur des machines virtuelles existantes
Si vous avez créé une machine virtuelle sans mise en réseau accélérée, vous pouvez activer cette fonctionnalité sur une machine virtuelle existante.  La machine virtuelle doit prendre en charge la mise en réseau accélérée et remplir les prérequis suivants (ces prérequis ont déjà été décrits plus haut) :

* La machine virtuelle doit avoir une taille prise en charge pour la mise en réseau accélérée
* La machine virtuelle doit être une image de la galerie Azure prise en charge (et une version de noyau prise en charge pour Linux)
* Toutes les machines virtuelles membres d’un groupe à haute disponibilité ou d’un groupe de machines virtuelles identiques doivent être arrêtées ou libérées avant l’activation de la mise en réseau accélérée sur une carte réseau

### <a name="individual-vms--vms-in-an-availability-set"></a>Machines virtuelles individuelles et machines virtuelles d’un groupe à haute disponibilité
Tout d’abord, arrêtez/libérez la machine virtuelle individuelle ou, dans le cas d’un groupe à haute disponibilité, toutes les machines virtuelles du groupe :

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Important : Si la machine virtuelle a été créée individuellement, en dehors d’un groupe à haute disponibilité, arrêtez/libérez simplement la machine virtuelle individuelle pour activer la mise en réseau accélérée.  Si la machine virtuelle a été créée dans un groupe à haute disponibilité, vous devez arrêter/libérer toutes les machines virtuelles membres de ce groupe avant d’activer la mise en réseau accélérée sur les cartes réseau. 

Après avoir arrêté la machine virtuelle, activez la mise en réseau accélérée sur la carte réseau de la machine virtuelle :

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Redémarrez la machine virtuelle ou, dans le cas d’un groupe à haute disponibilité, toutes les machines virtuelles du groupe, puis vérifiez que la mise en réseau accélérée est bien activée :

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>Groupe de machines virtuelles identiques (VMSS)
Un groupe de machines virtuelles identiques fonctionne un peu différemment, mais suit le même workflow.  Tout d’abord, arrêtez les machines virtuelles :

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Une fois que les machines virtuelles sont arrêtées, mettez à jour la propriété Accelerated Networking sur l’interface réseau :

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Notez que, dans un groupe de machines virtuelles identiques, les machines virtuelles sont mises à niveau selon trois méthodes de mise à jour différentes : manuelle, automatique et propagée.  Dans ces instructions, la stratégie est définie sur automatique afin que le groupe de machines virtuelles identiques récupère les mises à jour aussitôt après le redémarrage.  Pour définir la stratégie sur automatique afin que les mises à jour soient immédiatement récupérées :

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Enfin, redémarrez le groupe de machines virtuelles identiques :

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Après le redémarrage, attendez que les mises à niveau se terminent. Vous voyez alors la carte VF apparaître sur la machine virtuelle.  (Assurez-vous d’utiliser un système d’exploitation et des tailles de machine virtuelle pris en charge.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Redimensionnement des machines virtuelles existantes avec mise en réseau accélérée

Les machines virtuelles avec mise en réseau accélérée peuvent être redimensionnées uniquement en machines virtuelles qui prennent en charge la mise en réseau accélérée.  

Une machine virtuelle avec mise en réseau accélérée ne peut pas être redimensionnée en une instance de machine virtuelle qui ne prend pas en charge la mise en réseau accélérée.  Pour redimensionner l’une de ces machines virtuelles, vous devez effectuer ces opérations :

* Arrêtez/libérez la machine virtuelle ou, dans le cas d’un groupe à haute disponibilité ou d’un groupe de machines virtuelles identiques, arrêtez/libérez toutes les machines virtuelles du groupe.
* Désactivez la mise en réseau accélérée sur la carte réseau de la machine virtuelle ou, dans le cas d’un groupe à haute disponibilité ou d’un groupe de machines virtuelles identiques, de toutes les machines virtuelles du groupe.
* Une fois que la mise en réseau accélérée est désactivée, redimensionnez la machine virtuelle, ou toutes les machines virtuelles du groupe à haute disponibilité ou du groupe de machines virtuelles identiques, à une nouvelle taille qui ne prend pas en charge la mise en réseau accélérée, et redémarrez la ou les machines.
