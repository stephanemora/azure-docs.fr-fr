---
title: 'Tutoriel : Créer et tester une passerelle NAT - Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Ce tutoriel montre comment créer une passerelle NAT avec Azure PowerShell, et tester le service NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: bb75631beed73a6ebd9d1cf2c00c375726fed387
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586986"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Tutoriel : Créer une passerelle NAT avec Azure PowerShell et tester le service NAT

Dans ce tutoriel, vous allez créer une passerelle NAT pour fournir une connectivité sortante à des machines virtuelles dans Azure. Pour tester la passerelle NAT, vous déployez une machine virtuelle source et une machine virtuelle de destination. Vous allez tester la passerelle NAT en établissant des connexions sortantes à une adresse IP publique. Ces connexions iront de la machine virtuelle source vers la machine de destination. Ce tutoriel déploie la source et la destination sur deux réseaux virtuels différents dans le même groupe de ressources, pour des raisons de simplicité.

>[!NOTE] 
>Le service NAT de Réseau virtuel Azure est disponible en préversion publique à ce stade, et disponible dans un ensemble limité de [régions](./nat-overview.md#region-availability). Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Vous pouvez suivre ce tutoriel en utilisant Azure Cloud Shell ou exécuter les commandes respectives localement.  Si vous n’avez jamais utilisé Azure Cloud Shell, vous devez vous [connecter maintenant](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](https://docs.microsoft.com/cli/azure/group). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé **myResourceGroupNAT** à l’emplacement **eastus2** :


```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Créer la passerelle NAT

### <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Pour accéder à l’Internet, vous avez besoin d’une ou de plusieurs adresses IP publiques pour la passerelle NAT. Utilisez la commande [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) pour créer une ressource d’adresse IP publique nommée **myPublicIPsource** dans **myResourceGroupNAT**. Le résultat de cette commande est stocké dans une variable nommée **$publicIPsource** pour une utilisation ultérieure.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pips = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pips -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Créer un préfixe d’adresse IP publique

 Utilisez la commande [New-AzPublicIpprefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) pour créer une ressource de préfixe d’adresse IP publique nommée **myPublicIPprefixsource** dans **myResourceGroupNAT**.  Le résultat de cette commande est stocké dans une variable nommée **$publicIPPrefixsource** pour une utilisation ultérieure.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$prips = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prips -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Créer une ressource de passerelle NAT

Cette section explique en détail comment vous pouvez créer et configurer les composants suivants du service NAT à l’aide de la ressource de passerelle NAT :
  - Un pool d’adresses IP publiques et un préfixe d’adresse IP publique à utiliser pour les flux sortants qui sont traduits par la ressource de passerelle NAT.
  - Passer la valeur par défaut du délai d’inactivité de 4 minutes à 10 minutes.

Créez une passerelle NAT Azure globale avec [New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). Le résultat de cette commande crée une ressource de passerelle nommée **myNATgateway** qui utilise l’adresse IP publique **myPublicIPsource** et le préfixe d’adresse IP publique **myPublicIPprefixsource**. Le délai d’inactivité est défini sur 10 minutes.  Le résultat de cette commande est stocké dans une variable nommée **$natGateway** pour une utilisation ultérieure.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$nnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $nnm -ResourceGroupName $rsg -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Location $loc -Sku $sku -IdleTimeoutInMinutes 10      
  ```

À ce stade, la passerelle NAT est fonctionnelle et il ne reste plus qu’à définir quels sous-réseaux d’un réseau virtuel doivent l’utiliser.

## <a name="prepare-the-source-for-outbound-traffic"></a>Préparer la source du trafic sortant

Nous allons vous guider tout au long de la configuration d’un environnement de test complet. Vous allez configurer un test à l’aide d’outils open source pour vérifier la passerelle NAT. Nous allons commencer par la source, qui utilisera la passerelle NAT que nous avons créée précédemment.

### <a name="configure-virtual-network-for-source"></a>Configurer un réseau virtuel pour la source

Créez le réseau virtuel et associez le sous-réseau à la passerelle.

À l’aide de la commande [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest), créez dans **myResourceGroupNAT** un réseau virtuel nommé **myVnetsource**, que vous dotez d’un sous-réseau nommé **mySubnetsource** à l’aide de [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest). L’espace d’adressage IP pour le réseau virtuel est **192.168.0.0/16**. Le sous-réseau au sein du réseau virtuel est **192.168.0.0/24**.  Le résultat des commandes est stocké dans des variables nommées **$subnetsource** et **$vnetsource** en vue d’une utilisation ultérieure.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$sbn = 'mySubnetsource'
$spfx = '192.168.0.0/24'
$vnm = 'myVnetsource'
$vpfx = '192.168.0.0/16'
$loc = 'eastus2'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $sbn -AddressPrefix $spfx -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnet
```

Tout le trafic sortant vers les destinations Internet utilise désormais le service NAT.  Il n’est pas nécessaire de configurer un UDR.

Avant de pouvoir tester la passerelle NAT, nous devons créer une machine virtuelle source.  Nous allons attribuer une ressource d’adresse IP publique en tant qu’adresse IP publique au niveau de l’instance pour accéder à cette machine virtuelle de l’extérieur. Cette adresse est uniquement utilisée pour y accéder dans le cadre du test.  Nous allons vous montrer comment le service NAT est prioritaire sur les autres options sortantes.

Vous pouvez également créer cette machine virtuelle sans adresse IP publique, et créer une autre machine virtuelle à utiliser comme jumpbox sans adresse IP publique dans le cadre d’un exercice.

### <a name="create-public-ip-for-source-vm"></a>Créer une adresse IP publique pour la machine virtuelle source

Nous créons une adresse IP publique à utiliser pour accéder à la machine virtuelle.  Utilisez la commande [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) pour créer une ressource d’adresse IP publique nommée **myPublicIPVM** dans **myResourceGroupNAT**.  Le résultat de cette commande est stocké dans une variable nommée **$publicIpsourceVM** pour une utilisation ultérieure.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pipvm = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvm -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Créer un groupe de sécurité réseau et exposer le point de terminaison SSH pour la machine virtuelle

Les adresses IP publiques standard étant « sécurisées par défaut », nous créons un groupe de sécurité réseau pour autoriser SSH à l’accès entrant. Le service NAT reconnaît la direction du flux. Ce groupe de sécurité réseau n’est pas utilisé pour le trafic sortant dès lors que la passerelle NAT est configurée sur le même sous-réseau. Utilisez [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) pour créer une ressource de groupe de sécurité réseau nommée **myNSGsource**. Utilisez [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) afin de créer pour l’accès SSH une règle de groupe de sécurité réseau nommée **ssh** dans **myResourceGroupNAT**. Le résultat de cette commande est stocké dans la variable nommée **$nsgsource** pour une utilisation ultérieure.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-source-vm"></a>Créer une carte réseau pour la machine virtuelle source

Créez une interface réseau nommée **myNicsource** à l’aide de la commande [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0). Cette commande associe l’adresse IP publique au groupe de sécurité réseau. Le résultat de cette commande est stocké dans une variable nommée **$nicsource** pour une utilisation ultérieure.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $loc
```

### <a name="create-a-source-vm"></a>Créer une machine virtuelle source

#### <a name="create-ssh-key-pair"></a>Créer la paire de clés SSH

Vous aurez besoin d’une paire de clés SSH pour suivre ce guide de démarrage rapide. Si vous disposez déjà d’une paire de clés SSH, vous pouvez ignorer cette étape.

Utilisez ssh-keygen pour créer une paire de clés SSH.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Pour plus d’informations sur la création de paires de clés SSH, notamment l’utilisation de PuTTy, voir [Guide pratique pour utiliser des clés SSH avec Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

Si vous créez la paire de clés SSH à l’aide de Cloud Shell, celle-ci est stockée dans une image conteneur. Ce [compte de stockage est créé automatiquement](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Ne supprimez pas le compte de stockage ni son partage de fichiers tant que vous n’avez pas récupéré vos clés.

#### <a name="create-vm-configuration"></a>Créer une configuration de machine virtuelle

Pour créer une machine virtuelle dans PowerShell, vous créez une configuration qui a des paramètres tels que l’image à utiliser, la taille et les options d’authentification. La configuration est ensuite utilisée pour générer la machine virtuelle.

Définissez les informations d’identification SSH, les informations du système d’exploitation et la taille de machine virtuelle. Dans cet exemple, la clé SSH est stockée dans ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combinez les définitions de configuration pour créer une machine virtuelle nommée **myVMsource** à l’aide de la commande [New-AzVM]((https://docs.microsoft.com/powershell/module/az.compute/new-azvm?view=azps-2.8.0)) dans **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigsource
```

Bien que la commande soit retournée immédiatement, le déploiement de la machine virtuelle peut prendre quelques minutes.

## <a name="prepare-destination-for-outbound-traffic"></a>Préparer la destination pour le trafic sortant

Nous allons maintenant créer une destination pour le trafic sortant qui est traduit par le service NAT, et ainsi vous permettre de le tester.

### <a name="configure-virtual-network-for-destination"></a>Configurer un réseau virtuel pour la destination

Nous devons créer un réseau virtuel sur lequel se trouvera la machine virtuelle de destination.  Ces commandes sont identiques à celles des étapes de la machine virtuelle source. Des modifications mineures ont été ajoutées pour exposer le point de terminaison de destination.

À l’aide de la commande [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest), créez dans **myResourceGroupNAT** un réseau virtuel nommé **myVnetdestination**, que vous dotez d’un sous-réseau nommé **mySubnetdestination** à l’aide de [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest). L’espace d’adressage IP pour le réseau virtuel est **192.168.0.0/16**. Le sous-réseau au sein du réseau virtuel est **192.168.0.0/24**.  Le résultat des commandes est stocké dans des variables nommées **$subnetdestination** et **$vnetdestination** en vue d’une utilisation ultérieure.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnetdestination
```

### <a name="create-public-ip-for-destination-vm"></a>Créer une adresse IP publique pour la machine virtuelle de destination

Nous créons une adresse IP publique à utiliser pour accéder à la machine virtuelle source.  Utilisez la commande [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) pour créer une ressource d’adresse IP publique nommée **myPublicIPdestinationVM** dans **myResourceGroupNAT**.  Le résultat de cette commande est stocké dans une variable nommée **$publicIpdestinationVM** pour une utilisation ultérieure.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rsg -AllocationMethod $all -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>Créer un groupe de sécurité réseau et exposer les points de terminaison HTTP et SSH pour la machine virtuelle

Les adresses IP publiques standard étant « sécurisées par défaut », nous créons un groupe de sécurité réseau pour autoriser SSH à l’accès entrant. Utilisez [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) pour créer une ressource de groupe de sécurité réseau nommée **myNSGdestination**. Utilisez [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) afin de créer une règle de groupe de sécurité réseau nommée **ssh** pour l’accès SSH.  Utilisez [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) afin de créer une règle de groupe de sécurité réseau nommée **http** pour l’accès HTTP. Les deux règles sont créées dans **myResourceGroupNAT**. Le résultat de cette commande est stocké dans une variable nommée **$nsgdestination** pour une utilisation ultérieure.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule,$httprule
```

### <a name="create-nic-for-destination-vm"></a>Créer une carte réseau pour la machine virtuelle de destination

Créez une interface réseau nommée **myNicdestination** à l’aide de la commande [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0). Cette commande associe l’adresse IP publique au groupe de sécurité réseau. Le résultat de cette commande est stocké dans une variable nommée **$nicdestination** pour une utilisation ultérieure.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $loc
```

### <a name="create-a-destination-vm"></a>Créer une machine virtuelle de destination

#### <a name="create-vm-configuration"></a>Créer une configuration de machine virtuelle

Pour créer une machine virtuelle dans PowerShell, vous créez une configuration qui a des paramètres tels que l’image à utiliser, la taille et les options d’authentification. La configuration est ensuite utilisée pour générer la machine virtuelle.

Définissez les informations d’identification SSH, les informations du système d’exploitation et la taille de machine virtuelle. Dans cet exemple, la clé SSH est stockée dans ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$vmd = 'myVMdestination'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combinez les définitions de configuration pour créer une machine virtuelle nommée **myVMdestination** à l’aide de la commande [New-AzVM]((https://docs.microsoft.com/powershell/module/az.compute/new-azvm?view=azps-2.8.0)) dans **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigdestination
```

Bien que la commande soit retournée immédiatement, le déploiement de la machine virtuelle peut prendre quelques minutes.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Préparer un serveur web et une charge utile de test sur la machine virtuelle de destination

Tout d’abord, nous avons besoin de découvrir l’adresse IP de la machine virtuelle de destination.  Pour obtenir l’adresse IP publique de la machine virtuelle, utilisez [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPdestinationVM'
  
Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Copiez l’adresse IP publique, puis collez-la dans un bloc-notes afin de pouvoir l’utiliser dans les étapes suivantes. Indiquez qu’il s’agit de la machine virtuelle de destination.

### <a name="sign-in-to-destination-vm"></a>Se connecter à la machine virtuelle de destination

Les informations d’identification SSH doivent être stockées dans votre instance Cloud Shell depuis l’opération précédente.  Ouvrez une session [Azure Cloud Shell](https://shell.azure.com) dans votre navigateur. Utilisez l’adresse IP récupérée à l’étape précédente pour établir une connexion SSH à la machine virtuelle. 

```bash
ssh azureuser@<ip-address-destination>
```

Copiez et collez les commandes suivantes une fois que vous êtes connecté.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Ces commandes mettent à jour votre machine virtuelle, installent Nginx et créent un fichier de 100 Ko. Ce fichier sera récupéré à partir de la machine virtuelle source par le biais du service NAT.

Fermez la session SSH avec la machine virtuelle de destination.

## <a name="prepare-test-on-source-vm"></a>Préparer le test sur la machine virtuelle source

Tout d’abord, nous avons besoin de découvrir l’adresse IP de la machine virtuelle source.  Pour obtenir l’adresse IP publique de la machine virtuelle, utilisez [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPsourceVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Copiez l’adresse IP publique, puis collez-la dans un bloc-notes afin de pouvoir l’utiliser dans les étapes suivantes. Indiquez qu’il s’agit de la machine virtuelle source.

### <a name="log-into-source-vm"></a>Se connecter à la machine virtuelle source

Là encore, les informations d’identification SSH sont stockées dans Cloud Shell. Ouvrez un nouvel onglet pour [Azure Cloud Shell](https://shell.azure.com) dans votre navigateur.  Utilisez l’adresse IP récupérée à l’étape précédente pour établir une connexion SSH à la machine virtuelle. 

```bash
ssh azureuser@<ip-address-source>
```

Copiez et collez les commandes suivantes pour préparer le test du service NAT.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Ces commandes mettent à jour votre machine virtuelle, installe Go, installe [hey](https://github.com/rakyll/hey) à partir de GitHub, et met à jour votre environnement d’interpréteur de commandes.

Vous êtes désormais prêt à tester le service NAT.

## <a name="validate-nat-service"></a>Valider le service NAT

Quand vous êtes connecté à la machine virtuelle source, vous pouvez utiliser **curl** et **hey** pour générer des requêtes vers l’adresse IP de destination.

Utilisez curl pour récupérer le fichier de 100 Ko.  Remplacez **\<ip-address-destination>** dans l’exemple ci-dessous par l’adresse IP de destination que vous avez copiée précédemment.  Le paramètre **--output** indique que le fichier récupéré sera ignoré.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Vous pouvez également générer une série de requêtes à l’aide de **hey**. Là encore, remplacez **\<ip-address-destination>** par l’adresse IP de destination que vous avez copiée précédemment.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Cette commande génère 100 requêtes, 10 simultanément, avec un délai d’expiration de 30 secondes. La connexion TCP ne sera pas réutilisée.  Chaque demande récupère 100 Ko.  À la fin de l’exécution, **hey** fournit quelques statistiques sur le fonctionnement du service NAT.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) pour supprimer le groupe de ressources et toutes les ressources qu’il contient.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez créé une passerelle NAT, créé une machine virtuelle source et une machine virtuelle de destination, puis testé la passerelle NAT.

Consultez les métriques dans Azure Monitor pour découvrir le fonctionnement de votre service NAT. Diagnostiquez des problèmes, tels que l’épuisement des ressources des ports SNAT disponibles.  L’épuisement des ressources de ports SNAT est résolu facilement en ajoutant des ressources supplémentaires, d’adresse IP publique ou de préfixe d’adresse IP publique, ou des deux à la fois.

- Apprenez-en davantage sur le service [Nat de Réseau virtuel](./nat-overview.md).
- Apprenez-en davantage sur la [ressource de passerelle NAT](./nat-gateway-resource.md).
- Guide de démarrage rapide du déploiement d’une [ressource de passerelle NAT avec Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Guide de démarrage rapide du déploiement d’une [ressource de passerelle NAT avec Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Guide de démarrage rapide du déploiement d’une [ressource de passerelle NAT avec le portail Azure](./quickstart-create-nat-gateway-portal.md).
- [Faites-nous part de vos commentaires sur la préversion publique](https://aka.ms/natfeedback).

> [!div class="nextstepaction"]

