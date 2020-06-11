---
title: Créer une instance Azure Private Endpoint à l’aide de PowerShell | Microsoft Docs
description: En savoir plus sur Azure Private Link
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 83207c70b147e4f0d416f47a6b12f9826f49f2db
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267764"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Créer une instance Private Endpoint à l’aide d’Azure PowerShell
Private Endpoint est le composant fondamental de Private Link dans Azure. Il permet à des ressources Azure, comme des machines virtuelles, de communiquer en privé avec des ressources Private Link. 

Dans ce guide de démarrage rapide, vous allez découvrir comment créer une machine virtuelle sur un réseau virtuel Azure et un serveur SQL logique avec un point de terminaison privé Azure à l’aide d’Azure PowerShell. Ensuite, vous pourrez accéder de façon sécurisée à SQL Database à partir de la machine virtuelle.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Avant de pouvoir créer vos ressources, vous devez créer un groupe de ressources qui héberge le réseau virtuel et Private Endpoint avec [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *WestUS* :

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Création d'un réseau virtuel
Dans cette section, vous créez un réseau virtuel et un sous-réseau. Ensuite, associez le sous-réseau à votre réseau virtuel.

### <a name="create-a-virtual-network"></a>Création d'un réseau virtuel

Créez un réseau virtuel pour votre Private Endpoint avec [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). L’exemple suivant crée un réseau virtuel nommé *MyVirtualNetwork* :
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Ajouter un sous-réseau

Azure déploie des ressources dans un sous-réseau au sein d’un réseau virtuel. C’est pourquoi vous devez créer un sous-réseau. Créez une configuration de sous-réseau nommée *mySubnet* avec [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). L’exemple suivant crée un sous-réseau nommé *mySubnet* avec l’indicateur de stratégie réseau Private Endpoint défini sur **Désactivé**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

> [!CAUTION]
> Il est facile de confondre le paramètre `PrivateEndpointNetworkPoliciesFlag` avec un autre indicateur disponible, `PrivateLinkServiceNetworkPoliciesFlag`, car il s’agit de mots longs et d’une apparence similaire.  Vérifiez que vous utilisez la bonne valeur, `PrivateEndpointNetworkPoliciesFlag`.

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associer le sous-réseau au réseau virtuel

Vous pouvez écrire la configuration du sous-réseau dans le réseau virtuel avec [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Cette commande crée le sous-réseau :

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Créez une machine virtuelle dans le réseau virtuel avec [New-AzVM](/powershell/module/az.compute/new-azvm). Lorsque vous exécutez les commandes suivantes, vous êtes invité à fournir des informations d’identification. Entrez un nom d’utilisateur et un mot de passe pour la machine virtuelle :

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "westcentralus" `
    -VirtualNetworkName "MyVirtualNetwork" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389 `
    -AsJob  
```

L’option `-AsJob` permet de créer la machine virtuelle en arrière-plan. Vous pouvez passer à l’étape suivante.

Lorsqu’Azure commence la création de la machine virtuelle en arrière-plan, vous obtenez quelque chose de similaire à :

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-logical-sql-server"></a>Créer un serveur SQL logique 

Créez un serveur SQL logique avec la commande New-AzSqlServer. N’oubliez pas que le nom de votre serveur doit être unique dans Azure. Vous devez donc remplacer la valeur d’espace réservé entre crochets par votre propre valeur unique :

```azurepowershell-interactive
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"

$server = New-AzSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -Location "WestCentralUS" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

New-AzSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"`
    -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" `
    -SampleName "AdventureWorksLT"
```

## <a name="create-a-private-endpoint"></a>Créer un point de terminaison privé

Point de terminaison privé pour le serveur dans votre réseau virtuel avec [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection) : 

```azurepowershell

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" `
  -PrivateLinkServiceId $server.ResourceId `
  -GroupId "sqlServer" 
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  "myResourceGroup" -Name "MyVirtualNetwork"  
 
$subnet = $virtualNetwork `
  | Select -ExpandProperty subnets `
  | Where-Object  {$_.Name -eq 'mysubnet'}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName "myResourceGroup" `
  -Name "myPrivateEndpoint" `
  -Location "westcentralus" `
  -Subnet  $subnet `
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>Configurer la zone DNS privée 
Créez une zone DNS privée pour le domaine SQL Database, créez un lien d’association avec le réseau virtuel et créez un groupe de zones DNS pour associer le point de terminaison privé à la zone DNS privée.

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  

$config = New-AzPrivateDnsZoneConfig -Name "privatelink.database.windows.net" -PrivateDnsZoneId $zone.ResourceId

$privateDnsZoneGroup = New-AzPrivateDnsZoneGroup -ResourceGroupName "myResourceGroup" `
 -PrivateEndpointName "myPrivateEndpoint" -name "MyZoneGroup" -PrivateDnsZoneConfig $config
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>Se connecter à une machine virtuelle à partir d’Internet

Utilisez [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) pour retourner l’adresse IP publique d’une machine virtuelle. Cet exemple retourne l’adresse IP publique de la machine virtuelle *myVM* :

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Ouvrez une invite de commandes sur votre ordinateur local. Exécutez la commande mstsc. Remplacez <publicIpAddress> par l’adresse IP publique renvoyée à l’étape précédente : 


> [!NOTE]
> Si vous avez exécuté ces commandes à partir d’une invite PowerShell sur votre ordinateur local et que vous utilisez le module Az PowerShell version 1.0 ou une version ultérieure, vous pouvez continuer sur cette interface.
```
mstsc /v:<publicIpAddress>
```

1. Si vous y êtes invité, sélectionnez **Connexion**. 
2. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle.
  > [!NOTE]
  > Vous devrez peut-être sélectionner Plus de choix > Utiliser un autre compte pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle. 
  
3. Sélectionnez **OK**. 
4. Vous allez peut-être recevoir un avertissement de certificat. Si vous en recevez un, sélectionnez **Oui** ou **Continuer**. 

## <a name="access-sql-database-privately-from-the-vm"></a>Accéder à SQL Database en privé à partir de la machine virtuelle

1. Dans le Bureau à distance de myVM, ouvrez PowerShell.
2. Entrez `nslookup myserver.database.windows.net`. N’oubliez pas de remplacer `myserver` par le nom de votre serveur SQL.

    Vous recevez un message similaire à celui ci :
    
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
    
3. Installez [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15).
4. Dans **Se connecter au serveur**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | --- | --- |
    | Type de serveur | Moteur de base de données |
    | Nom du serveur | myserver.database.windows.net |
    | Nom d’utilisateur | Entrez le nom d’utilisateur fourni lors de la création. |
    | Mot de passe | Entrez le mot de passe fourni lors de la création. |
    | Se souvenir du mot de passe | Oui |
    
5. Sélectionnez **Connecter**.
6. Parcourez **Bases de données** dans le menu de gauche. 
7. (Facultatif) Créez ou interrogez des données dans mydatabase.
8. Fermez la connexion Bureau à distance à *myVM*. 

## <a name="clean-up-resources"></a>Nettoyer les ressources 
Lorsque vous avez terminé d’utiliser le point de terminaison privé, SQL Database et la machine virtuelle, utilisez [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources et toutes les ressources qu’il contient :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [Azure Private Link](private-link-overview.md)
