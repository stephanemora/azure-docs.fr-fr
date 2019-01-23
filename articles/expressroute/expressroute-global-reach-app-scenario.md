---
title: Scénario d’application ExpressRoute Global Reach | Microsoft Docs
description: Cette page fournit un scénario d’application pour Global Reach.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,global reach
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/14/2019
ms.author: rambala
ms.openlocfilehash: 5174baae40d7f14720affe90f702c024541a5745
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54333041"
---
# <a name="expressroute-global-reach-application-scenario"></a>Scénario d’application ExpressRoute Global Reach

Pour en savoir plus sur ExpressRoute Global Reach, consultez [ExpressRoute Global Reach][Global Reach]. Dans cet article, nous allons suivre un scénario d’application, comparer la solution ExpressRoute Global Reach à quelques autres solutions, configurer Global Reach pour l’exemple de scénario et vérifier les connexions. 

##<a name="application-scenario"></a>Scénario d’application

Fabrikam Inc. a une présence physique et un déploiement Azure importants dans la région USA Est. Fabrikam a une connectivité back-end entre ses déploiements locaux et Azure par le biais d’ExpressRoute. Contoso Ltd. a une présence et un déploiement Azure dans la région USA Ouest. Contoso a une connectivité back-end entre ses déploiements locaux et Azure par le biais d’ExpressRoute.  

Fabrikam Inc. acquiert Contoso Ltd. Suite à la fusion, Fabrikam souhaite interconnecter les réseaux. La figure suivante illustre le scénario :

 [![1]][1]

Les flèches en pointillés au milieu de la figure ci-dessus indiquent les interconnexions réseau nécessaires. Le tableau suivant montre la table de routage de l’appairage privé principal de l’ExpressRoute de Contoso Ltd. avant la fusion.

[![2]][2]

Le tableau suivant montre la table de routage de l’appairage privé principal de l’ExpressRoute de Fabrikam Inc. avant la fusion.

[![3]][3]

## <a name="traditional-solutions"></a>Solutions traditionnelles

### <a name="option-1-interconnect-on-premises-networks"></a>Option 1 : Interconnexion des réseaux locaux

La figure suivante illustre cette option. Comme illustré, vous pouvez interconnecter les deux réseaux locaux à l’aide d’un VPN de site à site ou d’autres options de connexion haut débit, et gérer tout le routage entre les deux entités. 

[![4]][4]

Cette option présente les inconvénients suivants :

- Vous forcez la communication interrégionale Azure pour le déploiement sur une route non optimale.
- Vous empêchez de tirer profit de l’avantage offert par la haute disponibilité du réseau principal Microsoft pour la communication interrégionale.
- Vous assumez l’entière responsabilité du routage pour la communication interrégionale.

### <a name="option-2-expressroute-cross-connectivity-and-interconnect-on-premises-networks"></a>Option 2 : Interconnectivité ExpressRoute et interconnexion des réseaux locaux

La figure suivante illustre cette option.

[![5]][5]

Comme illustré dans la figure ci-dessus, vous pouvez en plus établir une connectivité entre les circuits ExpressRoute et les réseaux virtuels interrégionaux. La connectivité interrégionale entre le réseau virtuel et les circuits ExpressRoute autoriserait les communications suivantes :

- Les réseaux virtuels USA Ouest/USA Est pourraient communiquer respectivement avec les réseaux locaux Fabrikam/Contoso.
- Le réseau virtuel USA Ouest pourrait communiquer avec le réseau virtuel USA Est.

L’interconnexion entre les deux réseaux locaux est toujours nécessaire pour que ceux-ci puissent communiquer entre eux.

Cette option permet à la communication interrégionale Azure pour le déploiement privé de passer par le réseau principal Microsoft, et à la communication entre les réseaux locaux de passer par le réseau externe. Toutefois, le principal inconvénient de cette solution est que vous devez établir plusieurs connexions interrégionales, ce qui compliquent la maintenance et la résolution des problèmes. De plus, cette option ne vous permet pas de tirer parti de la haute disponibilité du réseau principal mondial de Microsoft pour la communication entre les deux réseaux locaux.

### <a name="option-3-vnet-peering-and-interconnect-on-premises-networks"></a>Option 3 : VNET Peering et interconnexion des réseaux locaux

La figure suivante illustre cette option. L’option utilise VNET Peering pour la communication interrégionale entre les réseaux virtuels. Cette option, telle qu’illustrée, est incomplète car elle ne gère pas la communication interrégionale entre les réseaux virtuels et les réseaux locaux (indiquée par les deux flèches en pointillé au milieu). Utilisez une connexion de réseau local à réseau local (comme dans l’Option 1) ou une interconnectivité ExpressRoute (comme dans l’Option 2) pour la communication interrégionale à locale.

[![6]][6]

Cette option fournit un routage optimal pour la communication inter-régionale entre les réseaux virtuels. Toutefois, elle s’avère insuffisante si Fabrikam ou Contoso a un déploiement Azure plus complexe tel qu’un modèle de réseau virtuel hub-and-spoke. De plus, comme les deux options précédentes, cette option ne vous permet pas de tirer parti de la haute disponibilité du réseau principal mondial de Microsoft pour la communication entre les deux réseaux locaux.

## <a name="global-reach"></a>Global Reach

ExpressRoute Global Reach relie l’appairage privé des circuits ExpressRoute, comme illustré dans la figure suivante :

[![7]][7]

La configuration de Global Reach entre les deux circuits ExpressRoute autorise une communication privée entre les deux réseaux locaux et le déploiement Azure dans les deux régions. Ainsi, Global Reach offre toutes les communications requises (indiquées par la ligne en pointillés dans la première figure de cet article) sur le réseau principal Microsoft.

### <a name="configure-global-reach"></a>Configurer Global Reach

Pour découvrir comment configurer Global Reach ExpressRoute, consultez [Configurer Global Reach][Configure Global Reach]. 

Fabrikam Inc. et Contoso Ltd. ayant intégré Azure en tant que sociétés distinctes, les circuits ExpressRoute des deux sociétés sont dans deux abonnements Azure différents. Pour créer la connexion Global Reach entre les abonnements, vous devez créer une autorisation au niveau du circuit ExpressRoute appartenant à Contoso Ltd. et la passer au circuit ExpressRoute Fabrikam, Inc.


Pour créer une autorisation pour le circuit ExpressRoute de Contoso, commencez par vous connecter au compte Azure de Contoso, puis sélectionnez l’abonnement approprié (s’il y a plusieurs abonnements). Les commandes PowerShell pour ces étapes sont les suivantes :

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```
Les étapes de création d’une autorisation de circuit ExpressRoute sont présentées ci-dessous :

```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```

La sortie de « Set-AzureRmExpressRouteCircuit » liste l’ExpressRouteCircuit. Notez l’ID d’appairage privé et la clé d’autorisation figurant vers la fin de la liste. Voici un exemple d’extrait de sortie PowerShell :

```powershell
ServiceKey                       : <serviceKey>
Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 65020,
                                       "PrimaryPeerAddressPrefix": "192.168.11.16/30",
                                       "SecondaryPeerAddressPrefix": "192.168.11.20/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 110,
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "4",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
Authorizations                   : [
                                     {
                                       "Name": "Auth4-ASH-Cust11",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/authorizations/Auth4-ASH-Cust11",
                                       "AuthorizationKey": "<authorizationKey>",
                                       "AuthorizationUseStatus": "Used",
                                       "ProvisioningState": "Succeeded"
                                     }
                                   ]
AllowClassicOperations           : False
GatewayManagerEtag               : 4
```

Avec l’ID d’appairage et la clé d’autorisation, vous pouvez créer la connexion Global Reach sous le circuit ExpressRoute de Fabrikam. Connectez-vous au compte Azure de Fabrikam. S’il existe plusieurs abonnements, sélectionnez celui qui convient.

Global Reach crée un ensemble redondant de connexions point à point entre les deux paires MSEE. Pour les deux connexions point à point, vous devez spécifier un préfixe d’adresse /29 (dans notre exemple, nous utilisons 192.168.11.64/29). Utilisez les commandes suivantes pour créer la connexion Global Reach :

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix "__.__.__.__/29" -AuthorizationKey "########-####-####-####-############"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Une fois les commandes ci-dessus exécutées, la création des connexions Global Reach redondantes prendra quelques minutes.

### <a name="verify-global-reach"></a>Vérifier Global Reach

Le tableau suivant montre la table de routage de l’appairage privé principal de l’ExpressRoute de Contoso Ltd. après la configuration de Global Reach.

[![8]][8]

Le tableau suivant montre la table de routage de l’appairage privé principal de l’ExpressRoute de Fabrikam Inc. après la configuration de Global Reach.

[![9]][9]

Dans les tableaux ci-dessus, nous voyons tous les préfixes réseau de destination attendus, et les tronçons suivants appropriés sont listés.

Au-dessus figure le panneau « Obtenir la table de routage  » qui est accessible dans le portail Azure sous « Appairage privé » d’un circuit ExpressRoute. Vous pouvez également lister une table de routage ExpressRoute à l’aide de la commande PowerShell suivante :

```powershell
Get-AzExpressRouteCircuitRouteTable -DevicePath 'primary' -ExpressRouteCircuitName "Your_circuit_name" -PeeringType AzurePrivatePeering -ResourceGroupName "Your_resource_group"
```

Pour afficher la table de routage du MSEE secondaire, remplacez « primary » par le mot clé « secondary » dans la commande ci-dessus.

Nous allons également vérifier la connectivité de plan de données en exécutant des tests ping sur différentes destinations à partir de différents réseaux. Les trois tests ping suivants vérifient la connectivité de plan de données au réseau local Contoso, au réseau virtuel Azure de Contoso et au réseau virtuel Azure de Fabrikam à partir du réseau local de Fabrikam.


    C:\Users\FabrikamUser>ping 10.1.11.10
    
    Pinging 10.1.11.10 with 32 bytes of data:
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    
    Ping statistics for 10.1.11.10:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 69ms, Maximum = 69ms, Average = 69ms
    
    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=82ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=81ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 82ms, Average = 78ms
    
    C:\Users\FabrikamUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=2ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 2ms, Maximum = 3ms, Average = 2ms


Les deux tests ping suivants vérifient la connectivité de plan de données au réseau virtuel Azure de Contoso et au réseau virtuel Azure de Fabrikam à partir du réseau local de Contoso.

    C:\Users\ContosoUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=6ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 5ms, Maximum = 6ms, Average = 5ms
    
    C:\Users\ContosoUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=74ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 73ms, Maximum = 74ms, Average = 73ms

Le test ping suivant vérifie la connectivité de plan de données au réseau virtuel Azure de Contoso à partir du réseau virtuel Azure de Fabrikam.

    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=78ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 78ms, Average = 77ms

## <a name="optimization-for-latency-sensitive-traffic"></a>Optimisation pour le trafic sensible à la latence

Global Reach route le trafic par le biais d’appareils Microsoft Edge. Pour le scénario spécifique présenté dans cet article, vous pouvez obtenir un routage optimal entre les deux réseaux virtuels en activant VNet Peering entre eux. De même, vous pouvez obtenir un routage optimal entre les deux réseaux locaux par le biais d’un fournisseur de services, susceptible de fournir une connexion de réseau étendu plus directe entre les sites. Dans de tels scénarios, vous pouvez utiliser le routage Global Reach comme option de restauration automatique pour ces connexions. 

## <a name="next-steps"></a>Étapes suivantes

Global Reach est déployé pays par pays. Pour vérifier si Global Reach est disponible dans les pays qui vous intéressent, consultez [ExpressRoute Global Reach][Global Reach].

<!--Image References-->
[1]: ./media/expressroute-globalreach-applicationscenario/PreMergerScenario.png "Scénario d’application"
[2]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Premerger.png "Table de routage ExpressRoute de Contoso avant la fusion"
[3]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Premerger.png "Table de routage ExpressRoute de Fabrikam avant la fusion"
[4]: ./media/expressroute-globalreach-applicationscenario/S2SVPN-Solution.png "Interconnexion des réseaux locaux"
[5]: ./media/expressroute-globalreach-applicationscenario/ExR-XConnect-Solution.png "Interconnexion ExpressRoute"
[6]: ./media/expressroute-globalreach-applicationscenario/VNet-peering-Solution.png "VNet Peering"
[7]: ./media/expressroute-globalreach-applicationscenario/GlobalReach-Solution.png "Global Reach"
[8]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Postmerger.png "Table de routage ExpressRoute de Contoso avec Global Reach"
[9]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Postmerger.png "Table de routage ExpressRoute de Fabrikam avec Global Reach"

<!--Link References-->
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach





