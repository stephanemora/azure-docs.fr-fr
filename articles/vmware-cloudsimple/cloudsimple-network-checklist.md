---
title: Solution Azure VMware par CloudSimple - Check-list pour le réseau
description: Check-list pour allouer le CIDR réseau sur une solution Azure VMware par CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c31942b198697ddc913f3732fd41409334ff0ef6
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817392"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Prérequis du réseau pour la solution Azure VMware par CloudSimple

La solution Azure VMware par CloudSimple offre un environnement de cloud privé VMware accessible aux utilisateurs et aux applications depuis des environnements locaux, depuis des appareils gérés par l’entreprise et depuis des ressources Azure. La connectivité est fournie à l’aide de services réseau, comme des réseaux privés virtuels et des connexions ExpressRoute.  Certains des services réseau nécessitent la spécification de plages d’adresses réseau pour activer les services.  Les tableaux de cet article décrivent l’ensemble des plages d’adresses et les services correspondants qui utilisent les adresses spécifiées.  Certains des adresses sont obligatoires et d’autres dépendent des services que vous voulez déployer.  Ces espaces d’adressage ne doivent pas recouvrir vos sous-réseaux locaux, les sous-réseaux du réseau virtuel Azure ou les sous-réseaux des charges de travail CloudSimple planifiées.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Plages d’adresses réseau nécessaires pour la création d’un cloud privé

Pour la création du service CloudSimple et d’un cloud privé, la plage CIDR réseau suivante est nécessaire.

| Nom/Utilisée pour     | Description                                                                                                                            | Plage d’adresses            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| CIDR de la passerelle      | Nécessaire pour les services Edge (passerelles VPN).  Ce CIDR est nécessaire lors de la création du service CloudSimple et doit provenir de l’espace RFC 1918. | /28                      |
| CIDR vSphere/vSAN | Nécessaire pour les réseaux de gestion VMware. Ce CIDR doit être spécifié lors de la création d’un cloud privé.                                    | /24 ou /23 ou /22 ou /21 |

## <a name="network-address-range-required-for-azure-network-connection-to-on-premises-network"></a>Plage d’adresses réseau nécessaire pour la connexion du réseau Azure à un réseau local

La connexion du [réseau local au réseau du cloud privé avec ExpressRoute](on-premises-connection.md) établit une connexion Global Reach.  La connexion échange les routes via BGP entre votre réseau local, le réseau du cloud privé et vos réseaux Azure.

| Nom/Utilisée pour             | Description                                                                                                                                                                             | Plage d’adresses |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| CIDR de l’appairage ExpressRoute | Nécessaire quand ExpressRoute Global Reach est utilisé pour la connectivité locale. Ce CIDR doit être fourni quand une demande de connexion Global Reach est faite via un ticket de support. | /29           |

## <a name="network-address-range-required-for-using-site-to-site-vpn-connection-to-on-premises-network"></a>Plage d’adresses réseau nécessaire pour l’utilisation de la connexion VPN site à site au réseau local

La connexion du [réseau local au réseau du cloud privé avec un VPN de site à site](vpn-gateway.md) nécessite les adresses IP, le réseau local et les identificateurs suivants. 

| Adresse/Plage d’adresses | Description                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Adresse IP du pair               | Adresse IP publique de la passerelle VPN locale. Nécessaire pour établir une connexion VPN de site à site entre un centre de données local et la région du service CloudSimple. Cette adresse IP est nécessaire lors de la création d’une passerelle VPN de site à site.                                         |
| Identificateur du pair       | Identificateur du pair de votre passerelle VPN locale. C’est généralement identique à l’**adresse IP du pair**.  Si un identificateur unique est spécifié sur votre passerelle VPN locale, l’identificateur doit être spécifié.  L’ID de pair est nécessaire lors de la création d’une passerelle VPN de site à site.   |
| Réseaux locaux   | Préfixes locaux qui doivent accéder aux réseaux CloudSimple dans la région.  Incluez tous les préfixes du réseau local qui vont accéder au réseau CloudSimple, notamment le réseau client à partir duquel les utilisateurs vont accéder.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Plage d’adresses réseau nécessaire pour l’utilisation de connexions VPN point à site

Une connexion VPN de point à site permet d’accéder au réseau CloudSimple à partir d’un ordinateur client.  La [configuration d’un VPN de point à site](vpn-gateway.md) nécessite la spécification de la plage d’adresses réseau suivante.

| Adresse/Plage d’adresses | Description                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sous-réseau client         | Les adresses DHCP sont données à partir du sous-réseau client quand vous vous connectez avec le VPN point à site. Ce sous-réseau est nécessaire lors de la création d’une passerelle VPN de point à site sur le portail CloudSimple.  Le réseau est divisé en deux sous-réseaux : un des sous-réseaux est utilisé pour la connexion UDP et l’autre pour les connexions TCP. |

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide - Créer un service CloudSimple](quickstart-create-cloudsimple-service.md)
* [quickstart-create-private-cloud](quickstart-create-private-cloud.md)
* Découvrir plus d’informations sur les [connexions réseau Azure](cloudsimple-azure-network-connection.md)
* Découvrir plus d’informations sur les [passerelles VPN](cloudsimple-vpn-gateways.md)
