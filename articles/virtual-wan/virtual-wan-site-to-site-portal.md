---
title: 'Tutoriel : Utiliser Azure Virtual WAN pour créer des connexions de site à site'
description: Découvrez comment utiliser Azure Virtual WAN pour créer une connexion VPN site à site avec Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 08/18/2021
ms.author: cherylmc
ms.openlocfilehash: dbc48719f2897c22717319e1e07b5b3b3146fe84
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122638229"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Tutoriel : Créer une connexion de site à site à l’aide d’Azure Virtual WAN

Ce didacticiel vous montre comment utiliser Virtual WAN pour vous connecter à vos ressources dans Azure via une connexion VPN IPsec/IKE (IKEv1 et IKEv2). Ce type de connexion requiert un périphérique VPN local disposant d’une adresse IP publique exposée en externe. Pour plus d’informations sur Azure Virtual WAN, consultez l’article [Vue d’ensemble d’Azure Virtual WAN](virtual-wan-about.md).

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un WAN virtuel
> * Créer un hub
> * Créer une passerelle VPN de site à site
> * Créer un site
> * Connecter un site à un hub
> * Connecter un site VPN à un hub
> * Connecter un réseau virtuel à un hub
> * Télécharger un fichier de configuration
> * Afficher ou modifier votre passerelle VPN

> [!NOTE]
> Si vous avez de nombreux sites, vous utiliseriez généralement un [partenaire WAN virtuel](https://aka.ms/virtualwan) pour créer cette configuration. Toutefois, vous pouvez créer cette configuration vous-même si vous êtes familiarisé avec la mise en réseau et expert dans la configuration de votre propre périphérique VPN.
>

:::image type="content" source="./media/virtual-wan-about/virtualwan.png" alt-text="La capture d’écran montre un diagramme de réseau pour Virtual WAN.":::

## <a name="prerequisites"></a>Prérequis

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration :

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Créer un WAN virtuel

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-hub"></a><a name="hub"></a>Créer un hub

Un hub est un réseau virtuel qui peut contenir des passerelles pour offrir des fonctionnalités de site à site, ExpressRoute ou de point à site. Pour ce didacticiel, vous commencez par remplir l’onglet **Général** pour le hub virtuel, puis continuez à remplir l’onglet de site à site dans la section suivante. Notez qu’il est possible de créer un hub vide (un hub qui ne contient pas de passerelle), puis d’ajouter des passerelles (S2S, P2S, ExpressRoute, etc.) ultérieurement. Une fois le hub créé, vous serez facturé, même si vous n’y joignez aucun site et ne créez aucune passerelle dans le hub.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-site-to-site-vpn-gateway"></a><a name="gateway"></a>Créer une passerelle VPN de site à site

Dans cette section, vous allez configurer les paramètres de connectivité de site à site, puis procéder à la création du hub et de la passerelle S2S VPN. La création d’un hub et d’une passerelle peut prendre environ 30 minutes.

[!INCLUDE [Create a gateway](../../includes/virtual-wan-tutorial-s2s-gateway-include.md)]

## <a name="create-site"></a><a name="site"></a>Créer un site

Dans cette section, vous allez créer un site. Les sites correspondent à vos emplacements physiques. Créez autant de sites qu’il vous faut. Par exemple, si vous avez une succursale à New York, une autre à Londres et une autre à LA, vous créeriez trois sites distincts. Ces sites contiennent vos points de terminaison du périphérique VPN local. Vous pouvez créer jusqu’à 1000 sites par hub virtuel dans un WAN virtuel. Si vous aviez plusieurs hubs, vous pouvez créer 1 000 sites pour chacun de ces hubs. Si vous avez un appareil CPE de partenaire Virtual WAN, contactez ce dernier pour en savoir plus sur son automatisation sur Azure. En général, l’automatisation permet d’exporter en un seul clic des informations de branche à grande échelle dans Azure, et de configurer la connectivité entre l’appareil CPE et la passerelle VPN Azure Virtual WAN. Pour plus d’informations, consultez [Guide d’automatisation Azure à l’intention des partenaires CPE](virtual-wan-configure-automation-providers.md).

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-vpn-site-to-hub"></a><a name="connectsites"></a>Connexion de site VPN à concentrateur

Dans cette section, vous connectez votre site VPN au hub.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-vnet-to-hub"></a><a name="vnet"></a>Connecter un réseau virtuel à un hub

Dans cette section, vous créez une connexion entre le hub et votre réseau virtuel.

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-vpn-configuration"></a><a name="device"></a>Télécharger une configuration VPN

Utilisez le fichier de configuration de périphérique VPN pour configurer votre périphérique VPN local. Les étapes de base sont décrites ci-dessous. Les informations relatives à ce que contient le fichier de configuration et la configuration de votre périphérique VPN sont 

1. Accédez à votre page **HUB virtuel -> VPN (point à site)** .

1. En haut de la page **VPN (site à site)** , cliquez sur **Télécharger la configuration VPN**. Vous verrez une série de messages pendant qu’Azure crée un compte de stockage dans le groupe de ressources ’microsoft-network-[location]’, où location est l’emplacement du WAN.

1. Une fois que la création du fichier est terminée, cliquez sur le lien pour le télécharger. Pour en savoir plus sur le contenu du fichier, consultez [À propos du fichier de configuration de périphérique VPN](#config-file) dans cette section.

1. Appliquez la configuration à votre périphérique VPN local. Pour plus d’informations, consultez la page [Configuration de périphérique VPN](#vpn-device) dans cette section.

1. Une fois que vous avez appliqué la configuration à vos périphériques VPN, il n’est pas nécessaire de conserver le compte de stockage créé par Azure. Vous pouvez le supprimer.

### <a name="about-the-vpn-device-configuration-file"></a><a name="config-file"></a>À propos du fichier de configuration de périphérique VPN

Le fichier de configuration de périphérique contient les paramètres à utiliser lors de la configuration de votre périphérique VPN sur site. Lorsque vous affichez ce fichier, notez les informations suivantes :

* **vpnSiteConfiguration -** Cette section indique les détails de l’appareil configuré comme un site se connectant au réseau virtuel étendu. Cela inclut le nom et l’adresse IP publique de l’appareil de branche.
* **vpnSiteConnections -** Cette section fournit des informations sur les paramètres suivants :

    * **Espace d’adressage** du réseau virtuel du/des hub(s).<br>Exemple :
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Espace d’adressage** des réseaux virtuels qui sont connectés au hub.<br>Exemple :

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

### <a name="configuring-your-vpn-device"></a><a name="vpn-device"></a>Configuration de votre périphérique VPN

>[!NOTE]
> Si vous utilisez une solution de partenaire Virtual WAN, la configuration de l’appareil VPN est automatique. Le contrôleur de l’appareil obtient le fichier config d’Azure et l’applique à l’appareil pour configurer la connexion à Azure. Cela signifie que vous n’avez pas besoin de savoir comment configurer manuellement votre périphérique VPN.
>

Si vous avez besoin d’instructions pour configurer votre périphérique, vous pouvez utiliser les instructions de la [page de scripts de configuration de périphérique VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) en prenant les précautions suivantes :

* Les instructions sur la page des périphériques VPN ne sont pas écrites pour un réseau étendu virtuel, mais vous pouvez utiliser les valeurs WAN virtuel à partir du fichier de configuration pour configurer manuellement votre périphérique VPN.
 
* Les scripts de configuration de périphérique téléchargeables qui sont pour la passerelle VPN ne fonctionnent pas pour le réseau étendu virtuel, étant donné que la configuration est différente.

* Un nouvel élément Virtual WAN peut prendre en charge IKEv1 et IKEv2.

* Virtual WAN peut utiliser des appareils VPN basés sur une stratégie et sur une route, et des instructions d’appareil.

## <a name="view-or-edit-gateway-settings"></a><a name="gateway-config"></a>Afficher ou modifier les paramètres de la passerelle

Vous pouvez afficher et modifier les paramètres de votre passerelle VPN à tout moment en accédant à **Hub virtuel-> VPN (site à site)** et en sélectionnant **Afficher/configurer**.

:::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-1.png" alt-text="Capture d’écran montrant la page « VPN (site à site) » avec une flèche pointant vers l’action « Afficher/configurer »." lightbox="media/virtual-wan-site-to-site-portal/view-configuration-1-expand.png":::

Sur la page **Modifier la passerelle VPN**, vous pouvez voir les paramètres suivants :

* **Adresse IP publique** : Attribuée par Azure.
* **Adresse IP privée** : Attribuée par Azure.
* **Adresse IP BGP par défaut** : Attribuée par Azure.
* **Adresse IP BGP personnalisée** : Ce champ est réservé à APIPA (Automatic Private IP Addressing). Azure prend en charge l’adresse IP BGP dans les plages 169.254.21.* et 169.254.22.*. Azure accepte les connexions BGP dans ces plages, mais établit la connexion avec l’adresse IP BGP par défaut.

   :::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-2.png" alt-text="La capture d’écran montre la page Modifier la passerelle VPN, avec mise en évidence du bouton Modifier." lightbox="media/virtual-wan-site-to-site-portal/view-configuration-2-expand.png":::

## <a name="clean-up-resources"></a><a name="cleanup"></a>Supprimer des ressources

Quand vous n’avez plus besoin des ressources que vous avez créées, supprimez-les. Certaines des ressources Virtual WAN doivent être supprimées dans un certain ordre en raison des dépendances. La suppression peut prendre environ 30 minutes.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Ensuite, pour en savoir plus sur Virtual WAN, consultez :

> [!div class="nextstepaction"]
> * [FAQ sur Virtual WAN](virtual-wan-faq.md)
