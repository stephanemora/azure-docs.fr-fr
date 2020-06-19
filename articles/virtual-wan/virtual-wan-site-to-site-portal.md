---
title: 'Azure Virtual WAN : Créer des connexions site à site'
description: Dans ce didacticiel, découvrez comment utiliser le WAN virtuel Azure pour créer une connexion VPN de site à site vers Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: b4278cb2e8c5152f522258a37c37acda5efbacf8
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84687920"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Tutoriel : Créer une connexion de site à site à l’aide d’Azure Virtual WAN

Ce didacticiel vous montre comment utiliser Virtual WAN pour vous connecter à vos ressources dans Azure via une connexion VPN IPsec/IKE (IKEv1 et IKEv2). Ce type de connexion requiert un périphérique VPN local disposant d’une adresse IP publique exposée en externe. Pour plus d’informations sur Azure Virtual WAN, consultez l’article [Vue d’ensemble d’Azure Virtual WAN](virtual-wan-about.md).

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un WAN virtuel
> * Créer un hub
> * Créer un site
> * Connecter un site à un hub
> * Connecter un site VPN à un hub
> * Connecter un réseau virtuel à un hub
> * Télécharger un fichier de configuration
> * Afficher votre WAN virtuel

> [!NOTE]
> Si vous avez de nombreux sites, vous utiliseriez généralement un [partenaire WAN virtuel](https://aka.ms/virtualwan) pour créer cette configuration. Toutefois, vous pouvez créer cette configuration vous-même si vous êtes familiarisé avec la mise en réseau et expert dans la configuration de votre propre périphérique VPN.
>

![Diagramme WAN virtuel](./media/virtual-wan-about/virtualwan.png)

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration :

* Vous avez un réseau virtuel auquel vous souhaitez vous connecter. Vérifiez qu’aucun des sous-réseaux de votre réseau local ne chevauche les réseaux virtuels auxquels vous souhaitez vous connecter. Pour créer un réseau virtuel dans le portail Azure, consultez le [guide de démarrage rapide](../virtual-network/quick-create-portal.md).

* Votre réseau virtuel n’a pas de passerelle de réseau virtuel. Si votre réseau virtuel dispose d’une passerelle (VPN ou ExpressRoute), vous devez supprimer toutes les passerelles. Cette configuration nécessite que les réseaux virtuels soient connectés à la passerelle hub Virtual WAN.

* Obtenez une plage d’adresses IP pour la région de votre hub. Le hub est un réseau virtuel qui est créé et utilisé par Virtual WAN. La plage d’adresses que vous spécifiez pour le hub ne peut pas chevaucher les réseaux virtuels existants auxquels vous vous connectez. Elle ne peut pas non plus chevaucher vos plages d’adresses auxquelles vous vous connectez en local. Si vous ne maîtrisez pas les plages d’adresses IP situées dans votre configuration de réseau local, contactez une personne en mesure de vous aider.

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Créer un WAN virtuel

Dans un navigateur, accédez au Portail Azure et connectez-vous avec votre compte Azure.

1. Accédez à la page WAN Virtuel. Dans le portail, cliquez sur **+Créer une ressource**. Tapez **WAN virtuel** dans la zone de recherche et sélectionnez Entrée.
2. Sélectionnez **WAN virtuel** dans les résultats. Dans la page Virtual WAN, cliquez sur **Créer** pour ouvrir la page Créer un WAN.
3. Dans la page **Créer un WAN**, sous l’onglet **Fonctions de base**, renseignez les champs suivants :

   ![WAN virtuel](./media/virtual-wan-site-to-site-portal/vwan.png)

   * **Abonnement** : sélectionnez l’abonnement que vous voulez utiliser.
   * **Groupe de ressources** : créez-en un ou utilisez-en un qui existe déjà.
   * **Emplacement du groupe de ressources** : choisissez un emplacement de la ressource dans la liste déroulante. Un WAN est une ressource globale et ne réside pas dans une région particulière. Toutefois, vous devez sélectionner une région pour gérer et localiser plus facilement la ressource WAN que vous créez.
   * **Nom** : tapez le nom que vous souhaitez donner à votre WAN.
   * **Type :** De base ou Standard. Si vous créez un WAN de base, vous ne pourrez créer qu’un hub de base. Les hubs de base offrent uniquement une connectivité de site VPN à site.
4. Quand vous avez fini de renseigner les champs, cliquez sur **Vérifier + Créer**.
5. Après la validation, sélectionnez **Créer** pour créer le WAN virtuel.

## <a name="create-a-hub"></a><a name="hub"></a>Créer un hub

Un hub est un réseau virtuel qui peut contenir des passerelles pour offrir des fonctionnalités de site à site, ExpressRoute ou de point à site. Une fois le hub créé, vous serez facturé, même si vous n’y joignez aucun site. La création de la passerelle VPN de site à site prend 30 minutes dans le hub virtuel.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-site"></a><a name="site"></a>Créer un site

Vous êtes maintenant prêt à créer les sites correspondant à vos emplacements physiques. Créez autant de sites que nécessaire correspondant à vos emplacements physiques. Par exemple, si vous avez une succursale à New York, une autre à Londres et une autre à LA, vous créeriez trois sites distincts. Ces sites contiennent vos points de terminaison du périphérique VPN local. Vous pouvez créer jusqu’à 1 000 sites par hub virtuel dans Virtual WAN. Si vous aviez plusieurs hubs, vous pouvez créer 1 000 sites pour chacun de ces hubs. Si vous avez un appareil CPE de partenaire Virtual WAN (lien d’insertion), contactez ce dernier pour en savoir plus sur son automatisation sur Azure. En général, l’automatisation permet d’exporter en un seul clic des informations de branche à grande échelle dans Azure et de configurer la connectivité entre l’appareil CPE et la passerelle VPN Azure Virtual WAN. Pour plus d’informations, consultez [Guide d’automatisation Azure à l’intention des partenaires CPE](virtual-wan-configure-automation-providers.md).

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-the-vpn-site-to-the-hub"></a><a name="connectsites"></a>Connecter le site VPN au hub

Au cours de cette étape, vous connectez votre site VPN au hub.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Connecter le réseau virtuel au hub

Au cours de cette étape, vous créez la connexion entre votre hub et un réseau virtuel. Répétez ces étapes pour chaque réseau virtuel que vous souhaitez connecter.

1. Sur la page de votre WAN virtuel, cliquez sur **Connexion de réseau virtuel**.
2. Dans la page de connexion de réseau virtuel, cliquez sur **+ Ajouter une connexion**.
3. Dans la page **Ajouter une connexion**, renseignez les champs suivants :

    * **Nom de connexion** : nommez votre connexion.
    * **Hubs** : sélectionnez le hub que vous souhaitez associer à cette connexion.
    * **Abonnement** : vérifiez l’abonnement.
    * **Réseau virtuel** : sélectionnez le réseau virtuel que vous souhaitez connecter à ce hub. Le réseau virtuel ne peut pas avoir une passerelle de réseau virtuel déjà existante.
4. Cliquez sur **OK** pour créer la connexion de réseau virtuel.

## <a name="download-vpn-configuration"></a><a name="device"></a>Télécharger une configuration VPN

Utilisez la configuration de périphérique VPN pour configurer votre périphérique VPN local.

1. Dans la page de votre réseau étendu virtuel, cliquez sur **Vue d’ensemble**.
2. En haut de la page **Hub ->VPNSite**, cliquez sur **Télécharger la configuration VPN**. Azure crée un compte de stockage dans le groupe de ressources « microsoft-network-[emplacement] », où emplacement est l’emplacement du réseau étendu. Une fois que vous avez appliqué la configuration à vos périphériques VPN, vous pouvez supprimer ce compte de stockage.
3. Une fois que le fichier a terminé la création, vous pouvez cliquer sur le lien pour le télécharger.
4. Appliquez la configuration à votre périphérique VPN local.

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
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
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
                  "10.3.0.0/16"
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
* Virtual WAN peut utiliser des appareils VPN basés sur une stratégie et sur une route, et des instructions d’appareil.

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Afficher votre WAN virtuel

1. Accédez au WAN virtuel.
2. Sur la page **Vue d’ensemble**, chaque point sur la carte représente un hub. Placez le curseur sur n’importe quel point pour afficher le résumé d’intégrité du hub, son état de connexion et son nombre d’octets entrants et sortants.
3. Dans la section Hubs et connexions, vous pouvez afficher l’état du hub, les sites VPN, etc. Vous pouvez cliquer sur un nom de hub spécifique et accéder au site VPN pour obtenir des détails supplémentaires.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Virtual WAN, consultez la page [Vue d’ensemble de Virtual WAN](virtual-wan-about.md).
