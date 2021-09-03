---
title: Configurer un VPN de site à site dans un WAN virtuel pour Azure VMware Solution
description: Apprenez à établir un tunnel VPN (IPsec IKEv1 et IKEv2) site à site dans Azure VMware Solution.
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 06/30/2021
ms.openlocfilehash: fc2f62549a9a06122b77e0e8864c029cb6af8029
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122528019"
---
# <a name="configure-a-site-to-site-vpn-in-vwan-for-azure-vmware-solution"></a>Configurer un VPN de site à site dans un WAN virtuel pour Azure VMware Solution

Dans cet article, vous établirez un tunnel de site à site VPN (IPsec IKEv1 et IKEv2) qui se termine dans le hub Microsoft Azure Virtual WAN. Le hub contient la passerelle ExpressRoute d’Azure VMware Solution et la passerelle VPN site à site. Il connecte un appareil VPN local à un point de terminaison Azure VMware Solution.

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="Diagramme montrant l’architecture du tunnel de site à site VPN." border="false":::

## <a name="prerequisites"></a>Prérequis
Vous devez disposer d’une IP publique se terminant sur un périphérique VPN local.

## <a name="create-an-azure-virtual-wan"></a>Créer une ressource Azure Virtual WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-virtual-hub"></a>Créer un hub virtuel

Un hub virtuel est un réseau virtuel qui est créé et utilisé par Virtual WAN. Il s’agit du cœur de votre réseau Virtual WAN au sein d’une région donnée.  Il peut contenir des passerelles pour un itinéraire site à site et ExpressRoute. 

>[!TIP]
>Vous pouvez également [créer une passerelle dans un hub existant](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub).


[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-vpn-gateway"></a>Créer une passerelle VPN 

[!INCLUDE [Create a gateway](../../includes/virtual-wan-tutorial-s2s-gateway-include.md)]


## <a name="create-a-site-to-site-vpn"></a>Créer un VPN site à site

1. Dans le portail Azure, sélectionnez le WAN virtuel que vous avez créé précédemment.

2. Dans la rubrique **Vue d’ensemble** du hub virtuel, sélectionnez **Connectivité** > **VPN (site à site)**  > **Créer un site VPN**.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Capture d’écran de la page Vue d’ensemble du hub virtuel, avec les options VPN (site à site) et Créer un site VPN sélectionnées.":::  
 
3. Sous l’onglet **Informations de base**, renseignez les champs obligatoires. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/site-basics.png" alt-text="Capture d’écran montrant la page Créer un site VPN avec l’onglet Informations de base ouvert." lightbox="../../includes/media/virtual-wan-tutorial-site-include/site-basics.png":::

   * **Région** : précédemment appelée « Emplacement ». Il s’agit de l’emplacement auquel vous souhaitez créer cette ressource de site.
   
   * **Nom** : nom par lequel vous souhaitez faire référence à votre site local.
   
   * **Fournisseur de périphériques** : nom du fournisseur de périphériques VPN ; par exemple, Citrix, Cisco ou Barracuda. Cela peut aider l’équipe Azure à mieux comprendre votre environnement afin d’ajouter des possibilités d’optimisation supplémentaires ou vous aider à résoudre les problèmes.

   * **Espace d’adressage privé** : espace d’adressage IP CIDR situé sur votre site local. Le trafic destiné à cet espace d’adressage est acheminé vers votre site local. Le bloc CIDR n’est requis que si vous n’activez pas [BGP](../vpn-gateway/bgp-howto.md) pour le site.
    
   >[!NOTE]
   >Si vous modifiez l’espace d’adressage après avoir créé le site (par exemple, en ajoutant un espace d’adressage), la mise à jour des itinéraires effectifs pendant la recréation des composants peut prendre de 8 à 10 minutes.


1. Sélectionnez **Liens** pour ajouter des informations sur les liens physiques au niveau de la branche. Si vous disposez de l’appareil CPE Virtual WAN d’un partenaire, vérifiez auprès de ce dernier que ces informations sont échangées avec Azure dans le cadre de la configuration du chargement des informations de branche à partir de ses systèmes.

   La spécification de noms de liens et de fournisseurs vous permet de faire la distinction entre un nombre quelconque de passerelles susceptibles d’être créées dans le cadre du hub.  [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) et le numéro de système autonome (ASN) doivent être uniques au sein de votre organisation. BGP garantit qu’Azure VMware Solution et les serveurs locaux publient leurs routes à travers le tunnel. Si elle est désactivée, les sous-réseaux devant être publiés doivent être gérés manuellement. Si les sous-réseaux manquent, HCX ne parvient pas à former la maille de services. 
 
   >[!IMPORTANT]
   >Par défaut, Azure affecte automatiquement une adresse IP privée de la plage de préfixes GatewaySubnet en tant qu’adresse IP BGP Azure sur la passerelle VPN Azure. L’adresse BGP Azure APIPA personnalisée est nécessaire lorsque vos appareils VPN locaux utilisent une adresse APIPA (169.254.0.1 à 169.254.255.254) comme adresse IP BGP. La passerelle VPN Azure choisit l’adresse APIPA personnalisée si la ressource de passerelle de réseau local correspondante (réseau local) a une adresse APIPA en tant qu’adresse IP d’homologue BGP. Si la passerelle de réseau local utilise une adresse IP normale (non APIPA), la passerelle VPN Azure revient à l’adresse IP privée de la plage GatewaySubnet.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/site-links.png" alt-text="Capture d’écran montrant la page Créer un site VPN avec l’onglet Liens ouvert." lightbox="../../includes/media/virtual-wan-tutorial-site-include/site-links.png":::

1. Sélectionnez **Revoir + créer**. 

1. Accédez au hub virtuel de votre choix, puis désélectionnez **Association de hub** pour connecter votre site VPN au hub.
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="Capture d’écran montrant l’option Se connecter à ce hub." lightbox="../../includes/media/virtual-wan-tutorial-site-include/connect.png":::   

## <a name="optional-create-policy-based-vpn-site-to-site-tunnels"></a>(Facultatif) Créer des tunnels VPN site à site basés sur des stratégies

>[!IMPORTANT]
>Cette étape est facultative et s’applique uniquement aux VPN basés sur des stratégies. 

Les [configurations VPN basées sur des stratégies](../virtual-wan/virtual-wan-custom-ipsec-portal.md) nécessitent que les réseaux locaux et Azure VMware Solution soient spécifiés, y compris les plages de hubs.  Ces plages spécifient le domaine de chiffrement du point de terminaison local du tunnel VPN basé sur des stratégies.  Le côté Azure VMware Solution requiert uniquement l’activation de l’indicateur du sélecteur de trafic basé sur des stratégies. 

1. Dans le Portail Azure, accédez à votre site de hub Virtual WAN. Sous **Connectivité**, sélectionnez **VPN (site à site)** .

2. Sélectionnez le site VPN pour lequel vous voulez configurer une stratégie IPsec personnalisée.

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/locate.png" alt-text="Capture d’écran montrant les sites VPN existants pour configurer les stratégies IPsec du client." lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/locate.png":::

3. Sélectionnez le nom de votre site VPN, sélectionnez **More** (...) tout à droite. Sélectionnez ensuite **Modifier la connexion VPN**.

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/contextmenu.png" alt-text="Capture d’écran montrant le menu contextuel d’un site VPN existant." lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/contextmenu.png":::

   - Sécurité du protocole Internet (IPSec), sélectionnez **Personnalisé**.

   - Utilisez le sélecteur de trafic basé sur des stratégies, sélectionnez **Activer**

   - Spécifiez les détails pour **IKE Phase 1** et **IKE Phase 2 (IPSec)** . 

4. Remplacez la valeur Par défaut du paramètre IPsec par Personnalisé et personnalisez la stratégie IPsec. Ensuite, sélectionnez **Enregistrer**.

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/edit.png" alt-text="Capture d’écran montrant les sites VPN existants." lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/edit.png":::

   Les sélecteurs de trafic ou les sous-réseaux qui font partie du domaine de chiffrement basé sur des stratégies doivent être :
    
   - Hub Virtual WAN `/24`

   - Cloud privé Azure VMware Solution `/22`

   - Réseau virtuel Azure connecté (le cas échéant)

## <a name="connect-your-vpn-site-to-the-hub"></a>Connexion de votre site VPN au hub

1. Sélectionnez le nom de votre site VPN, puis sélectionnez **Connecter des sites VPN**. 

1. Dans le champ **Clé prépartagée**, entrez la clé précédemment définie pour le point de terminaison local. 

   >[!TIP]
   >Si vous n’avez pas de clé déjà définie, vous pouvez laisser ce champ vide. Une clé est générée automatiquement pour vous. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="Capture d’écran montrant le volet Sites connectés pour le hub virtuel, prêt pour une clé prépartagée et les paramètres associés."::: 

1. Si vous déployez un pare-feu dans le hub et qu’il s’agit du tronçon suivant, définissez l’option **Propager l’itinéraire par défaut** sur **Activer**. 

   Lorsque cette option est activée, le hub Virtual WAN propage l’itinéraire par défaut à une connexion uniquement si le hub a déjà appris l’itinéraire par défaut lors du déploiement d’un pare-feu dans le hub ou si le tunneling forcé est activé sur un autre site connecté. L’itinéraire par défaut ne provient pas du hub Virtual WAN.  

1. Sélectionnez **Connecter**. Après quelques minutes, le site affiche la connexion et l’état de la connectivité.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="Capture d’écran montrant une connexion de site à site et un état de connectivité." lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

   **État de la connexion :** état de la ressource Azure pour la connexion qui connecte le site VPN à la passerelle VPN du hub Azure. Une fois cette opération de plan de contrôle réussie, la passerelle VPN Azure et le périphérique VPN local établissent la connectivité.

   **État de la connectivité :** état de connectivité réel (chemin d’accès aux données) entre la passerelle VPN d’Azure dans le hub et le site VPN. Il peut afficher l’un des états suivants :

    * **Inconnu**: généralement observé si les systèmes principaux travaillent à la transition vers un autre état.
    * **Connexion en cours** : La passerelle VPN Azure tente d’accéder au site VPN local réel.
    * **Connecté** : la connectivité est établie entre la passerelle VPN Azure et le site VPN local.
    * **Déconnecté**: généralement affiché en cas de déconnexion pour une raison quelconque (localement ou dans Azure)



1. Téléchargez le fichier de configuration du VPN et appliquez-le au point de terminaison local.  
   
   1. Sur la page VPN (site à site), dans la partie supérieure, sélectionnez **Télécharger la configuration VPN**. Azure crée un compte de stockage dans le groupe de ressources « microsoft-network-\[location\] », où location est l’emplacement du réseau étendu. Une fois que vous avez appliqué la configuration à vos périphériques VPN, vous pouvez supprimer ce compte de stockage.

   1. Une fois le fichier créé, sélectionnez le lien pour le télécharger. 

   1. Appliquez la configuration à votre périphérique VPN local.

   Pour plus d’informations sur le fichier de configuration, consultez [À propos du fichier de configuration de périphérique VPN](../virtual-wan/virtual-wan-site-to-site-portal.md#config-file).

1. Appliquez un correctif à ExpressRoute pour Azure VMware Solution dans le hub Virtual WAN. 

   >[!IMPORTANT]
   >Vous devez d’abord avoir créé un cloud privé avant de pouvoir corriger la plateforme. 


   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

1. Liez Azure VMware Solution et la passerelle VPN dans le hub Virtual WAN. Vous allez utiliser la clé d’autorisation et l’ID ExpressRoute (URI du circuit pair) de l’étape précédente.

   1. Sélectionnez votre passerelle ExpressRoute, puis sélectionnez **Accepter la clé d’autorisation**.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Capture d’écran de la page ExpressRoute pour le cloud privé, avec mise en évidence de l’option Utiliser la clé d’autorisation.":::

   1. Collez la clé d’autorisation dans le champ **Clé d’autorisation**.

   1. Collez l’ID ExpressRoute dans le champ **URI du circuit pair**. 

   1. Cochez la case **Associez automatiquement ce circuit ExpressRoute au hub**. 

   1. Sélectionnez **Ajouter** pour établir le lien. 

1. Testez votre connexion en [créant un segment NSX-T](./tutorial-nsx-t-network-segment.md) et en provisionnant une machine virtuelle sur le réseau. Effectuez un test Ping à la fois sur les points de terminaison Azure VMware Solution et sur les points de terminaison locaux.

   >[!NOTE]
   >Attendez environ 5 minutes avant de tester la connectivité à partir d’un client situé derrière votre circuit ExpressRoute, par exemple, une machine virtuelle dans le réseau virtuel que vous avez créée précédemment.
