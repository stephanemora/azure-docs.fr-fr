---
title: Créer un tunnel IPSec dans Azure VMware Solution
description: Découvrez comment créer un hub Virtual WAN pour établir un tunnel IPSec dans Azure VMware Solution.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 21df674862b65ef6573a8a3fcfd7538b1053f04e
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491836"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Créer un tunnel IPSec dans Azure VMware Solution

Dans cet article, nous allons suivre les étapes permettant d’établir un tunnel de site à site VPN (IPsec IKEv1 et IKEv2) qui se termine dans le hub Microsoft Azure Virtual WAN. Nous allons créer un hub Azure Virtual WAN et une passerelle VPN avec une adresse IP publique associée. Ensuite, nous allons créer une passerelle Azure ExpressRoute et établir un point de terminaison Azure VMware Solution. Nous allons également examiner les détails de l’activation d’une configuration VPN basée sur une stratégie. 

## <a name="topology"></a>Topologie

![Diagramme montrant l’architecture du tunnel de site à site VPN.](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

Le hub virtuel Azure contient la passerelle ExpressRoute d’Azure VMware Solution et la passerelle VPN de site à site. Il connecte un appareil VPN local à un point de terminaison Azure VMware Solution.

## <a name="before-you-begin"></a>Avant de commencer

Pour créer le tunnel VPN de site à site, vous devez créer une adresse IP publique qui se termine sur un appareil VPN local.

## <a name="create-a-virtual-wan-hub"></a>Créer un hub Virtual WAN

1. Dans le Portail Azure, recherchez **Virtual WAN**. Sélectionnez **+Ajouter**. La page Créer un WAN s’ouvre.  

2. Entrez les champs requis dans la page **Créer un WAN**, puis sélectionnez **Vérifier + créer**.
   
   | Champ | Valeur |
   | --- | --- |
   | **Abonnement** | Cette valeur est prérenseignée avec l’abonnement appartenant au groupe de ressources. |
   | **Groupe de ressources** | Le Virtual WAN est une ressource globale qui n’est pas limitée à une région spécifique.  |
   | **Emplacement du groupe de ressources** | Pour créer le hub Virtual WAN, vous devez définir un emplacement pour le groupe de ressources.  |
   | **Nom** |   |
   | **Type** | Sélectionnez **Standard**, ce qui permet autorise plus que le seul trafic de la passerelle VPN.  |

   :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="Capture d’écran montrant la page Créer un WAN dans le portail Azure.":::

3. Dans le Portail Azure, sélectionnez le Virtual WAN que vous avez créé à l’étape précédente, sélectionnez **Créer un hub virtuel**, entrez les champs requis, puis sélectionnez **Suivant : Site à site**. 

   | Champ | Valeur |
   | --- | --- |
   | **Région** | La sélection d’une région est requise du point de vue de la gestion.  |
   | **Nom** |    |
   | **Espace d’adressage privé du hub** | Entrez le sous-réseau à l’aide d’un `/24` (minimum).  |

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Capture d’écran montrant la page Créer un hub virtuel.":::

4. Sous l’onglet **Site à site**, définissez la passerelle de site à site en définissant le débit agrégé à partir de la liste déroulante **Unités d’échelle de la passerelle**. 

   >[!TIP]
   >Une unité d’échelle = 500 Mbits/s. Les unités d’échelle sont en paire pour la redondance, chacune prenant en charge 500 Mbits/s.
  
5. Sous l’onglet **ExpressRoute**, créez une passerelle ExpressRoute. 

   >[!TIP]
   >Une valeur d’unité d’échelle est de 2 Gbits/s. 

    La création de chaque hub prend environ 30 minutes. 

## <a name="create-a-vpn-site"></a>Créer un site VPN 

1. Dans **Ressources récentes** dans le Portail Azure, sélectionnez le WAN virtuel que vous avez créé dans la section précédente.

2. Dans la **Vue d’ensemble** du hub virtuel, sélectionnez **Connectivité** > **VPN (site à site)** , puis sélectionnez **Créer un site VPN**.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Capture d’écran de la page Vue d’ensemble du hub virtuel, avec les options VPN (site à site) et Créer un site VPN sélectionnées.":::  
 
3. Sous l’onglet **Général**, entrez les champs requis, puis sélectionnez **Suivant : Liens**. 

   | Champ | Valeur |
   | --- | --- |
   | **Région** | La même région que celle que vous avez spécifiée dans la section précédente.  |
   | **Nom** |  |
   | **Fournisseur de l’appareil** |  |
   | **Border Gateway Protocol** | Affectez la valeur **Activer** pour garantir qu’Azure VMware Solution et les serveurs locaux publient leurs itinéraires à travers le tunnel. Si elle est désactivée, les sous-réseaux devant être publiés doivent être gérés manuellement. Si les sous-réseaux manquent, HCX ne parviendra pas à former la maille de services. Pour plus d’informations, consultez [À propos de BGP avec la passerelle VPN Azure](../vpn-gateway/vpn-gateway-bgp-overview.md). |
   | **Espace d’adressage privé**  | Entrez le bloc CIDR local.  Il est utilisé pour acheminer tout le trafic lié local sur le tunnel.  Le bloc CIDR n’est requis que si vous n’activez pas BGP. |
   | **Se connecter à** |   |

4. Sous l’onglet **Liens**, renseignez les champs requis et sélectionnez **Vérifier + créer**. La spécification de noms de liens et de fournisseurs vous permet de faire la distinction entre un nombre quelconque de passerelles susceptibles d’être créées dans le cadre du hub. BGP et le numéro de système autonome (ASN) doivent être uniques au sein de votre organisation.
 
## <a name="optional-defining-a-vpn-site-for-policy-based-vpn-site-to-site-tunnels"></a>(Facultatif) Définition d’un site VPN pour les tunnels de site à site VPN basés sur des stratégies

Cette section s’applique uniquement aux VPN basés sur des stratégies. Les configurations VPN basées sur des stratégies (ou itinéraires statiques) reposent sur des fonctionnalités d’appareil VPN sur site dans la plupart des cas. Elles nécessitent la spécification de réseaux locaux et Azure VMware Solution. Pour Azure VMware Solution avec un hub Azure Virtual WAN, vous ne pouvez sélectionner *aucun* réseau. Au lieu de cela, vous devez spécifier toutes les plages de hub Virtual WAN local et Azure VMware Solution appropriées. Ces plages de hub sont utilisées pour spécifier le domaine de chiffrement du point de terminaison local du tunnel VPN de base de stratégie. Le côté Azure VMware Solution requiert uniquement l’activation de l’indicateur du sélecteur de trafic basé sur des stratégies. 

1. Dans le portail Azure, accédez à votre site de hub Virtual WAN. Sous **Connectivité**, sélectionnez **VPN (site à site)** .

2. Sélectionnez le nom de votre site VPN, puis les points de suspension (...) tout à droite. Sélectionnez ensuite **Modifier la connexion VPN à ce hub**.
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Capture d’écran de la page dans Azure du site du hub Virtual WAN mettant en évidence les points de suspension qui permettent d’accéder à Modifier la connexion VPN à ce hub." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Modifiez la connexion entre le site VPN et le hub, puis sélectionnez **Enregistrer**.
   - Sécurité du protocole Internet (IPSec), sélectionnez **Personnalisé**.
   - Utilisez le sélecteur de trafic basé sur des stratégies, sélectionnez **Activer**
   - Spécifiez les détails pour **IKE Phase 1** et **IKE Phase 2 (IPSec)** . 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Capture d’écran de la page Modifier la connexion VPN."::: 
 
   Les sélecteurs de trafic ou les sous-réseaux qui font partie du domaine de chiffrement basé sur des stratégies doivent être :
    
   - Le hub Virtual WAN /24
   - Le coud privé Azure VMware Solution /22
   - Le réseau virtuel Azure connecté (le cas échéant)

## <a name="connect-your-vpn-site-to-the-hub"></a>Connexion de votre site VPN au hub

1. Sélectionnez le nom de votre site VPN, puis sélectionnez **Connecter des sites VPN**. 
1. Dans le champ **Clé prépartagée**, entrez la clé précédemment définie pour le point de terminaison local. 

   >[!TIP]
   >Si vous n’avez pas de clé déjà définie, vous pouvez laisser ce champ vide. Une clé est générée automatiquement pour vous. 
 
   >[!IMPORTANT]
   >Activez uniquement **Propager la route par défaut** si vous déployez un pare-feu dans le hub et qu’il s’agit du tronçon suivant pour les connexions via ce tunnel.

1. Sélectionnez **Connecter**. Un écran d’état de la connexion indique l’état de la création du tunnel.

2. Accédez à la vue d’ensemble du Virtual WAN et ouvrez la page du site VPN pour télécharger le fichier de configuration VPN pour le point de terminaison local.  

3. Appliquez un correctif à ExpressRoute pour Azure VMware Solution dans le hub Virtual WAN. Cette étape nécessite la création d’un cloud privé au préalable.

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Liez Azure VMware Solution et la passerelle VPN dans le hub Virtual WAN. 
   1. Dans le Portail Azure, ouvrez le Virtual WAN que vous avez créé précédemment. 
   1. Sélectionnez le hub Virtual WAN créé, puis sélectionnez **ExpressRoute** dans le volet gauche. 
   1. Sélectionnez **+ Utiliser la clé d’autorisation**.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Capture d’écran de la page ExpressRoute pour le cloud privé, avec mise en évidence de l’option Utiliser la clé d’autorisation.":::

   1. Collez la clé d’autorisation dans le champ Clé d’autorisation.
   1. Collez l’ID ExpressRoute dans le champ **URI du circuit pair**. 
   1. Sélectionnez **Associez automatiquement ce circuit ExpressRoute au hub.** 
   1. Sélectionnez **Ajouter** pour établir le lien. 

5. Testez votre connexion en [créant un segment NSX-T](./tutorial-nsx-t-network-segment.md) et en provisionnant une machine virtuelle sur le réseau. Effectuez un test Ping à la fois sur les points de terminaison Azure VMware Solution et sur les points de terminaison locaux.
