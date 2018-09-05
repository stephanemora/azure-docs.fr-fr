---
title: Configurer l’automatisation du réseau WAN virtuel Azure pour partenaires WAN virtuels | Microsoft Docs
description: Cet article aide les partenaires de solutions de connectivité à définition logicielle à configurer l’automatisation d’un réseau WAN virtuel Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: cherylmc
Customer intent: As a Virtual WAN software-defined connectivity provider, I want to set up a provisioning environment.
ms.openlocfilehash: bac728f286c90550107b27da76a070623577ed82
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918898"
---
# <a name="configure-virtual-wan-automation---for-virtual-wan-partners-preview"></a>Configurer l’automatisation du réseau WAN virtuel pour partenaires WAN virtuel (préversion)

Cet article vous aide à comprendre comment configurer l’environnement d’automatisation pour connecter et configurer un appareil de branche (un périphérique VPN client local ou SDWAN) pour un réseau WAN virtuel Azure. Si vous fournissez des appareils de branche qui peuvent accommoder une connectivité VPN par IPsec/IKEv2, cet article est pour vous.

Les solutions de connectivité à définition logicielle gèrent généralement leurs appareils de branche à l’aide d’un contrôleur ou d’un centre de provisionnement des appareils. Le contrôleur peut utiliser des API Azure pour automatiser la connectivité au réseau WAN virtuel Azure. Ce type de connexion requiert un réseau SDWAN ou un périphérique VPN local disposant d’une adresse IP publique exposée en externe.

##  <a name="access"></a>Contrôle d’accès

Les clients doivent pouvoir configurer un contrôle d'accès approprié pour le réseau WAN virtuel dans l’interface utilisateur de l’appareil. L’utilisation d’un principal de service Azure est recommandée. Un accès basé sur le principal de service fournit au contrôleur de l’appareil une authentification adéquate pour charger des informations de branche. Pour plus d’informations, consultez [Créer un principal de service](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).

##  <a name="site"></a>Charger les informations de branche

Concevez l’expérience utilisateur pour charger les informations de branche (site local) dans Azure. Les [API REST](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) pour **VPNSite** peuvent être utilisées afin de créer les informations de site dans le réseau WAN virtuel. Vous pouvez fournir tous les appareils VPN/SDWAN de branche, ou sélectionner les personnalisations d’appareil adéquates.

##  <a name="hub"></a>Hub et services

Une fois que l’appareil de branche est chargé dans Azure, le client choisira une sélection de région et/ou services de hub dans le portail Azure, ce qui appelle un ensemble d’opérations pour créer le réseau virtuel du hub et le point de terminaison du VPN dans le hub. La passerelle VPN est une passerelle évolutive dimensionnée en fonction des besoins en bande passante et de connexion.

## <a name="device"></a>Configuration de l'appareil

Dans cette étape, un client qui n’utilise pas un fournisseur doit télécharger manuellement la configuration Azure et l’appliquer à son appareil VPN/SDWAN local. En tant que fournisseur, vous devez automatiser cette étape. Le contrôleur peut appeler l’API REST **GetVpnConfiguration** pour télécharger la configuration Azure qui ressemblera typiquement au fichier suivant :

**Notes sur la configuration**

  * Si les réseaux virtuels Azure sont attachés au hub virtuel, ils apparaîtront en tant que ConnectedSubnets (sous-réseaux connectés).
  * La connectivité VPN utilise une configuration basée sur itinéraires et IKEv2.

### <a name="understanding-the-device-configuration-file"></a>Comprendre le fichier de configuration de périphérique

Le fichier de configuration de périphérique contient les paramètres à utiliser lors de la configuration de votre périphérique VPN sur site. Lorsque vous affichez ce fichier, notez les informations suivantes :

* **vpnSiteConfiguration -** Cette section indique les détails de l’appareil configuré comme un site se connectant au réseau virtuel étendu. Cela inclut le nom et l’adresse IP publique de l’appareil de branche.
* **vpnSiteConnections -** Cette section fournit des informations sur les éléments suivants :

    * **Espace d’adressage** du réseau virtuel du/des hub(s).<br>Exemple :
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Espace d’adressage** des réseaux virtuels qui sont connectés au hub.<br>Exemple :

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **Adresses IP** de la passerelle VPN virtuelle. Étant donné que la passerelle VPN a chaque connexion comprenant des 2 tunnels en configuration actif-actif, vous verrez les deux adresses IP répertoriées dans ce fichier. Dans cet exemple, vous voyez « Instance0 » et « Instance1 » pour chaque site.<br>Exemple :

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

## <a name="default"></a>Stratégies par défaut

### <a name="initiator"></a>Initiateur

Les sections suivantes répertorient les combinaisons de stratégies prises en charge quand Azure représente l’initiateur du tunnel.

**Phase-1**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256, DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* 3DES, SHA1, DH_GROUP_2
* 3DES, SHA_256, DH_GROUP_2

**Phase-2**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256, SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE


### <a name="responder"></a>Répondeur

Les sections suivantes répertorient les combinaisons de stratégies prises en charge quand Azure représente le répondeur du tunnel.

**Phase-1**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256, DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* 3DES, SHA1, DH_GROUP_2
* 3DES, SHA_256, DH_GROUP_2

**Phase-2**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256, SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE
* CBC_DES, SHA_1, PFS_NONE 
* AES_256, SHA_1, PFS_1
* AES_256, SHA_1, PFS_2
* AES_256, SHA_1, PFS_14
* AES_128, SHA_1, PFS_1
* AES_128, SHA_1, PFS_2
* AES_128, SHA_1, PFS_14
* CBC_3DES, SHA_1, PFS_1
* CBC_3DES, SHA_1, PFS_2
* CBC_3DES, SHA_256, PFS_2
* AES_256, SHA_256, PFS_1
* AES_256, SHA_256, PFS_2
* AES_256, SHA_256, PFS_14
* AES_256, SHA_1, PFS_24
* AES_256, SHA_256, PFS_24
* AES_128, SHA_256, PFS_NONE
* AES_128, SHA_256, PFS_1
* AES_128, SHA_256, PFS_2
* AES_128, SHA_256, PFS_14
* CBC_3DES, SHA_1, PFS_14

### <a name="does-everything-need-to-match-between-the-virtual-hub-vpngateway-policy-and-my-on-premises-sdwanvpn-device-or-sd-wan-configuration"></a>Tous les éléments entre la stratégie de passerelle VPN et mes configurations de périphérique VPN/SDWAN local ou SD-WAN doivent-ils correspondre ?

La configuration de votre périphérique VPN/SDWAN local ou SD-WAN doit correspondre aux algorithmes et paramètres suivants spécifiés dans la stratégie IPsec/IKE Azure, ou les contenir. Les durées de vie de l’AS sont uniquement des spécifications locales et n’ont pas besoin de correspondre.

* Algorithme de chiffrement IKE
* Algorithme d’intégrité IKE
* Groupe DH
* Algorithme de chiffrement IPsec
* Algorithme d’intégrité IPsec
* Groupe PFS

## <a name="feedback"></a>Commentaires de la préversion

Nous aimerions recevoir vos commentaires. Envoyez un e-mail à <azurevirtualwan@microsoft.com> pour signaler des problèmes ou pour fournir des commentaires (positifs ou négatifs) sur le WAN virtuel. Ajoutez le nom de votre société entre crochets « [] » dans la ligne Objet. Ajoutez également votre ID d’abonnement, si vous signalez un problème.

## <a name="next-steps"></a>Étapes suivantes

Pour en plus sur le réseau WAN virtuel, consultez [À propos du réseau WAN virtuel Azure](virtual-wan-about.md) et la [FAQ sur le réseau WAN virtuel Azure](virtual-wan-faq.md).
