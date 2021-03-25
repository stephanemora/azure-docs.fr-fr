---
title: Configurer des règles NAT de VPN pour votre passerelle
titleSuffix: Azure Virtual WAN
description: Découvrez comment configurer des règles NAT pour votre passerelle VPN VWAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 6fbee31f015953bd7e65648ea273e3ca84686115
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431214"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>Configurer des règles NAT pour votre passerelle VPN de Virtual WAN – Préversion

> [!IMPORTANT]
> Les règles NAT sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez configurer votre passerelle VPN de Virtual WAN avec des règles NAT statiques un-à-un. Une règle NAT fournit un mécanisme permettant de configurer la traduction un-à-un des adresses IP. Vous pouvez une NAT pour interconnecter deux réseaux IP dont les adresses IP sont incompatibles ou se chevauchent. Un scénario classique est celui des branches avec des adresses IP se chevauchant, qui souhaitent accéder aux ressources de réseau virtuel Azure.

Cette configuration utilise une table de flux pour router le trafic d’une adresse IP externe (hôte) vers une adresse IP interne associée à un point de terminaison à l’intérieur d’un réseau virtuel (machine virtuelle, ordinateur, conteneur, etc.).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagramme montrant une architecture.":::

## <a name="configure-nat-rules"></a><a name="rules"></a>Configurer les règles NAT

Vous pouvez configurer et afficher des règles NAT sur les paramètres de votre passerelle VPN à tout moment.

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="Capture d’écran montrant la modification de règles.":::

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
   * **Connexion de lien :** Ressource de connexion qui connecte virtuellement un site VPN à la passerelle VPN de site à site du hub Azure.

### <a name="configuration-considerations"></a><a name="considerations"></a>Considérations relatives à la configuration

* La taille du sous-réseau pour le mappage interne et externe doit être la même pour la NAT statique un-à-un.
* Veillez à modifier le site VPN dans le portail Azure pour ajouter des préfixes **ExternalMapping** dans le champ « Espace d’adressage privé ». Actuellement, les sites pour lesquels le protocole BGP est activé doivent s’assurer que l’annonceur BGP local (paramètres BGP de l’appareil) inclut une entrée pour les préfixes de mappage externes.

## <a name="examples-and-verification"></a><a name="examples"></a>Exemples et vérification

### <a name="ingress-mode-nat"></a>NAT en mode d’entrée

Les règles NAT en mode d’entrée sont appliquées aux paquets qui entrent dans Azure via la passerelle VPN de site à site de Virtual WAN. Dans ce scénario, vous voulez connecter deux branches VPN de site à site à Azure. Le Site VPN 1 se connecte via Link 1, et le Site VPN 2 se connecte via Link 2. Chaque site a l’espace d’adressage 192.169.1.0/24.

Le diagramme suivant présente le résultat final projeté :

:::image type="content" source="./media/nat-rules-vpn-gateway/ingress.png" alt-text="Diagramme montrant la NAT en mode d’entrée.":::

1. Spécifiez une règle NAT.

   Spécifiez une règle NAT pour garantir que la passerelle VPN de site à site est en mesure de faire la distinction entre les deux branches avec des espaces d’adressage qui se chevauchent (comme 192.168.1.0/24). Dans cet exemple, nous nous concentrons sur Link 1 pour le Site VPN 1.

   La règle NAT suivante peut être configurée et associée au Link 1 de l’une des branches. Étant donné qu’il s’agit d’une règle NAT statique, les espaces d’adressage d’InternalMapping et d’ExternalMapping contiennent le même nombre d’adresses IP.

   * **Nom :** IngressRule01
   * **Type :** Statique
   * **Mode :** IngressSnat
   * **InternalMapping :** 192.168.1.0/24
   * **ExternalMapping :** 10.1.1.0/24
   * **Connexion de lien :** Link 1

1. Publiez la valeur ExternalMapping appropriée.

   Dans cette étape, vérifiez que votre passerelle VPN de site à site publie l’espace d’adressage ExternalMapping approprié sur le reste de vos ressources Azure. Les instructions sont différentes selon que le protocole BGP est activé ou non.

   **Exemple 1 : Le protocole BGP est activé**

   * Vérifiez que le haut-parleur BGP local situé sur le Site VPN 1 est configuré pour publier l’espace d’adressage 10.1.1.0/24.
   * Pendant cette période de préversion, les sites pour lesquels le protocole BGP est activé doivent garantir que l’annonceur BGP local (paramètres BGP de l’appareil) inclut une entrée pour les préfixes de mappage externes.

   **Exemple 2 : Le protocole BGP n’est pas activé**

   * Accédez à la ressource Hub virtuel qui contient la passerelle VPN de site à site. Dans la page de hub virtuel, sous **Connectivité**, sélectionnez **VPN (site à site)** .
   * Sélectionnez le site VPN connecté au hub Virtual WAN via Link 1. Sélectionnez **Modifier le site**, puis entrez 10.1.1.0/24 comme espace d’adressage privé pour le site VPN.

     :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site.png" alt-text="Capture d’écran montrant la page Modifier le site VPN.":::

### <a name="packet-flow"></a>Flux de paquets

Dans cet exemple, un appareil local veut atteindre un réseau virtuel spoke. Le flux de paquets se présente comme suit, avec les traductions NAT en gras.

1. Le trafic en provenance d’un emplacement local est lancé.
   * Adresse IP source : **192.168.1.1**
   * Adresse IP de destination : 30.0.0.1
1. Le trafic entre dans la passerelle de site à site et est traduit à l’aide de la règle NAT, puis envoyé au spoke.
   * Adresse IP source : **10.1.1.1**
   * Adresse IP de destination : 30.0.0.1
1. La réponse du spoke est lancée.
   * Adresse IP source : 30.0.0.1
   * Adresse IP de destination : **10.1.1.1**
1. Le trafic entre dans la passerelle VPN de site à site et la traduction est inversée et envoyée à l’emplacement local.
   * Adresse IP source : 30.0.0.1
   * Adresse IP de destination : **192.168.1.1**

### <a name="verification-checks"></a>Contrôles de vérification

Cette section présente les contrôles permettant de vérifier que votre configuration est correctement définie.

#### <a name="validate-defaultroutetable-rules-and-routes"></a>Valider DefaultRouteTable, les règles et les routes

Dans Virtual WAN, les branches sont associées à **DefaultRouteTable**, ce qui implique que toutes les connexions de branche découvrent les routes qui sont renseignées dans DefaultRouteTable. La règle NAT dotée du préfixe de mappage externe s’affiche dans les routes effectives de DefaultRouteTable.

Exemple :

* **Préfixe :** 10.1.1.0/24  
* **Type du tronçon suivant :** VPN_S2S_Gateway
* **Tronçon suivant :** Ressource VPN_S2S_Gateway

#### <a name="validate-address-prefixes"></a>Valider les préfixes d’adresse

Cet exemple s’applique aux ressources des réseaux virtuels qui sont associés à DefaultRouteTable.

Les **routes effectives** sur les cartes d’interface réseau d’une machine virtuelle qui se trouve dans un réseau virtuel Spoke connecté au hub Virtual WAN doivent également contenir les préfixes d’adresse de la valeur **ExternalMapping** des règles NAT.

#### <a name="validate-bgp-advertisements"></a>Valider les publications BGP

Si le protocole BGP est configuré sur la connexion au site VPN, vérifiez le haut-parleur BGP local pour garantir qu’il publie une entrée pour les préfixes de mappage externes.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les configurations de site à site, consultez [Configurer une connexion de site à site de Virtual WAN](virtual-wan-site-to-site-portal.md).
