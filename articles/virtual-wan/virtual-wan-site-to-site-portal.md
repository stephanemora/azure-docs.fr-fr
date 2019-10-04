---
title: Utiliser le WAN virtuel Azure pour créer une connexion de site à site vers Azure | Microsoft Docs
description: Dans ce didacticiel, découvrez comment utiliser le WAN virtuel Azure pour créer une connexion VPN de site à site vers Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: e9be7ef5c4f37c66f7cbf2c6226936438b367108
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515169"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Didacticiel : Créer une connexion de site à site à l’aide d’Azure Virtual WAN

Ce didacticiel vous montre comment utiliser Virtual WAN pour vous connecter à vos ressources dans Azure via une connexion VPN IPsec/IKE (IKEv1 et IKEv2). Ce type de connexion requiert un périphérique VPN local disposant d’une adresse IP publique exposée en externe. Pour plus d’informations sur Azure Virtual WAN, consultez l’article [Vue d'ensemble d’Azure Virtual WAN](virtual-wan-about.md).

> [!NOTE]
> Si vous avez de nombreux sites, vous utiliseriez généralement un [partenaire WAN virtuel](https://aka.ms/virtualwan) pour créer cette configuration. Toutefois, vous pouvez créer cette configuration vous-même si vous êtes familiarisé avec la mise en réseau et expert dans la configuration de votre propre périphérique VPN.
>

![Diagramme WAN virtuel](./media/virtual-wan-about/virtualwan.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un WAN
> * Créer un site
> * Créer un hub
> * Connecter un hub à un site
> * Créer un réseau virtuel compatible (si vous n’en avez pas encore)
> * Connecter un réseau virtuel à un hub
> * Télécharger et appliquer la configuration de périphérique VPN
> * Afficher votre WAN virtuel
> * Afficher l’intégrité des ressources
> * Surveiller une connexion

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. Créer un WAN virtuel

Dans un navigateur, accédez au [portail Azure](https://aka.ms/azurevirtualwanpreviewfeatures) et connectez-vous avec votre compte Azure.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="site"></a>2. Créer un site

Créez autant de sites que nécessaire correspondant à vos emplacements physiques. Par exemple, si vous avez une succursale à New York, une autre à Londres et une autre à LA, vous créeriez trois sites distincts. Ces sites contiennent vos points de terminaison du périphérique VPN local. À ce stade, vous ne pouvez spécifier qu’un seul espace d’adressage privé pour votre site.

1. Cliquez sur le WAN que vous avez créé. Sur la page du WAN, sous **WAN Architecture** (Architecture WAN), cliquez sur **Sites VPN** pour ouvrir la page dédiée aux sites VPN.
2. Sur la page **Sites VPN**, cliquez sur **+Créer un site**.
3. Sur la page **Créer un site**, renseignez les champs suivants :

   * **Nom** : nom par lequel vous souhaitez faire référence à votre site local.
   * **Adresse IP publique** : adresse IP publique du périphérique VPN qui réside sur votre site local.
   * **Espace d’adressage privé** : il s’agit de l’espace d’adresse IP situé sur votre site local. Le trafic destiné à cet espace d’adressage est acheminé vers votre site local.
   * **Abonnement** : vérifiez l’abonnement.
   * **Groupe de ressources** : le groupe de ressources que vous souhaitez utiliser.
   * **Lieu**
4. Cliquez sur **Afficher les options avancées** pour afficher des paramètres supplémentaires. 

   Vous pouvez sélectionner **BGP** pour activer le protocole BGP et ainsi activer cette fonctionnalité sur toutes les connexions créées pour ce site dans Azure. La configuration du protocole BGP sur un réseau Virtual WAN équivaut à configurer le protocole BGP sur une passerelle VPN Azure. Votre adresse d’homologue BGP local *ne doit pas* être identique à l’adresse IP publique de votre réseau VPN vers l’appareil ou à l’espace d’adresse du réseau virtuel du site VPN. Utilisez une adresse IP différente sur le périphérique VPN de votre adresse IP BGP homologue. Il peut s’agir d’une adresse affectée à l’interface de bouclage sur le périphérique. Toutefois, il *ne peut pas* s’agir d’une adresse APIPA (169.254.*x*.*x*). Spécifiez cette adresse sur la passerelle de réseau local correspondante, représentant l’emplacement. Pour les conditions préalables BGP, consultez [À propos de BGP avec la passerelle VPN Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).

   Vous pouvez également renseigner le champ **Informations sur le périphérique** (facultatif). Cela peut aider l’équipe Azure à mieux comprendre votre environnement afin d’ajouter des possibilités d’optimisation supplémentaires à l’avenir ou pour vous aider à résoudre les problèmes.
   
5. Cliquez sur **Confirmer**.
6. Après avoir cliqué sur **Confirmer**, affichez l’état sur la page Sites VPN. Le site passe de l’état **Approvisionnement** à l’état **Approvisionné**.

## <a name="hub"></a>3. Créer un hub

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="associate"></a>4. Associer les sites au hub

Les hubs doivent généralement être associés aux sites qui se trouvent dans la même région que celle du réseau virtuel.

1. Sur la page **Sites VPN**, sélectionnez les sites que vous souhaitez associer au hub, puis cliquez sur **+Nouvelle association au hub**.
2. Sur la page **Associate sites with one or more hubs** (Associer des sites à un ou plusieurs hubs), sélectionnez un hub dans la liste déroulante. Vous pouvez associer un site à d’autres hubs en cliquant sur **+Add an association** (+Ajouter une association).
3. Vous pouvez également ajouter une **PSK** spécifique ici ou utiliser la valeur par défaut.
4. Cliquez sur **Confirmer**.
5. Vous pouvez afficher l’état de la connexion sur la page **Sites VPN**.

## <a name="vnet"></a>5. Créez un réseau virtuel

Si vous n’avez pas déjà de réseaux virtuels, vous pouvez en créer un rapidement à l’aide de PowerShell ou du portail Azure. Si vous avez déjà un réseau virtuel, vérifiez qu’il répond aux critères requis et ne dispose pas d’une passerelle de réseau virtuel.

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="vnet"></a>6. Connecter votre réseau virtuel à un hub

Dans cette étape, vous créez la connexion de peering entre votre hub et un réseau virtuel. Répétez ces étapes pour chaque réseau virtuel que vous souhaitez connecter.

1. Sur la page de votre WAN virtuel, cliquez sur **Connexion de réseau virtuel**.
2. Dans la page de connexion de réseau virtuel, cliquez sur **+ Ajouter une connexion**.
3. Dans la page **Ajouter une connexion**, renseignez les champs suivants :

    * **Nom de connexion** : nommez votre connexion.
    * **Hubs** : sélectionnez le hub que vous souhaitez associer à cette connexion.
    * **Abonnement** : vérifiez l’abonnement.
    * **Réseau virtuel** : sélectionnez le réseau virtuel que vous souhaitez connecter à ce hub. Le réseau virtuel ne peut pas avoir une passerelle de réseau virtuel déjà existante.
4. Cliquez sur **OK** pour créer la connexion du peering.

## <a name="device"></a>7. Télécharger une configuration VPN

Utilisez la configuration de périphérique VPN pour configurer votre périphérique VPN local.

1. Dans la page de votre réseau étendu virtuel, cliquez sur **Vue d’ensemble**.
2. En haut de la page Vue d'ensemble, cliquez sur **Télécharger la configuration VPN**. Azure crée un compte de stockage dans le groupe de ressources « microsoft-network-[emplacement] », où emplacement est l’emplacement du réseau étendu. Une fois que vous avez appliqué la configuration à vos périphériques VPN, vous pouvez supprimer ce compte de stockage.
3. Une fois que le fichier a terminé la création, vous pouvez cliquer sur le lien pour le télécharger.
4. Appliquez la configuration à votre périphérique VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Comprendre le fichier de configuration de périphérique VPN

Le fichier de configuration de périphérique contient les paramètres à utiliser lors de la configuration de votre périphérique VPN sur site. Lorsque vous affichez ce fichier, notez les informations suivantes :

* **vpnSiteConfiguration -** Cette section indique les détails de l’appareil configuré comme un site se connectant au réseau virtuel étendu. Cela inclut le nom et l’adresse IP publique de l’appareil de branche.
* **vpnSiteConnections -** Cette section fournit des informations sur les paramètres suivants :

    * **Espace d’adressage** du réseau virtuel du/des hub(s)<br>Exemple :
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Espace d’adressage** des réseaux virtuels qui sont connectés au hub<br>Exemple :

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **Adresses IP** de la passerelle VPN virtuelle. Étant donné que chaque connexion effectuée avec la passerelle VPN est composée de deux tunnels en configuration actif-actif, vous pouvez voir les deux adresses IP répertoriées dans ce fichier. Dans cet exemple, vous voyez « Instance0 » et « Instance1 » pour chaque site.<br>Exemple :

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Détails de configuration de connexion de passerelle VPN**, comme BGP, une clé prépartagée, etc. La clé PSK est la clé prépartagée automatiquement générée pour vous. Vous pouvez toujours modifier la connexion dans la page Vue d’ensemble pour une clé PSK personnalisée.
  
### <a name="example-device-configuration-file"></a>Exemple de fichier de configuration de périphérique

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>Configuration de votre périphérique VPN

>[!NOTE]
> Si vous utilisez une solution de partenaire Virtual WAN, la configuration de l’appareil VPN est automatique. Le contrôleur de l’appareil obtient le fichier config d’Azure et l’applique à l’appareil pour configurer la connexion à Azure. Cela signifie que vous n’avez pas besoin de savoir comment configurer manuellement votre périphérique VPN.
>

Si vous avez besoin d’instructions pour configurer votre périphérique, vous pouvez utiliser les instructions de la [page de scripts de configuration de périphérique VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) en prenant les précautions suivantes :

* Les instructions sur la page des périphériques VPN ne sont pas écrites pour un réseau étendu virtuel, mais vous pouvez utiliser les valeurs WAN virtuel à partir du fichier de configuration pour configurer manuellement votre périphérique VPN. 
* Les scripts de configuration de périphérique téléchargeables qui sont pour la passerelle VPN ne fonctionnent pas pour le réseau étendu virtuel, étant donné que la configuration est différente.
* Un nouvel élément Virtual WAN peut prendre en charge IKEv1 et IKEv2.
* Le WAN virtuel peut uniquement utiliser des périphériques VPN basés sur un itinéraire et des instructions de périphérique.

## <a name="viewwan"></a>8. Afficher votre WAN virtuel

1. Accédez au WAN virtuel.
2. Dans la page Vue d’ensemble, chaque point sur la carte représente un hub. Placez le curseur sur n’importe quel point pour afficher le résumé de l’intégrité du hub.
3. Dans la section des hubs et des connexions, vous pouvez afficher l’état du hub, le site, la région, l’état de la connexion VPN et les octets entrés et sortis.

## <a name="viewhealth"></a>9. Afficher l’intégrité des ressources

1. Accédez à votre réseau étendu.
2. Sur la page de votre WAN, dans la section **SUPPORT + Dépannage**, cliquez sur **Intégrité** pour afficher votre ressource.

## <a name="connectmon"></a>10. Surveiller une connexion

Créez une connexion pour surveiller les communications entre une machine virtuelle Azure et un site distant. Pour plus d’informations sur la façon de configurer un contrôleur de connexion, consultez [Surveiller une communication réseau](~/articles/network-watcher/connection-monitor.md). Le champ source est l’adresse IP de la machine virtuelle dans Azure, et l’adresse IP de destination est le l’adresse IP du site.

## <a name="cleanup"></a>11. Supprimer des ressources

Quand vous n’avez plus besoin de ces ressources, vous pouvez utiliser [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources et toutes les ressources qu’il contient. Remplacez « myResourceGroup » par le nom de votre groupe de ressources et exécutez la commande PowerShell suivante :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer un WAN
> * Créer un site
> * Créer un hub
> * Connecter un hub à un site
> * Connecter un réseau virtuel à un hub
> * Télécharger et appliquer la configuration de périphérique VPN
> * Afficher votre WAN virtuel
> * Afficher l’intégrité des ressources
> * Surveiller une connexion

Pour plus d’informations sur le WAN virtuel, consultez la page [Vue d'ensemble de WAN virtuel](virtual-wan-about.md).
