---
title: Configurer des règles NAT de VPN pour votre passerelle
titleSuffix: Azure Virtual WAN
description: Découvrez comment configurer des règles NAT pour votre passerelle VPN VWAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/17/2021
ms.author: cherylmc
ms.openlocfilehash: a31b3718eb1baa32aef39474383924efe8cf93b6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743228"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>Configurer des règles NAT pour votre passerelle VPN de Virtual WAN – Préversion

> [!IMPORTANT]
> Les règles NAT sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez configurer votre passerelle VPN de Virtual WAN avec des règles NAT statiques un-à-un. Une règle NAT fournit un mécanisme permettant de configurer la traduction un-à-un des adresses IP. Vous pouvez une NAT pour interconnecter deux réseaux IP dont les adresses IP sont incompatibles ou se chevauchent. Un scénario classique est celui des branches avec des adresses IP se chevauchant, qui souhaitent accéder aux ressources de réseau virtuel Azure.

Cette configuration utilise une table de flux pour router le trafic d’une adresse IP externe (hôte) vers une adresse IP interne associée à un point de terminaison à l’intérieur d’un réseau virtuel (machine virtuelle, ordinateur, conteneur, etc.).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagramme montrant une architecture.":::

## <a name="configure-and-view-rules"></a><a name="view"></a>Configurer et afficher des règles

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

### <a name="configuration-considerations"></a>Considérations relatives à la configuration

* La taille du sous-réseau pour les mappages interne et externe doit être la même pour la NAT statique un-à-un.
* Veillez à modifier le site VPN dans le portail Azure pour ajouter des préfixes **ExternalMapping** dans le champ « Espace d’adressage privé ». Actuellement, les sites pour lesquels le protocole BGP est activé doivent s’assurer que l’annonceur BGP local (paramètres BGP de l’appareil) inclut une entrée pour les préfixes de mappage externes.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les configurations de site à site, consultez [Configurer une connexion de site à site de Virtual WAN](virtual-wan-site-to-site-portal.md).
