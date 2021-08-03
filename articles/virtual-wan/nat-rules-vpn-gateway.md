---
title: Configurer des règles NAT de VPN pour votre passerelle
titleSuffix: Azure Virtual WAN
description: Découvrez comment configurer des règles NAT pour votre passerelle VPN VWAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: fc6c9f6c7c69f92c66706678dd27e082412afa3e
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110539169"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>Configurer des règles NAT pour votre passerelle VPN de Virtual WAN – Préversion

> [!IMPORTANT]
> Les règles NAT sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez configurer votre passerelle VPN de Virtual WAN avec des règles NAT statiques un-à-un. Une règle NAT fournit un mécanisme permettant de configurer la traduction un-à-un des adresses IP. Vous pouvez une NAT pour interconnecter deux réseaux IP dont les adresses IP sont incompatibles ou se chevauchent. Un scénario classique est celui des branches avec des adresses IP se chevauchant, qui souhaitent accéder aux ressources de réseau virtuel Azure.

Cette configuration utilise une table de flux pour router le trafic d’une adresse IP externe (hôte) vers une adresse IP interne associée à un point de terminaison à l’intérieur d’un réseau virtuel (machine virtuelle, ordinateur, conteneur, etc.).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagramme montrant une architecture.":::
   
Pour utiliser la traduction d’adresses réseau (NAT), les périphériques VPN doivent utiliser des sélecteurs de trafic Any-To-Any (générique). Les sélecteurs de trafic basés sur des stratégies (étroits) ne sont pas pris en charge conjointement à la configuration NAT.

## <a name="configure-nat-rules"></a><a name="rules"></a>Configurer les règles NAT

Vous pouvez configurer et afficher des règles NAT sur les paramètres de votre passerelle VPN à tout moment.

> [!NOTE]
> La NAT site à site n’est pas prise en charge avec les connexions VPN site à site où des sélecteurs de trafic basés sur des stratégies sont utilisés.

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="Capture d’écran montrant comment modifier des règles."lightbox="./media/nat-rules-vpn-gateway/edit-rules.png":::
1. Accédez à votre hub virtuel.
1. Sélectionnez **VPN (site à site)** .
1. Sélectionnez **Règles NAT (Modifier)** .
1. Sur la page **Modifier la règle NAT**, vous pouvez **Ajouter/Modifier/Supprimer** une règle NAT à l’aide des valeurs suivantes :

   * **Nom :** nom unique de votre règle NAT.
   * **Type :** Statique. Une NAT statique un-à-un établit une relation un-à-un entre une adresse interne et une adresse externe.
   * **Mode :** IngressSnat ou EgressSnat.  
      * Le mode IngressSnat (également appelé NAT de source d’entrée) s’applique au trafic entrant dans la passerelle VPN de site à site du hub Azure. 
      * Le mode EgressSnat (également appelé NAT de source de sortie) s’applique au trafic sortant de la passerelle VPN de site à site du hub Azure. 
   * **InternalMapping :** plage de préfixes d’adresse des adresses IP sources sur le réseau interne qui sera mappé à un ensemble d’adresses IP externes. En d’autres termes, votre plage de préfixe d’adresse avant NAT.
   * **ExternalMapping :** plage de préfixes d’adresse des adresses IP de destination sur le réseau externe auquel les adresses IP sources seront mappées. En d’autres termes, votre plage de préfixe d’adresse après NAT.
   * **Connexion de liaison :** ressource de connexion qui connecte virtuellement un site VPN à la passerelle VPN site à site du hub Azure Virtual WAN.
 
> [!NOTE]
> Si vous souhaitez que la passerelle VPN site à site publie des préfixes d’adresse traduits (**ExternalMapping**) via BGP, cliquez sur le bouton **Activer la traduction BGP**, grâce auquel le site local apprendra automatiquement la plage après NAT des règles de sortie et le site Azure (hub Virtual WAN, réseaux virtuels connectés, branches VPN et ExpressRoute) apprendra automatiquement la plage après NAT des règles d’entrée. Notez que le paramètre **Activer la traduction BGP** est appliqué à toutes les règles NAT sur la passerelle VPN site à site du hub Virtual WAN. 

## <a name="example-configurations"></a><a name="examples"></a>Exemples de configurations

### <a name="ingress-snat-bgp-enabled-vpn-site"></a>SNAT d’entrée (site VPN avec BGP)

Les **règles SNAT d’entrée** sont appliquées aux paquets qui entrent dans Azure via la passerelle VPN site à site de Virtual WAN. Dans ce scénario, vous voulez connecter deux branches VPN de site à site à Azure. Site VPN 1 se connecte via Liaison A, et Site VPN 2 se connecte via Liaison B. Chaque site a le même espace d’adressage 10.30.0.0/24.

Dans cet exemple, nous allons traduire l’adresse réseau de site1 vers 127.30.0.0.0/24. Les réseaux virtuels spoke et autres branches de Virtual WAN apprendront automatiquement cet espace d’adressage après NAT. 

Le diagramme suivant présente le résultat final projeté :

:::image type="content" source="./media/nat-rules-vpn-gateway/diagram-bgp.png" alt-text="Diagramme montrant la NAT en mode Entrée pour les sites avec BGP.":::

1. Spécifiez une règle NAT.

   Spécifiez une règle NAT pour garantir que la passerelle VPN site à site est en mesure de faire la distinction entre les deux branches avec des espaces d’adressage qui se chevauchent (comme 10.30.0.0/24). Dans cet exemple, nous nous concentrons sur Liaison A pour Site VPN 1.

   La règle NAT suivante peut être configurée et associée à Liaison A. Étant donné qu’il s’agit d’une règle NAT statique, les espaces d’adressage des plages **InternalMapping** et **ExternalMapping** contiennent le même nombre d’adresses IP.

   * **Nom :** ingressRule01
   * **Type :** Statique
   * **Mode :** IngressSnat
   * **InternalMapping :** 10.30.0.0/24
   * **ExternalMapping :** 172.30.0.0/24
   * **Connexion de liaison :** Liaison A

 2.  Basculez le paramètre **Traduction d’itinéraires BGP** sur « Activer ».

       :::image type="content" source="./media/nat-rules-vpn-gateway/enable-bgp.png" alt-text="Capture d’écran montrant comment activer la traduction BGP.":::


3. Vérifiez que la passerelle VPN site à site peut être associée à l’homologue BGP local.

      Dans cet exemple, la **règle NAT d’entrée** doit traduire 10.30.0.132 en 127.30.0.132. Pour ce faire, cliquez sur « Modifier le site VPN » pour configurer l’adresse BGP de Liaison A du site VPN pour refléter cette adresse d’homologue BGP traduite (127.30.0.132). 

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site-bgp.png" alt-text="Capture d’écran montrant comment modifier l’adresse IP de l’homologue BGP."lightbox="./media/nat-rules-vpn-gateway/edit-site-bgp.png":::

 

### <a name="considerations-if-the-vpn-site-connects-via-bgp"></a><a name="considerations"> </a>Considérations relatives à la connexion du site VPN via BGP
* La taille du sous-réseau pour le mappage interne et externe doit être la même pour la NAT statique un-à-un.
* Si la **traduction BGP** est activée, la passerelle VPN site à site publiera automatiquement la plage **ExternalMapping** des **règles NAT de sortie** vers le site local, ainsi que la plage **ExternalMapping** des **règles NAT d’entrée** vers Azure (hub Virtual WAN, réseaux virtuels spoke connectés, connexions VPN/ExpresRoute). Si la **traduction BGP** est désactivée, les itinéraires traduits ne sont pas publiés automatiquement sur le site local. Ainsi, le locuteur BGP local doit être configuré pour publier la plage après NAT (**ExternalMapping**) des règles **NAT d’entrée** associées à cette connexion de liaison de site VPN. De même, un itinéraire pour la plage après NAT (**ExternalMapping**) des **règles NAT de sortie** doit être appliqué sur l’appareil local.
* La passerelle VPN site à site traduit automatiquement l’adresse IP de l’homologue BGP local **si** celle-ci est contenue dans la plage **InternalMapping** d’une **règle NAT d’entrée**. Par conséquent, l’**adresse BGP de la connexion de liaison** du site VPN doit refléter l’adresse traduite par la NAT (qui fait partie de la plage ExternalMapping). 

    Par exemple, si l’adresse IP BGP locale est 10.30.0.133 et qu’il existe une **règle NAT d’entrée** qui traduit 10.30.0.0/24 en 127.30.0.0/24, l’**adresse BGP de la connexion de liaison** du site VPN doit être configurée pour être l’adresse traduite (127.30.0.133).

 
### <a name="ingress-snat-vpn-site-with-statically-configured-routes"></a>SNAT d’entrée (site VPN avec itinéraires configurés statiquement)

Les **règles SNAT d’entrée** sont appliquées aux paquets qui entrent dans Azure via la passerelle VPN site à site de Virtual WAN. Dans ce scénario, vous voulez connecter deux branches VPN de site à site à Azure. Site VPN 1 se connecte via Liaison A, et Site VPN 2 se connecte via Liaison B. Chaque site a le même espace d’adressage 10.30.0.0/24.

Dans cet exemple, nous allons traduire l’adresse réseau de Site VPN 1 en 127.30.0.0.0/24. Toutefois, comme le site VPN n’est pas connecté à la passerelle VPN site à site via BGP, les étapes de configuration sont légèrement différentes de celles de l’exemple avec BGP. 

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram-static.png" alt-text="Capture d’écran montrant les configurations de diagramme pour les sites VPN qui utilisent le routage statique.":::


1. Spécifiez une règle NAT.

    Spécifiez une règle NAT pour garantir que la passerelle VPN site à site est en mesure de faire la distinction entre les deux branches avec le même espace d’adressage 10.30.0.0/24. Dans cet exemple, nous nous concentrons sur Liaison A pour Site VPN 1.

    La règle NAT suivante peut être configurée et associée à Liaison A de l’une des branches de Site VPN 1. Étant donné qu’il s’agit d’une règle NAT statique, les espaces d’adressage des plages **InternalMapping** et **ExternalMapping** contiennent le même nombre d’adresses IP.

    * **Nom** : IngressRule01
    * **Type** : Statique
    * **Mode** : IngressSnat
    * **InternalMapping** : 10.30.0.0/24
    * **ExternalMapping** : 172.30.0.0/24
    * **Connexion de liaison** : Liaison A

2. Modifiez le champ « Espace d’adressage privé » de Site VPN 1 pour vous assurer que la passerelle VPN site à site apprend la plage après NAT (172.30.0.0/24).

   * Accédez à la ressource Hub virtuel qui contient la passerelle VPN de site à site. Dans la page de hub virtuel, sous Connectivité, sélectionnez VPN (site à site) .

   * Sélectionnez le site VPN connecté au hub Virtual WAN via Liaison A. Sélectionnez « Modifier le site » et entrez 172.30.0.0/24 comme espace d’adressage privé pour le site VPN. 

   :::image type="content" source="./media/nat-rules-vpn-gateway/vpn-site-static.png" alt-text="Capture d’écran montrant comment modifier l’espace d’adressage privé d’un site VPN" lightbox="./media/nat-rules-vpn-gateway/vpn-site-static.png":::

### <a name="considerations-if-vpn-sites-are-statically-configured-not-connected-via-bgp"></a><a name="considerationsnobgp"></a>Considérations relatives à la configuration statique des sites VPN (non connectés via BGP)
* La taille du sous-réseau pour le mappage interne et externe doit être la même pour la NAT statique un-à-un.
* Modifiez le site VPN dans le portail Azure pour ajouter les préfixes de la plage **ExternalMapping** des **règles NAT d’entrée** dans le champ « Espace d’adressage privé ». 
* Pour les configurations impliquant des **règles NAT de sortie**, une stratégie de routage ou un itinéraire statique avec la plage **ExternalMapping** de la **règle NAT de sortie** doivent être appliqués sur l’appareil local.

### <a name="packet-flow"></a>Flux de paquets

Dans les exemples précédents, un appareil local souhaite atteindre une ressource dans un réseau virtuel spoke. Le flux de paquets se présente comme suit, avec les traductions NAT en gras.

1. Le trafic en provenance d’un emplacement local est lancé.
   * Adresse IP source : **10.30.0.4**
   * Adresse IP de destination : 10.200.0.4
1. Le trafic entre dans la passerelle de site à site et est traduit à l’aide de la règle NAT, puis envoyé au spoke.
   * Adresse IP source : **172.30.0.4**
   * Adresse IP de destination : 10.200.0.4
1. La réponse du spoke est lancée.
   * Adresse IP source : 10.200.0.4
   * Adresse IP de destination : **172.30.0.4**
1. Le trafic entre dans la passerelle VPN de site à site et la traduction est inversée et envoyée à l’emplacement local.
   * Adresse IP source : 10.200.0.4
   * Adresse IP de destination : **10.30.0.4**

### <a name="verification-checks"></a>Contrôles de vérification

Cette section présente les contrôles permettant de vérifier que votre configuration est correctement définie. 

#### <a name="validate-defaultroutetable-rules-and-routes"></a>Valider DefaultRouteTable, les règles et les routes

Dans Virtual WAN, les branches sont associées à **DefaultRouteTable**, ce qui implique que toutes les connexions de branche découvrent les routes qui sont renseignées dans DefaultRouteTable. La règle NAT dotée du préfixe traduit s’affiche dans les itinéraires effectifs de DefaultRouteTable.

Dans l’exemple précédent :

* **Préfixe :** 172.30.0.0/24  
* **Type du tronçon suivant :** VPN_S2S_Gateway
* **Tronçon suivant :** Ressource VPN_S2S_Gateway

#### <a name="validate-address-prefixes"></a>Valider les préfixes d’adresse

Cet exemple s’applique aux ressources des réseaux virtuels qui sont associés à DefaultRouteTable.

Les **itinéraires effectifs** sur les cartes réseau de toute machine virtuelle qui se trouve dans un réseau virtuel spoke connecté au hub Virtual WAN doivent également contenir les préfixes d’adresse de la plage **ExternalMapping** spécifiée dans la **règle NAT d’entrée**. 

L’appareil local doit également contenir des itinéraires pour les préfixes contenus dans la plage **ExternalMapping** des **règles NAT de sortie**. 

####  <a name="common-configuration-patterns"></a>Modèles de configuration courants 

> [!NOTE]
> La NAT site à site n’est pas prise en charge avec les connexions VPN site à site où des sélecteurs de trafic basés sur des stratégies sont utilisés.

Le tableau suivant présente les modèles de configuration courants qui surviennent lors de la configuration de différents types de règles NAT sur la passerelle VPN site à site.  

| Type de site VPN | Règles NAT d’entrée | Règles NAT de sortie
|--- |--- | ---|
|Site VPN avec itinéraires configurés statiquement |Modifiez le paramètre « Espace d’adressage privé » du site VPN pour contenir la plage **ExternalMapping** de la règle NAT.| Appliquez des itinéraires pour la plage **ExternalMapping** de la règle NAT sur l’appareil local.|
|Site VPN (avec traduction BGP)|Placez l’adresse **ExternalMapping** de l’homologue BGP dans l’adresse BGP de la connexion de liaison du site VPN.  | Aucune considération particulière. |
| Site VPN (sans traduction BGP) | Assurez-vous que le locuteur BGP local publie les préfixes dans la plage **ExternalMapping** de la règle NAT. Placez également l’adresse ExternalMapping de l’homologue BGP dans l’adresse BGP de la connexion de liaison du site VPN.| Appliquez des itinéraires pour la plage **ExternalMapping** de la règle NAT sur l’appareil local.|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les configurations de site à site, consultez [Configurer une connexion de site à site de Virtual WAN](virtual-wan-site-to-site-portal.md).
