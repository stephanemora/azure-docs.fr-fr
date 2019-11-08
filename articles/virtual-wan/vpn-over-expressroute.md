---
title: Créer une connexion VPN de site à site par peering privé ExpressRoute dans Azure Virtual WAN | Microsoft Docs
description: Dans ce tutoriel, découvrez comment utiliser Azure Virtual WAN pour créer une connexion VPN de site à site par peering privé ExpressRoute.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 10/11/2019
ms.author: cherylmc
Customer intent: I want to connect my on-premises networks to my VNets using S2S VPN connection over my ExpressRoute private peering using Azure Virtual WAN.
ms.openlocfilehash: 6272d6fe6f8c35c06a8121e10be2dd5a2e5512a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510958"
---
# <a name="create-a-site-to-site-vpn-connection-over-expressroute-private-peering-using-azure-virtual-wan"></a>Créer une connexion VPN de site à site par peering privé ExpressRoute avec Azure Virtual WAN

Cet article explique comment utiliser Virtual WAN pour établir une connexion VPN IPsec/IKE d’un réseau local vers Azure par peering privé d’un circuit ExpressRoute. Cette technique peut assurer un transit chiffré entre les réseaux locaux et les réseaux virtuels Azure par ExpressRoute, sans passer par l’Internet public ou utiliser des adresses IP publiques.

## <a name="topology-and-routing"></a>Topologie et routage

Le diagramme suivant illustre un exemple de connexion VPN par peering privé ExpressRoute :

![VPN par ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

Le diagramme illustre un réseau du réseau local connecté à la passerelle VPN du hub Azure par peering privé ExpressRoute. L’établissement de la connectivité est simple :

1. Établir une connectivité ExpressRoute avec un circuit ExpressRoute et le peering privé
2. Établir la connectivité VPN comme l’explique ce document

L’un des aspects importants de cette configuration est le routage entre les réseaux locaux et Azure sur le chemin VPN et le chemin ExpressRoute.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Trafic de réseaux locaux vers Azure

Pour le trafic de réseaux locaux vers Azure, les préfixes Azure (y compris le hub virtuel et tous les réseaux virtuels spoke qui y sont connectés) sont publiés via le protocole BGP de peering privé ExpressRoute et le protocole BGP de VPN, ce qui donne deux itinéraires (chemins) réseau des réseaux locaux vers Azure : l’un par le chemin protégé par IPsec, l’autre directement par ExpressRoute **sans** protection IPsec. Pour que le chiffrement s’applique bien à la communication, les itinéraires Azure qui passent par la passerelle VPN locale doivent être préférés au chemin ExpressRoute direct pour le réseau connecté par VPN dans le diagramme.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Trafic d’Azure vers des réseaux locaux

La même condition s’applique au trafic d’Azure vers les réseaux locaux. Il existe deux moyens de faire en sorte que le chemin IPsec soit préféré au chemin ExpressRoute direct (sans IPsec) :

- Publiez des préfixes plus spécifiques sur la session BGP VPN pour le réseau connecté par VPN : une grande plage englobant le « réseau connecté par VPN » par peering privé ExpressRoute, puis des plages plus spécifiques dans la session BGP VPN. Par exemple, publiez 10.0.0.0/16 par ExpressRoute et 10.0.1.0/24 par VPN.

- Publiez des préfixes disjoints pour VPN et ExpressRoute. Si les plages réseau par VPN sont disjointes d’un autre réseau connecté par ExpressRoute, vous pouvez publier les préfixes respectivement dans les sessions VPN et BGP ExpressRoute. Par exemple, publiez 10.0.0.0/24 par ExpressRoute et 10.0.1.0/24 par VPN.

Dans ces deux exemples, Azure envoie le trafic à 10.0.1.0/24 via la connexion VPN, plutôt que directement par ExpressRoute sans protection VPN.

> [!WARNING]
> Si vous publiez les **mêmes** préfixes sur les connexions ExpressRoute et VPN, Azure **utilisera directement le chemin ExpressRoute sans protection VPN**.
>

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. Créer un réseau Virtual WAN et un hub avec des passerelles

Avant toute chose, les ressources Azure suivantes et les configurations locales correspondantes doivent être en place :

1. Un réseau Azure Virtual WAN
2. Un hub Virtual WAN avec une passerelle [ExpressRoute](virtual-wan-expressroute-portal.md) et une [passerelle VPN](virtual-wan-site-to-site-portal.md)

Pour savoir comment créer un réseau Azure Virtual WAN et un hub avec une association ExpressRoute, voir [Créer une association ExpressRoute avec Azure Virtual WAN](virtual-wan-expressroute-portal.md) ; pour savoir comment créer une passerelle VPN dans le réseau Virtual WAN, voir [Créer une connexion de site à site avec Azure Virtual WAN](virtual-wan-site-to-site-portal.md).

## <a name="site"></a>2. Créer un site pour le réseau local

La ressource de site est la même que les sites VPN non ExpressRoute pour Virtual WAN. Il est important de noter que l’adresse IP du périphérique VPN local peut désormais être une adresse IP privée ou une adresse IP publique dans le réseau local accessible par le peering privé ExpressRoute créée à l’étape 1.

> [!NOTE]
> L’adresse IP du périphérique VPN local DOIT faire partie des préfixes d’adresse publiés sur le hub Virtual WAN par peering privé Azure ExpressRoute.
>

1. Accédez au Portail Azure dans votre navigateur. Cliquez sur le WAN que vous avez créé. Sur la page WAN, sous **Connectivité**, cliquez sur **Sites VPN** pour ouvrir la page des sites VPN.

2. Sur la page **Sites VPN**, cliquez sur **+Créer un site**.

3. Sur la page **Créer un site**, renseignez les champs suivants :

   * **Abonnement** : vérifiez l’abonnement.
   * **Groupe de ressources** : sélectionnez ou créez le groupe de ressources que vous souhaitez utiliser.
   * **Région** : région Azure de la ressource de site VPN.
   * **Nom** : nom par lequel vous souhaitez faire référence à votre site local.
   * **Fournisseur du périphérique** : fournisseur du périphérique VPN local.
   * **Border Gateway Protocol** : sélectionnez « Activer » si votre réseau local utilise le protocole BGP.
   * **Espace d’adressage privé** : il s’agit de l’espace d’adresse IP situé sur votre site local. Le trafic destiné à cet espace d’adressage est routé vers le réseau local via la passerelle VPN.
   * **Hubs** : sélectionnez un ou plusieurs hubs pour connecter ce site VPN. Les hubs sélectionnés doivent disposer déjà de passerelles VPN.

4. Cliquez sur **Suivant : Liens >** pour les paramètres de liaison VPN :

   * **Nom de la liaison** : nom par lequel vous souhaitez faire référence à cette connexion.
   * **Nom du fournisseur** : nom du fournisseur de services Internet de ce site. Dans le cas d’un réseau local ExpressRoute, nom du fournisseur de services ExpressRoute.
   * **Vitesse** : vitesse de la liaison du service Internet ou du circuit ExpressRoute.
   * **Adresse IP** : adresse IP publique du périphérique VPN qui se trouve sur le site local. Dans le cas d’ExpressRoute en local, adresse IP privée du périphérique VPN par ExpressRoute.

   Si le protocole BGP est activé, il s’applique à toutes les connexions créées pour ce site dans Azure. La configuration du protocole BGP sur un réseau Virtual WAN équivaut à configurer le protocole BGP sur une passerelle VPN Azure. L’adresse du peer BGP local *ne doit pas* être identique à l’adresse IP publique du réseau VPN vers l’appareil ou à l’espace d’adressage du réseau virtuel du site VPN. Utilisez une adresse IP différente sur le périphérique VPN de votre adresse IP BGP homologue. Il peut s’agir d’une adresse affectée à l’interface de bouclage sur le périphérique. Toutefois, il *ne peut pas* s’agir d’une adresse APIPA (169.254.*x*.*x*). Spécifiez cette adresse sur la passerelle de réseau local correspondante, représentant l’emplacement. Pour les conditions préalables BGP, consultez [À propos de BGP avec la passerelle VPN Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).

5. Cliquez sur **Suivant : Vérifier + créer >** pour vérifier les valeurs des paramètres et créer le site VPN. Si vous avez sélectionné **Hubs** pour vous connecter, la connexion sera établie entre le réseau local et la passerelle VPN Hub.

## <a name="hub"></a>3. Mettre à jour le paramètre de connexion VPN de façon à utiliser ExpressRoute

Après avoir créé le site VPN et établi la connexion au hub, suivez les étapes ci-dessous pour configurer la connexion de façon à utiliser le peering privé ExpressRoute :

1. Revenez à la page des ressources Virtual WAN et cliquez sur la ressource du hub, ou accédez au hub connecté à partir du site VPN.

2. Sous **Connectivité**, cliquez sur **VPN (de site à site)** .

3. Cliquez sur « … » sur le site VPN par ExpressRoute et sélectionnez « **Modifier la connexion VPN à ce hub** ».

4. Sélectionnez « Oui » pour « **Utiliser une adresse IP privée Azure** ». Le paramètre configure la passerelle VPN du hub de façon à utiliser des adresses IP privées situées dans la plage d’adresses du hub sur la passerelle pour cette connexion, au lieu des adresses IP publiques. Ainsi, le trafic provenant du réseau local parcourra les chemins de peering privé ExpressRoute plutôt que d’utiliser l’Internet public pour cette connexion VPN. La capture d’écran qui suit présente la fenêtre des paramètres.

   ![Paramètres de connexion VPN](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
5. Cliquez sur **Enregistrer**.

Une fois enregistrée, la passerelle VPN du hub utilisera les adresses IP privées pour établir les connexions IPsec/IKE avec le périphérique VPN local par ExpressRoute.

## <a name="associate"></a>4. Récupérer les adresses IP privées de la passerelle VPN du hub

Téléchargez la configuration du périphérique VPN pour obtenir les adresses IP privées de la passerelle VPN du hub. Elles sont nécessaires pour configurer le périphérique VPN local.

1. Sur la page de votre hub, cliquez sur **VPN (de site à site)** sous **Connectivité**

2. En haut de la page Vue d’ensemble, cliquez sur **Télécharger la configuration VPN**. Azure crée un compte de stockage dans le groupe de ressources « microsoft-network-[emplacement] », où emplacement est l’emplacement du réseau étendu. Une fois que vous avez appliqué la configuration à vos périphériques VPN, vous pouvez supprimer ce compte de stockage.

3. Une fois que le fichier a terminé la création, vous pouvez cliquer sur le lien pour le télécharger.

4. Appliquez la configuration à votre périphérique VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Comprendre le fichier de configuration de périphérique VPN

Le fichier de configuration de périphérique contient les paramètres à utiliser lors de la configuration de votre périphérique VPN sur site. Lorsque vous affichez ce fichier, notez les informations suivantes :

* **vpnSiteConfiguration -** Cette section indique les détails de l’appareil configuré comme un site se connectant au réseau virtuel étendu. Cela inclut le nom et l’adresse IP publique de l’appareil de branche.
* **vpnSiteConnections -** Cette section fournit des informations sur les paramètres suivants :

    * **Espace d’adressage** du réseau virtuel du/des hub(s)<br/>Exemple :
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * **Espace d’adressage** des réseaux virtuels qui sont connectés au hub<br>Exemple :
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * **Adresses IP** de la passerelle VPN virtuelle. Étant donné que chaque connexion effectuée avec la passerelle VPN est composée de deux tunnels en configuration actif-actif, vous pouvez voir les deux adresses IP répertoriées dans ce fichier. Dans cet exemple, « Instance0 » et « Instance1 » apparaissent pour chaque site ; il s’agit d’adresses IP privées et non d’adresses IP publiques.<br>Exemple :
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * **Détails de configuration de connexion de passerelle VPN**, comme BGP, une clé prépartagée, etc. La clé PSK est la clé prépartagée automatiquement générée pour vous. Vous pouvez toujours modifier la connexion dans la page Vue d’ensemble pour une clé PSK personnalisée.
  
### <a name="example-device-configuration-file"></a>Exemple de fichier de configuration de périphérique

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>Configuration de votre périphérique VPN

Si vous avez besoin d’instructions pour configurer votre périphérique, vous pouvez utiliser les instructions de la [page de scripts de configuration de périphérique VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) en prenant les précautions suivantes :

* Les instructions sur la page des périphériques VPN ne sont pas écrites pour un réseau étendu virtuel, mais vous pouvez utiliser les valeurs WAN virtuel à partir du fichier de configuration pour configurer manuellement votre périphérique VPN. 
* Les scripts de configuration de périphérique téléchargeables qui sont pour la passerelle VPN ne fonctionnent pas pour le réseau étendu virtuel, étant donné que la configuration est différente.
* Un nouvel élément Virtual WAN peut prendre en charge IKEv1 et IKEv2.
* Le WAN virtuel peut uniquement utiliser des périphériques VPN basés sur un itinéraire et des instructions de périphérique.

## <a name="viewwan"></a>5. Afficher votre WAN virtuel

1. Accédez au WAN virtuel.

2. Dans la page Vue d’ensemble, chaque point sur la carte représente un hub. Placez le curseur sur n’importe quel point pour afficher le résumé de l’intégrité du hub.

3. Dans la section des hubs et des connexions, vous pouvez afficher l’état du hub, le site, la région, l’état de la connexion VPN et les octets entrés et sortis.

## <a name="viewhealth"></a>6. Afficher l’intégrité des ressources

1. Accédez à votre réseau étendu.

2. Sur la page de votre WAN, dans la section **SUPPORT + Dépannage**, cliquez sur **Intégrité** pour afficher votre ressource.

## <a name="connectmon"></a>7. Surveiller une connexion

Créez une connexion pour surveiller les communications entre une machine virtuelle Azure et un site distant. Pour plus d’informations sur la façon de configurer un contrôleur de connexion, consultez [Surveiller une communication réseau](~/articles/network-watcher/connection-monitor.md). Le champ source est l’adresse IP de la machine virtuelle dans Azure, et l’adresse IP de destination est le l’adresse IP du site.

## <a name="cleanup"></a>8. Supprimer des ressources

Quand vous n’avez plus besoin de ces ressources, vous pouvez utiliser [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources et toutes les ressources qu’il contient. Remplacez « myResourceGroup » par le nom de votre groupe de ressources et exécutez la commande PowerShell suivante :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Cet article explique comment créer une connexion VPN par peering privé ExpressRoute avec Virtual WAN. Pour plus d’informations sur Virtual WAN et d’autres fonctionnalités liées, voir la page [Vue d’ensemble de Virtual WAN](virtual-wan-about.md).
