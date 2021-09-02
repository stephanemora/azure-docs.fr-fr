---
title: À propos de la traduction d’adresses réseau (NAT, Network Address Translation) sur la passerelle VPN
titleSuffix: Azure VPN Gateway
description: Découvrez la traduction d’adresses réseau (NAT) dans le réseau privé virtuel (VPN) Azure pour connecter des réseaux avec des espaces d’adressage qui se chevauchent.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/22/2021
ms.author: yushwang
ms.openlocfilehash: 2bcf33466b3cbf57a67307961bb1e21e3a810a72
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112759577"
---
# <a name="about-nat-on-azure-vpn-gateway-preview"></a>À propos de la fonctionnalité NAT sur la passerelle VPN Azure (préversion)

Cet article fournit une vue d’ensemble de la prise en charge de la traduction d’adresses réseau (NAT) dans la passerelle VPN Azure. La fonctionnalité NAT définit les mécanismes de traduction d’une adresse IP en une autre dans un paquet IP. Il existe plusieurs scénarios pour le NAT :

* Connecter plusieurs réseaux avec des adresses IP qui se chevauchent
* Connecter à partir de réseaux avec des adresses IP privées (RFC1918) vers Internet (répartition Internet)
* Connecter des réseaux IPv6 vers des réseaux IPv4 (NAT64)

> [!IMPORTANT]
> Le NAT de passerelle VPN Azure prend en charge le premier scénario pour connecter des réseaux locaux ou des filiales à un réseau virtuel Azure avec des adresses IP qui se chevauchent. La répartition Internet et le NAT64 ne sont **PAS** pris en charge.

## <a name="overlapping-address-spaces"></a><a name="why"></a>Espaces d’adressage qui se chevauchent

Les organisations utilisent fréquemment des adresses IP privées définies dans le document RFC1918 pour la communication interne dans leurs réseaux privés. Quand ces réseaux sont connectés à l’aide d’un VPN via Internet ou à l’aide d’un WAN privé, les espaces d’adressage **ne doivent pas** se chevaucher. Si c’est le cas, la communication échoue. Pour connecter deux réseaux ou plus avec des adresses IP qui se chevauchent, le NAT est déployé sur les appareils de passerelle qui connectent les réseaux. 

## <a name="nat-type-static--dynamic"></a><a name="type"></a>Type de NAT : statique et dynamique

Sur un appareil de passerelle, le NAT traduit les adresses IP source et/ou de destination en fonction de règles ou de stratégies NAT pour éviter les conflits d’adresse. Il existe différents types de règles de traduction NAT :

* **NAT statique** : des règles statiques définissent une relation fixe de mappage d’adresses. Une adresse IP donnée est mappée à la même adresse du pool cible. Les mappages pour les règles statiques sont sans état, car le mappage est fixe.

* **NAT dynamique** : en cas de NAT dynamique, une adresse IP peut être traduite en différentes adresses IP cibles en fonction de la disponibilité, ou avec une combinaison différente d’adresse IP et de port TCP/UDP. On parle alors également de traduction d’adresses réseau et de ports (NAPT, Network Address and Port Translation). Les règles dynamiques entraînent des mappages de traduction avec état en fonction des flux de trafic à un moment donné.

Il faut également tenir compte de la taille du pool d’adresses pour la traduction. Si la taille du pool d’adresses cible est identique à celle du pool d’adresses d’origine, utilisez une règle NAT statique pour définir un mappage 1 à 1 dans un ordre séquentiel. Si la taille du pool d’adresses cible est inférieure à celle du pool d’adresses d’origine, utilisez la règle NAT dynamique pour tenir compte des différences.

> [!IMPORTANT]
> * Le NAT sur une passerelle VPN Azure prend uniquement en charge les règles de NAT statique 1 à 1. Les règles de NAT dynamique ne sont pas prises en charge.
> * La fonctionnalité NAT est prise en charge sur les références SKU suivantes : VpnGw2~5, VpnGw2AZ~5AZ.
> * Le NAT est pris en charge uniquement sur les connexions IPsec (sécurité du protocole Internet) entre différents locaux. Les connexions de réseau virtuel à réseau virtuel et les connexions P2S (point à site) ne sont pas prises en charge.

## <a name="nat-mode-ingress--egress"></a><a name="mode"></a>Mode NAT : entrée et sortie

Chaque règle NAT définit un mappage d’adresse ou une relation de traduction pour l’espace d’adressage réseau correspondant :

* Entrée : une règle **IngressSNAT** mappe un espace d’adressage de réseau local à un espace d’adressage traduit pour éviter le chevauchement d’adresses.

* Sortie : une règle **EgressSNAT** mappe l’espace d’adressage de réseau virtuel Azure à un autre espace d’adressage traduit. 

Pour chaque règle NAT, les deux champs suivants spécifient les espaces d’adressage avant et après la traduction :

* **Mappages internes** : il s’agit de l’espace d’adressage **avant** la traduction. Pour une règle d’entrée, ce champ correspond à l’espace d’adresse d’origine du réseau local. Pour une règle de sortie, il s’agit de l’espace d’adressage du réseau virtuel d’origine.

* **Mappages externes** : il s’agit de l’espace d’adressage **après** la traduction pour les réseaux locaux (entrée) ou les réseaux virtuels (sortie). Si différents réseaux sont connectés à une passerelle VPN Azure, les espaces d’adressage de tous les **mappages externes** ne doivent pas se chevaucher entre eux, ni avec les réseaux connectés sans NAT.

## <a name="nat-and-routing"></a><a name="routing"></a>NAT et routage

Une fois qu’une règle NAT est définie pour une connexion, l’espace d’adressage effectif de la connexion change avec la règle. Si le protocole BGP est activé sur la passerelle VPN Azure, sélectionnez l’option « Activer la traduction d’itinéraires BGP » pour convertir automatiquement les itinéraires appris et publiés sur les connexions avec des règles NAT :

* Itinéraires appris : les préfixes de destination des itinéraires appris via une connexion avec les règles IngressSNAT sont traduits à partir des préfixes de mappage internes (pré-NAT) vers les préfixes de mappage externes (après NAT) de ces règles.

* Itinéraires publiés : la passerelle VPN Azure publie les préfixes de mappage externe (après NAT) des règles EgressSNAT pour l’espace d’adressage du réseau virtuel et les itinéraires appris avec les préfixes d’adresses après NAT issus d’autres connexions.

* Considérations relatives à l’adresse IP de l’homologue BGP pour un réseau local avec NAT :
   * Adresse APIPA (169.254.0.1 à 169.254.255.254) : aucune règle NAT n’est requise. Spécifiez directement l’adresse APIPA dans la passerelle de réseau local.
   * Adresse non APIPA : spécifiez l’adresse IP **traduite** ou **après NAT** sur la passerelle de réseau local. Utilisez l’adresse ou les adresses IP BGP Azure **traduites** ou **après NAT** pour configurer les routeurs VPN locaux. Assurez-vous que les règles NAT sont définies pour la traduction prévue.

> [!NOTE]
> Les itinéraires appris sur les connexions sans règles IngressSNAT ne sont pas convertis. Les itinéraires de réseau virtuel publiés sur les connexions sans règles EgressSNAT ne sont pas non plus convertis.
>

## <a name="nat-example"></a><a name="example"></a>Exemple de NAT

Le diagramme suivant montre un exemple de configurations NAT dans le VPN Azure :

:::image type="content" source="./media/nat-overview/vpn-nat.png" alt-text="Diagramme NAT." lightbox="./media/nat-overview/vpn-nat.png" border="false":::

Le diagramme montre un réseau virtuel Azure et deux réseaux locaux avec l’espace d’adressage 10.0.1.0/24. Pour connecter ces deux réseaux au réseau virtuel Azure et à la passerelle VPN, créez les règles suivantes :

* Règle IngressSNAT 1 : cette règle traduit l’espace d’adressage local 10.0.1.0/24 en 100.0.2.0/24.

* Règle IngressSNAT 2 : cette règle traduit l’espace d’adressage local 10.0.1.0/24 en 100.0.3.0/24.

* Règle EgressSNAT 1 : cette règle traduit l’espace d’adressage local 10.0.1.0/24 en 100.0.1.0/24.

Dans le diagramme, chaque ressource de connexion contient les règles suivantes :

* Connexion 1 (VNet et branche 1) :
    * Règle IngressSNAT 1
    * Règle EgressSNAT 1

* Connexion 2 (VNet et branche 2)
    * Règle IngressSNAT 2
    * Règle EgressSNAT 1

En fonction des règles associées aux connexions, voici les espaces d’adressage pour chaque réseau :

| Réseau  | Original    | Traduit   |
| ---      | ---         | ---          |
| Réseau virtuel     | 10.0.1.0/24 | 100.0.1.0/24 |
| Branche 1 | 10.0.1.0/24 | 100.0.2.0/24 |
| Branche 2 | 10.0.1.0/24 | 100.0.3.0/24 |

Le diagramme ci-dessous montre un paquet IP de la branche 1 vers le réseau virtuel, avant et après la traduction NAT :

:::image type="content" source="./media/nat-overview/nat-packet.png" alt-text="Diagramme montrant un paquet avant et après la traduction NAT." lightbox="./media/nat-overview/nat-packet.png" border="false":::

> [!IMPORTANT]
> Une règle SNAT unique définit la traduction dans **les deux** directions d’un réseau particulier :
>
> * Une règle IngressSNAT définit la traduction des adresses IP source entrantes **dans** la passerelle VPN Azure à partir du réseau local. Elle gère également la traduction des adresses IP de destination qui quittent le réseau virtuel vers le même réseau local.
> * Une règle EgressSNAT définit la traduction des adresses IP source sortantes de la passerelle VPN Azure vers les réseaux locaux. Elle gère également la traduction des adresses IP de destination pour les paquets arrivant dans le réseau virtuel via ces connexions à l’aide de la règle EgressSNAT.
> * Dans les deux cas, aucune règle **DNAT** n’est nécessaire.

## <a name="nat-configuration"></a><a name="config"></a>Configuration NAT

Pour implémenter la configuration NAT comme indiqué ci-dessus, commencez par créer les règles NAT dans votre passerelle VPN Azure, puis créez les connexions avec les règles NAT correspondantes associées. Pour obtenir des instructions concernant la configuration de la traduction d’adresses réseau (NAT) pour vos connexions entre différents locaux, consultez [Configurer le NAT sur des passerelles VPN Azure](nat-howto.md).

## <a name="nat-faq"></a><a name="faq"></a>Questions fréquentes (FAQ) sur le NAT

[!INCLUDE [vpn-gateway-faq-nat-include](../../includes/vpn-gateway-faq-nat-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des instructions concernant la configuration de la traduction d’adresses réseau (NAT) pour vos connexions entre différents locaux, consultez [Configurer le NAT sur des passerelles VPN Azure](nat-howto.md).