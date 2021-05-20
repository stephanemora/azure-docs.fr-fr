---
title: Configurer un VPN de site à site dans un WAN virtuel pour Azure VMware Solution
description: Apprenez à établir un tunnel VPN (IPsec IKEv1 et IKEv2) site à site dans Azure VMware Solution.
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 4d410a94b822db8eeed0ba166908c804a1a6eaaa
ms.sourcegitcommit: 2cb7772f60599e065fff13fdecd795cce6500630
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108802771"
---
# <a name="configure-a-site-to-site-vpn-in-vwan-for-azure-vmware-solution"></a>Configurer un VPN de site à site dans un WAN virtuel pour Azure VMware Solution

Dans cet article, nous allons suivre les étapes permettant d’établir un tunnel de site à site VPN (IPsec IKEv1 et IKEv2) qui se termine dans le hub Microsoft Azure Virtual WAN. Le hub contient la passerelle ExpressRoute d’Azure VMware Solution et la passerelle VPN site à site. Il connecte un appareil VPN local à un point de terminaison Azure VMware Solution.

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="Diagramme montrant l’architecture du tunnel de site à site VPN." border="false":::

Dans ce guide pratique, vous allez :
- Créer un hub Azure Virtual WAN et une passerelle VPN à laquelle est rattachée une IP publique. 
- Créer une passerelle Azure ExpressRoute et établir un point de terminaison Azure VMware Solution. 
- Activer une configuration VPN locale basée sur des stratégies. 

## <a name="prerequisites"></a>Prérequis
Vous devez disposer d’une IP publique se terminant sur un périphérique VPN local.

## <a name="step-1-create-an-azure-virtual-wan"></a>Étape 1. Créer une ressource Azure Virtual WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="step-2-create-a-virtual-wan-hub-and-gateway"></a>Étape 2. Créer un hub et une passerelle Virtual WAN

>[!TIP]
>Vous pouvez également [créer une passerelle dans un hub existant](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub).

1. Sélectionnez la ressource Virtual WAN créée à l’étape précédente.

1. Sélectionnez **Créer un hub virtuel**, renseignez les champs obligatoires, puis sélectionnez **Suivant : Site à site**. 

   Entrez le sous-réseau à l’aide d’un `/24` (minimum).

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Capture d’écran montrant la page Créer un hub virtuel.":::

4. Sélectionnez l’onglet **Site à site**, définissez la passerelle site à site en paramétrant le débit agrégé dans la liste déroulante **Unités d’échelle de la passerelle**. 

   >[!TIP]
   >Les unités d’échelle sont par paires pour la redondance, chacune prenant en charge 500 Mbits/s (une unité d’échelle = 500 Mbits/s). 
  
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-hub-include/site-to-site.png" alt-text="Capture d’écran montrant les informations site à site.":::

5. Sélectionnez l’onglet **ExpressRoute** et créez une passerelle ExpressRoute. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-er-hub-include/hub2.png" alt-text="Capture d’écran des paramètres ExpressRoute.":::

   >[!TIP]
   >Une valeur d’unité d’échelle est de 2 Gbits/s. 

    La création de chaque hub prend environ 30 minutes. 

## <a name="step-3-create-a-site-to-site-vpn"></a>Étape 3. Créer un VPN site à site

1. Dans le portail Azure, sélectionnez le WAN virtuel que vous avez créé précédemment.

2. Dans la rubrique **Vue d’ensemble** du hub virtuel, sélectionnez **Connectivité** > **VPN (site à site)**  > **Créer un site VPN**.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Capture d’écran de la page Vue d’ensemble du hub virtuel, avec les options VPN (site à site) et Créer un site VPN sélectionnées.":::  
 
3. Sous l’onglet **Informations de base**, renseignez les champs obligatoires. 

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics2.png" alt-text="Capture d’écran de l’onglet Informations de base pour le nouveau site VPN.":::  

   1. Définissez **Border Gateway Protocol** sur **Activer**.  Lorsqu’il est activé, il garantit qu’Azure VMware Solution et les serveurs locaux publient leurs itinéraires à travers le tunnel. Si elle est désactivée, les sous-réseaux devant être publiés doivent être gérés manuellement. Si les sous-réseaux manquent, HCX ne parviendra pas à former la maille de services. Pour plus d’informations, consultez [À propos de BGP avec la passerelle VPN Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).
   
   1. Pour l’**espace d’adressage privé**, entrez le bloc CIDR local. Il est utilisé pour acheminer tout le trafic lié local sur le tunnel. Le bloc CIDR n’est requis que si vous n’activez pas BGP.

1. Sélectionnez **Suivant : Liens** et renseignez les champs obligatoires. La spécification de noms de liens et de fournisseurs vous permet de faire la distinction entre un nombre quelconque de passerelles susceptibles d’être créées dans le cadre du hub. BGP et le numéro de système autonome (ASN) doivent être uniques au sein de votre organisation.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-links.png" alt-text="Capture d’écran montrant les détails du lien.":::

1. Sélectionnez **Revoir + créer**. 

1. Accédez au hub virtuel de votre choix, puis désélectionnez **Association de hub** pour connecter votre site VPN au hub.
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="Capture d’écran montrant le volet Sites connectés pour le hub virtuel, prêt pour une clé prépartagée et les paramètres associés.":::   

## <a name="step-4-optional-create-policy-based-vpn-site-to-site-tunnels"></a>Étape 4. (Facultatif) Créer des tunnels VPN site à site basés sur des stratégies

>[!IMPORTANT]
>Cette étape est facultative et s’applique uniquement aux VPN basés sur des stratégies. 

Les configurations VPN basées sur des stratégies nécessitent que les réseaux locaux et Azure VMware Solution soient spécifiés, y compris les plages de hubs.  Ces plages de hubs spécifient le domaine de chiffrement du point de terminaison local du tunnel VPN basé sur des stratégies.  Le côté Azure VMware Solution requiert uniquement l’activation de l’indicateur du sélecteur de trafic basé sur des stratégies. 

1. Dans le portail Azure, accédez à votre site de hub Virtual WAN. Sous **Connectivité**, sélectionnez **VPN (site à site)** .

2. Sélectionnez le nom de votre site VPN, puis les points de suspension (...) tout à droite. Sélectionnez ensuite **Modifier la connexion VPN à ce hub**.
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Capture d’écran de la page dans Azure du site du hub Virtual WAN mettant en évidence les points de suspension qui permettent d’accéder à Modifier la connexion VPN à ce hub." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Modifiez la connexion entre le site VPN et le hub, puis sélectionnez **Enregistrer**.
   - Sécurité du protocole Internet (IPSec), sélectionnez **Personnalisé**.
   - Utilisez le sélecteur de trafic basé sur des stratégies, sélectionnez **Activer**
   - Spécifiez les détails pour **IKE Phase 1** et **IKE Phase 2 (IPSec)** . 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Capture d’écran de la page Modifier la connexion VPN."::: 
 
   Les sélecteurs de trafic ou les sous-réseaux qui font partie du domaine de chiffrement basé sur des stratégies doivent être :
    
   - Hub Virtual WAN `/24`
   - Cloud privé Azure VMware Solution `/22`
   - Réseau virtuel Azure connecté (le cas échéant)

## <a name="step-5-connect-your-vpn-site-to-the-hub"></a>Étape 5. Connexion de votre site VPN au hub

1. Sélectionnez le nom de votre site VPN, puis sélectionnez **Connecter des sites VPN**. 

1. Dans le champ **Clé prépartagée**, entrez la clé précédemment définie pour le point de terminaison local. 

   >[!TIP]
   >Si vous n’avez pas de clé déjà définie, vous pouvez laisser ce champ vide. Une clé est générée automatiquement pour vous. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="Capture d’écran montrant le volet Sites connectés pour le hub virtuel, prêt pour une clé prépartagée et les paramètres associés."::: 

1. Si vous déployez un pare-feu dans le hub et qu’il s’agit du tronçon suivant, définissez l’option **Propager l’itinéraire par défaut** sur **Activer**. 

   Lorsque cette option est activée, le hub Virtual WAN propage l’itinéraire par défaut à une connexion uniquement si le hub a déjà appris l’itinéraire par défaut lors du déploiement d’un pare-feu dans le hub ou si le tunneling forcé est activé sur un autre site connecté. L’itinéraire par défaut ne provient pas du hub Virtual WAN.  

1. Sélectionnez **Connecter**. Après quelques minutes, le site affiche la connexion et l’état de la connectivité.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="Capture d’écran montrant une connexion de site à site et un état de connectivité." lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

1. [Téléchargez le fichier config du VPN](../virtual-wan/virtual-wan-site-to-site-portal.md#device) pour le point de terminaison local.  

3. Appliquez un correctif à ExpressRoute pour Azure VMware Solution dans le hub Virtual WAN. 

   >[!IMPORTANT]
   >Vous devez d’abord avoir créé un cloud privé avant de pouvoir corriger la plateforme. 

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Liez Azure VMware Solution et la passerelle VPN dans le hub Virtual WAN. Vous allez utiliser la clé d’autorisation et l’ID ExpressRoute (URI du circuit pair) de l’étape précédente.

   1. Sélectionnez votre passerelle ExpressRoute, puis sélectionnez **Accepter la clé d’autorisation**.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Capture d’écran de la page ExpressRoute pour le cloud privé, avec mise en évidence de l’option Utiliser la clé d’autorisation.":::

   1. Collez la clé d’autorisation dans le champ **Clé d’autorisation**.
   1. Collez l’ID ExpressRoute dans le champ **URI du circuit pair**. 
   1. Cochez la case **Associez automatiquement ce circuit ExpressRoute au hub**. 
   1. Sélectionnez **Ajouter** pour établir le lien. 

5. Testez votre connexion en [créant un segment NSX-T](./tutorial-nsx-t-network-segment.md) et en provisionnant une machine virtuelle sur le réseau. Effectuez un test Ping à la fois sur les points de terminaison Azure VMware Solution et sur les points de terminaison locaux.
