---
title: Connectivité configurée de réseau virtuel à SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Connectivité configurée à partir du réseau virtuel pour utiliser SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9cd290f9e5e7819f3dffa2dd1efea9cd0028fc2c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239466"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Connecter un réseau virtuel à de grandes instances HANA

Après avoir créé un réseau virtuel Azure, vous pouvez connecter ce réseau à SAP HANA sur de grandes instances Azure. Créez une passerelle Azure ExpressRoute sur le réseau virtuel. Cette passerelle vous permet de lier le réseau virtuel au circuit ExpressRoute qui se connecte au locataire du client sur le tampon de grande Instance HANA.

> [!NOTE] 
> Cette étape peut prendre jusqu’à 30 minutes. La nouvelle passerelle est créée dans l’abonnement Azure désigné, puis connectée au réseau virtuel Azure spécifié.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

S’il existe déjà une passerelle, vérifiez qu’il s’agit d’une passerelle ExpressRoute. Si elle n’est pas une passerelle ExpressRoute, supprimez la passerelle et recréer comme une passerelle ExpressRoute. Si une passerelle ExpressRoute est déjà établie, consultez la section « Lier des réseaux virtuels » de cet article. 

- Utilisez soit le [portail Azure](https://portal.azure.com/), soit PowerShell pour créer une passerelle VPN ExpressRoute connectée à votre réseau virtuel.
  - Si vous utilisez le portail Azure, ajoutez une nouvelle **passerelle de réseau virtuel**, puis sélectionnez **ExpressRoute** comme type de passerelle.
  - Si vous utilisez PowerShell, commencez par télécharger et par utiliser le dernier [SDK Azure PowerShell](https://azure.microsoft.com/downloads/). 
 
Les commandes suivantes créent une passerelle ExpressRoute. Le texte précédé par un _$_ sont des variables définies par l’utilisateur qui doivent être mis à jour avec vos propres informations.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Dans cet exemple, la référence SKU de passerelle HighPerformance a été utilisée. Vous pouvez opter pour HighPerformance ou pour UltraPerformance, les seules références SKU de passerelle prises en charge pour SAP HANA sur Azure (grandes instances).

> [!IMPORTANT]
> Pour les grandes instances HANA de classe Type II, vous devez utiliser la référence SKU de la passerelle UltraPerformance.

## <a name="link-virtual-networks"></a>Lier des réseaux virtuels

Le réseau virtuel Azure a maintenant une passerelle ExpressRoute. Utilisez les informations d’autorisation fournies par Microsoft pour connecter la passerelle ExpressRoute au circuit ExpressRoute SAP HANA grandes Instances. Vous pouvez vous connecter à l’aide du portail Azure ou de PowerShell. Les instructions PowerShell sont les suivantes. 

Exécutez les commandes suivantes pour chaque passerelle ExpressRoute à l’aide d’un paramètre AuthGUID différent pour chaque connexion. Les deux premières entrées dans le script suivant proviennent des informations fournies par Microsoft. En outre, AuthGUID est spécifique à chaque réseau virtuel et à sa passerelle. Si vous souhaitez ajouter un autre réseau virtuel Azure, vous devez obtenir un autre AuthID pour votre circuit ExpressRoute qui connecte les grandes instances HANA dans Azure à partir de Microsoft. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> Le dernier paramètre dans la commande New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** est un nouveau paramètre qui permet d’ExpressRoute rapide chemin d’accès. Une fonctionnalité qui réduit la latence réseau entre vos machines virtuelles Azure et les unités de grande Instance HANA. La fonctionnalité a été ajoutée dans mai 2019. Pour plus d’informations, consultez l’article [architecture de réseau SAP HANA (grandes Instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Assurez-vous que vous exécutez la dernière version des applets de commande PowerShell avant d’exécuter les commandes.

Pour connecter la passerelle à plusieurs circuits ExpressRoute associés à votre abonnement, vous devrez peut-être exécuter plusieurs fois cette étape. Par exemple, vous allez probablement connecter la même passerelle de réseau virtuel au circuit ExpressRoute qui relie le réseau virtuel à votre réseau local.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Application ExpressRoute rapide chemin d’accès à des circuits de grande Instance HANA ExpressRoute existants
La documentation jusqu'à présent, vous avez appris comment connecter un nouveau circuit ExpressRoute qui a été créé avec un déploiement de grande Instance HANA à une passerelle Azure ExpressRoute de l’un de vos réseaux virtuels Azure. Mais de nombreux clients ont déjà leur configuration de circuits ExpressRoute déjà et ont leurs réseaux virtuels aux grandes Instances HANA déjà connecté. Comme le nouveau ExpressRoute rapidement tracé est ce qui réduit la latence du réseau, il est recommandé que vous appliquez la modification pour utiliser cette fonctionnalité. Les commandes pour vous connecter à un nouveau circuit ExpreesRoute et pour modifier un ExpressRoute Circuit existant sont les mêmes. Par conséquent, vous devez exécuter cette séquence de commandes PowerShell pour modifier un circuit existant à utiliser 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

Il est important que vous ajoutiez le dernier paramètre tel qu’affiché ci-dessus pour activer la fonctionnalité ExpressRoute rapide chemin d’accès


## <a name="expressroute-global-reach"></a>Portée mondiale ExpressRoute
Que vous souhaitez activer la portée mondiale pour moins le des deux scénarios :

 - Réplication de système HANA sans autres proxys ou pare-feu
 - Copie des sauvegardes entre les unités de grande Instance HANA dans deux régions différentes pour effectuer des copies du système ou actualisation du système

Vous devez prendre en compte que :

- Vous devez fournir une plage d’espace d’adresse de/29 espace d’adressage. Que plage d’adresses peut se chevauchent pas avec les autres plages d’adresses d’espace que vous avez utilisé la connexion jusqu'à présent les grandes Instances HANA vers Azure et ne peuvent pas se chevaucher avec une de vos plages d’adresses IP vous utilisé ailleurs dans Azure ou sur site.
- Il existe une limitation sur les ASN (numéro de système autonome) qui peut être utilisé pour publier vos itinéraires locaux aux grandes Instances HANA. Votre réseau local ne doit pas publier tous les itinéraires avec ASN privé dans la plage de 65000 – 65020 ou 65515. 
- Pour le scénario de connexion en local un accès direct aux grandes instances HANA, vous devez calculer un tarif pour le circuit qui vous connecte à Azure. Pour connaître les prix, vérifier les prix pour [Global de module complémentaire atteindre](https://azure.microsoft.com/pricing/details/expressroute/).

Pour obtenir une ou les deux scénarios appliquées à votre déploiement, ouvrez un message de la prise en charge avec Azure comme décrit dans [ouvrir une demande de support pour les grandes Instances HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Données nécessaires et les mots clés que vous devez utiliser pour Microsoft pour pouvoir acheminer et d’exécution sur votre demande, l’aspect suivant :

- Service : Grande instance SAP HANA
- Type de problème : Configuration et installation
- Sous-type du problème : Mon problème n’est pas répertorié ci-dessus
- L’objet « Modifier mon réseau - ajouter portée mondiale »
- Détails : « Ajouter portée mondiale à grande Instance HANA au locataire de grande Instance HANA ou « ajouter Global atteindre en local au locataire de grande Instance HANA.
- Obtenir des détails supplémentaires pour la grande Instance HANA au cas de locataire de grande Instance HANA : Vous devez définir le **deux régions Azure** où se trouvent les deux clients pour se connecter **AND** vous devez envoyer le   **/29 plage d’adresses IP**
- Détails supplémentaires sur l’en local au cas de locataire de grande Instance HANA : Vous devez définir le **région Azure** où le locataire de grande Instance HANA est déployé vous souhaitez vous connecter directement. En outre, vous devez fournir le **Auth GUID** et **Circuit ID de l’homologue** que vous avez reçu lorsque vous avez établi votre circuit ExpressRoute entre en local et Azure. En outre, vous devez nommer votre **ASN**. La dernière est remis une **/29 plage d’adresses IP** pour ExpressRoute de portée mondiale.

> [!NOTE]
> Si vous souhaitez avoir les deux cas gérés, vous avez besoin fournir deux différents/29 plages d’adresses qui ne se chevauchent pas avec toutes les adresses IP adresse plage utilisée jusqu'à présent. 




## <a name="next-steps"></a>Étapes suivantes

- [Configuration réseau requise supplémentaire pour HLI](hana-additional-network-requirements.md)
