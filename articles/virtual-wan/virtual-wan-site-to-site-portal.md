---
title: Utiliser le WAN virtuel Azure pour créer une connexion de site à site vers Azure | Microsoft Docs
description: Dans ce didacticiel, découvrez comment utiliser le WAN virtuel Azure pour créer une connexion VPN de site à site vers Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 1b61c335dec2c641862c08fd6f752d78b2ee5866
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056662"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan-preview"></a>Didacticiel : Créer une connexion de site à site à l’aide du WAN virtuel Azure (préversion)

Ce didacticiel vous montre comment utiliser le WAN virtuel pour vous connecter à vos ressources dans Azure via une connexion VPN IPsec/IKE (IKEv2). Ce type de connexion requiert un périphérique VPN local disposant d’une adresse IP publique exposée en externe. Pour plus d’informations sur le WAN virtuel, consultez [Vue d'ensemble de WAN virtuel](virtual-wan-about.md)

> [!NOTE]
> Si vous avez de nombreux sites, vous utiliseriez généralement un [partenaire WAN virtuel](https://aka.ms.virtualwan) pour créer cette configuration. Toutefois, vous pouvez créer cette configuration vous-même si vous êtes familiarisé avec la mise en réseau et expert dans la configuration de votre propre périphérique VPN.
>

![Diagramme WAN virtuel](./media/virtual-wan-about/virtualwan.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

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

> [!IMPORTANT]
> WAN virtuel Azure est actuellement une préversion publique gérée. Pour utiliser le WAN virtuel, vous devez vous [inscrire à la préversion](#enroll).
>
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration :

* Veillez à disposer d’un périphérique VPN compatible basé sur un itinéraire pouvant utiliser IKEv2, et à être entouré d’une personne en mesure de le configurer. Si vous travaillez avec un partenaire WAN virtuel, les paramètres de configuration sont créés automatiquement et vous n’aurez pas à vous soucier de la configuration manuelle du périphérique.
* Vérifiez que vous disposez d’une adresse IPv4 publique exposée en externe pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.
* Si vous avez déjà un réseau virtuel auquel vous souhaitez vous connecter, vérifiez qu’aucun des sous-réseaux de votre réseau local ne se chevauche avec les réseaux virtuels auxquels vous souhaitez vous connecter. Votre réseau virtuel ne nécessite pas un sous-réseau de passerelles et ne peut pas avoir de passerelles de réseau virtuel. Si vous n’avez pas de réseaux virtuels, vous pouvez en créer un en suivant les étapes dans cet article.
* Obtenez une plage d’adresses IP pour la région de votre hub. Le hub est un réseau virtuel et la plage d’adresses que vous spécifiez pour la région du hub ne peut pas se chevaucher avec vos réseaux virtuels existants auxquels vous vous connectez. Il ne peut pas chevaucher vos plages d’adresses auxquelles vous vous connectez en local. Si vous ne maîtrisez pas les plages d’adresses IP situées dans votre configuration de réseau local, vous devez contacter une personne en mesure de vous aider.
* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="enroll"></a>1. S’inscrire à la préversion

Avant de pouvoir configurer WAN virtuel, vous devez tout d’abord inscrire automatiquement votre abonnement à la préversion. Sinon, vous ne serez pas en mesure d’utiliser WAN virtuel dans le portail. Pour vous abonner, envoyez un e-mail à **azurevirtualwan@microsoft.com** avec votre ID d’abonnement. Vous recevrez un e-mail une fois votre abonnement inscrit.

## <a name="vnet"></a>2. Créez un réseau virtuel

Si vous n’avez pas déjà de réseaux virtuels, vous pouvez en créer un rapidement à l’aide de PowerShell. Vous pouvez également créer un réseau virtuel au moyen du portail Azure.

* Veillez à vérifier que l’espace d’adresse pour le réseau virtuel que vous créez ne chevauche pas les plages d’adresses d’autres réseaux virtuels auxquels vous souhaitez vous connecter, ou avec les espaces d’adresse de votre réseau local. 
* Si vous avez déjà un réseau virtuel, vérifiez qu’il répond aux critères requis et ne dispose pas d’une passerelle de réseau virtuel.

Vous pouvez facilement créer votre réseau virtuel en cliquant sur « Essayez-le » dans cet article pour ouvrir une console PowerShell. Ajustez les valeurs, puis copiez et collez les commandes dans la fenêtre de console.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Ajuster les commandes PowerShell, puis créez un groupe de ressources.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Créer un réseau virtuel

Ajustez les commandes PowerShell pour créer le réseau virtuel compatible avec votre environnement.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```

## <a name="wan"></a>3. Créer un WAN virtuel

1. Dans un navigateur, accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.
2. À ce stade, vous pouvez trouver un WAN virtuel en accédant à **Tous les services** et en recherchant WAN virtuel. Ou bien, vous pouvez chercher un WAN virtuel dans la zone de recherche située en haut du Portail Azure. Cliquez sur **WAN virtuel** pour ouvrir la page.
3. Cliquez sur **Créer** pour ouvrir la page **Créer un WAN**. Si la page n’est pas disponible, vous n'avez pas encore été approuvé pour cette préversion.

  ![Créer un WAN](./media/virtual-wan-site-to-site-portal/createwan.png)
4. Sur la page Créer un WAN, renseignez les champs suivants.

  * **Nom** : sélectionnez le nom que vous souhaitez donner à votre réseau étendu.
  * **Abonnement** : sélectionnez l’abonnement que vous voulez utiliser.
  * **Groupe de ressources** : Créer en un nouveau ou utiliser un qui existe déjà.
  * **Emplacement de la ressource** : choisissez un emplacement de la ressource dans la liste déroulante. Un réseau étendu est une ressource globale et ne réside pas dans une région particulière. Toutefois, vous devez sélectionner une région afin de gérer et de localiser plus facilement la ressource de réseau étendu que vous créez.
5. Cliquez sur **Créer** pour créer le réseau étendu.

## <a name="site"></a>4. Créer un site

Créez autant de sites que nécessaire correspondant à vos emplacements physiques. Par exemple, si vous avez une branche à New York, une autre à Londres et une autre à LA, vous créeriez trois sites distincts. Ces sites contiennent vos points de terminaison du périphérique VPN local. À ce stade, vous ne pouvez spécifier qu’un seul espace d’adressage privé pour votre site.

1. Naviguez jusqu’à **Toutes les ressources**.
2. Cliquez sur le réseau virtuel étendu que vous avez créé.
3. Cliquez sur **+ Créer un site** en haut de la page pour ouvrir la page **Créer un site**.

  ![nouveau site](media/virtual-wan-site-to-site-portal/createsite.png)
4. Sur la page **Créer un site**, renseignez les champs suivants :

  *  **Nom** : c’est le nom par lequel vous souhaitez faire référence à votre site local.
  *  **Adresse IP publique** : il s’agit de l’adresse IP publique du périphérique VPN qui réside sur votre site local.
  *  **Espace d’adressage privé** : il s’agit de l’espace d’adresse IP situé sur votre site local. Le trafic destiné à cet espace d’adressage est acheminé vers votre site local.
  *  **Abonnement** : vérifiez l’abonnement.
  *  **Groupe de ressources** : le groupe de ressources que vous souhaitez utiliser.
5. Cliquez sur **Afficher les options avancées** pour afficher des paramètres supplémentaires. Vous pouvez **activer BGP** (champ facultatif, qui activera cette fonctionnalité sur toutes les connexions créées pour ce site dans Azure). Vous pouvez également saisir des **informations sur le périphérique** (champ facultatif). Cela peut aider l’équipe Azure à mieux comprendre votre environnement afin d’ajouter des possibilités d’optimisation supplémentaires ou pour vous aider à résoudre les problèmes.

  ![BGP](media/virtual-wan-site-to-site-portal/sitebgp.png)
6. Cliquez sur **Confirmer** pour créer le site.
7. Répétez ces étapes pour chaque site que vous souhaitez créer.

## <a name="hub"></a>5. Créer un hub et connecter des sites

1. Dans la page de votre réseau étendu virtuel, cliquez sur **Sites**.
2. Sous **Sites non associés**, vous voyez une liste de sites qui n’ont pas encore été connectés à un concentrateur.
3. Sélectionnez les sites que vous souhaitez associer.
4. Dans la liste déroulante, sélectionnez la région avec laquelle votre hub sera associé. Vous devez associer votre hub à la région où résident les réseaux virtuels auxquels vous souhaitez être connectés.
5. Cliquez sur **Confirmer**. Si vous n’avez pas encore de hub dans cette région, un réseau virtuel de hub virtuel est automatiquement créé. Dans ce cas, la page **Créer des hubs régionaux** s’affiche.
6. Sur la page **Créer des hubs régionaux**, saisissez la plage d’adresses pour le réseau virtuel de votre hub. Il s’agit du réseau virtuel qui contiendra vos services de hub. La plage que vous spécifiez ici doit être une plage d’adresses IP privées et ne peut pas chevaucher vos espaces d’adressage sur site ou vos espaces d’adressage de réseau virtuel. Un point de terminaison VPN suivant sera créé dans le réseau virtuel du hub. (La création automatique d’un hub et de la passerelle est uniquement disponible dans le portail.)
7. Cliquez sur **Créer**.

## <a name="vnet"></a>6. Connecter votre réseau virtuel à un hub

Dans cette étape, vous créez la connexion de peering entre votre hub et un réseau virtuel. Répétez ces étapes pour chaque réseau virtuel que vous souhaitez connecter.

1. Dans la page de votre réseau étendu, cliquez sur **Connexion réseau de réseau virtuel**.
2. Dans la page de connexion de réseau virtuel, cliquez sur **+ Ajouter une connexion**.
3. Dans la page **Ajouter une connexion**, renseignez les champs suivants :

    * **Nom de connexion** : nommez votre connexion.
    * **Hubs** : sélectionnez le hub que vous souhaitez associer à cette connexion.
    * **Abonnement** : vérifiez l’abonnement.
    * **Réseau virtuel** : sélectionnez le réseau virtuel que vous souhaitez connecter à ce hub. Le réseau virtuel ne peut pas avoir une passerelle de réseau virtuel déjà existante.

## <a name="device"></a>7. Télécharger une configuration VPN

Utilisez la configuration de périphérique VPN pour configurer votre périphérique VPN local.

1. Dans la page de votre réseau étendu virtuel, cliquez sur **Vue d’ensemble**.
2. En haut de la page Vue d'ensemble, cliquez sur **Télécharger la configuration VPN**. Azure crée un compte de stockage dans le groupe de ressources « microsoft-network-[emplacement] », où emplacement est l’emplacement du réseau étendu. Une fois que vous avez appliqué la configuration à vos périphériques VPN, vous pouvez supprimer ce compte de stockage.
3. Une fois que le fichier a terminé la création, vous pouvez cliquer sur le lien pour le télécharger.
4. Appliquez la configuration à votre périphérique VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Comprendre le fichier de configuration de périphérique VPN

Le fichier de configuration de périphérique contient les paramètres à utiliser lors de la configuration de votre périphérique VPN sur site. Lorsque vous affichez ce fichier, notez les informations suivantes :

* **vpnSiteConfiguration -** Cette section indique les détails de l’appareil configuré comme un site se connectant au réseau virtuel étendu. Cela inclut le nom et l’adresse IP publique de l’appareil de branche.
* **vpnSiteConnections -** Cette section fournit des informations sur les éléments suivants :

    * **Espace d’adressage** du réseau virtuel du/des hub(s)<br>Exemple :
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Espace d’adressage** des réseaux virtuels qui sont connectés au hub<br>Exemple :

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **Adresses IP** de la passerelle VPN virtuelle. Étant donné que la passerelle VPN a chaque connexion comprenant des 2 tunnels en configuration actif-actif, vous verrez les deux adresses IP répertoriées dans ce fichier. Dans cet exemple, vous voyez « Instance0 » et « Instance1 » pour chaque site.<br>Exemple :

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
> Si vous travaillez avec une solution de partenaire WAN virtuel, une configuration de périphérique VPN se produit automatiquement où le contrôleur de périphérique obtient le fichier de configuration à partir d’Azure et l’applique au périphérique pour configurer la connexion à Azure. Cela signifie que vous n’avez pas besoin de savoir comment configurer manuellement votre périphérique VPN.
>

Si vous avez besoin d’instructions pour configurer votre périphérique, vous pouvez utiliser les instructions de la [page de scripts de configuration de périphérique VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) en prenant les précautions suivantes :

* Les instructions sur la page des périphériques VPN ne sont pas écrites pour un réseau étendu virtuel, mais vous pouvez utiliser les valeurs WAN virtuel à partir du fichier de configuration pour configurer manuellement votre périphérique VPN. 
* Les scripts de configuration de périphérique téléchargeables qui sont pour la passerelle VPN ne fonctionnent pas pour le réseau étendu virtuel, étant donné que la configuration est différente.
* Le réseau étendu virtuel peut uniquement utiliser le protocole IKEv2, et non pas IKEv1.
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

## <a name="cleanup"></a>11. Supprimer les ressources

Quand vous n’avez plus besoin de ces ressources, vous pouvez utiliser [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour le supprimer et toutes les ressources qu’il contient. Remplacez « myResourceGroup » par le nom de votre groupe de ressources et exécutez la commande PowerShell suivante :

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="feedback"></a>Commentaires de la préversion

Nous aimerions recevoir vos commentaires. Envoyez un e-mail à <azurevirtualwan@microsoft.com> pour signaler des problèmes ou pour fournir des commentaires (positifs ou négatifs) sur le réseau étendu virtuel. Ajoutez le nom de votre société entre crochets « [] » dans la ligne Objet. Ajoutez également votre ID d’abonnement, si vous signalez un problème.

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