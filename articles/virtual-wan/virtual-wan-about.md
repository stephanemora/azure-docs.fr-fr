---
title: Présentation du WAN virtuel Azure | Microsoft Docs
description: Apprenez-en davantage sur la connectivité de branche à branche évolutive et automatisée via le WAN virtuel.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/25/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: fedb41f6538e0c65114c4a28c8aa43ffc7ef6d7f
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957598"
---
# <a name="what-is-azure-virtual-wan"></a>Qu’est-ce que le WAN virtuel Azure ?

Un WAN virtuel Azure est un service de mise en réseau qui offre une connectivité de branche à branche optimisée et automatisée via Azure. Le WAN virtuel vous permet de vous connecter et de configurer des appareils de branche pour communiquer avec Azure. Cela peut se faire manuellement ou à l’aide d’appareils de partenaire de votre choix via un WAN virtuel partenaire. Consultez l’[article sur les partenaires préférés](https://go.microsoft.com/fwlink/p/?linkid=2019615) pour plus d’informations. L’utilisation d’appareils de partenaire permet une utilisation facile, une simplification de la connectivité et une gestion de la configuration. Le tableau de bord intégré du WAN Azure fournit des informations de dépannage en temps réel qui peuvent vous faire gagner du temps et vous permettre d’afficher en toute simplicité la connectivité à grande échelle.

![Diagramme WAN virtuel](./media/virtual-wan-about/virtualwan.png)

Cet article fournit un aperçu rapide de la connectivité réseau de vos charges de travail Azure et non Azure. Le WAN virtuel offre les avantages suivants :

* **Solutions de connectivité intégrées dans une architecture hub and spoke :** Automatisez la configuration et la connectivité site à site entre les sites locaux et un hub Azure.
* **Installation et configuration spoke automatisées :** Connectez en toute transparence vos charges de travail et vos réseaux virtuels au hub Azure.
* **Dépannage intuitif :** Vous pouvez afficher le flux de bout en bout au sein d’Azure et utiliser ces informations pour prendre les mesures nécessaires.

## <a name="s2s"></a>Connexions site à site

Pour créer une connexion de site à site à l’aide du WAN virtuel, vous pouvez faire appel à un [partenaire de WAN virtuel](https://go.microsoft.com/fwlink/p/?linkid=2019615) ou établir la connexion manuellement.

### <a name="s2spartner"></a>Collaboration avec un partenaire de WAN virtuel

Lorsque vous utilisez un partenaire WAN virtuel, le processus est le suivant :

1. Le contrôleur de l’appareil de branche (VPN/SDWAN) est authentifié pour exporter des informations orientées Site vers Azure à l’aide d’un [principal de service Azure](../active-directory/develop/howto-create-service-principal-portal.md).
2. Le contrôleur de l’appareil de branche (VPN/SDWAN) obtient la configuration de la connectivité Azure et met à jour l’appareil local. Cela permet d’automatiser le téléchargement, la modification et la mise à jour de la configuration sur le périphérique VPN local.
3. Une fois que l’appareil possède la bonne configuration Azure, une connexion de site à site (deux tunnels actifs) est établie avec le WAN Azure. Azure prend en charge IKEv1 et IKEv2. La prise en charge du protocole BGP est facultative.


Si vous ne souhaitez pas utiliser un partenaire préféré, vous pouvez configurer la connexion manuellement en suivant les instructions du tutoriel [Créer une connexion de site à Site à l’aide du WAN virtuel](virtual-wan-site-to-site-portal.md).

## <a name="p2s"></a>Connexions point à site (préversion)

Une connexion point à site (P2S) vous permet de créer une connexion sécurisée à votre hub virtuel à partir d’un ordinateur de client individuel. Une connexion P2S est établie en étant démarrée à partir de l’ordinateur client. Cette solution est utile pour les télétravailleurs souhaitant se connecter à partir d’un emplacement distant, comme depuis leur domicile ou pendant une conférence. De même, l’utilisation d’un VPN P2S est une solution utile qui constitue une alternative au VPN site à site (S2S) lorsqu’un nombre restreint de clients doit se connecter.

Pour établir la connexion manuellement, consultez l’article [Créer une connexion de point à site à l’aide du WAN virtuel](https://go.microsoft.com/fwlink/p/?linkid=2020051&clcid).

## <a name="er"></a>Connexions ExpressRoute connections (préversion)

Pour établir la connexion manuellement, consultez l’article [Créer une connexion ExpressRoute à l’aide du WAN virtuel](https://go.microsoft.com/fwlink/p/?linkid=2020148&clcid).


## <a name="resources"></a>Ressources du WAN virtuel

Pour configurer un WAN virtuel de bout en bout, vous devez créer les ressources suivantes :

* **virtualWAN :** la ressource virtualWAN représente une superposition virtuelle de votre réseau Azure et est constituée de plusieurs ressources. Elle contient des liens vers tous les hubs virtuels que vous souhaitez faire figurer dans le WAN virtuel. Les ressources du WAN virtuel sont isolées les unes des autres et ne peuvent pas contenir de hub commun. Les hubs virtuels d’un WAN virtuel ne communiquent pas entre eux. La propriété « Allow branch to branch traffic » (Autoriser le trafic de branche à branche) autorise le trafic entre des sites VPN ainsi que les sites compatibles VPN à ExpressRoute. N’oubliez pas que ExpressRoute dans le WAN virtuel Azure est actuellement en préversion.

* **Site :** la ressource de site appelée vpnsite représente votre périphérique VPN local et ses paramètres. En faisant appel à un partenaire de WAN virtuel, vous disposez d’une solution intégrée pour exporter automatiquement ces informations vers Azure.

* **Hub :** Un hub virtuel est un réseau virtuel géré par Microsoft. Le hub contient différents points de terminaison de service pour activer la connectivité à partir de votre réseau local (vpnsite). Le hub est le cœur de votre réseau au sein d’une région spécifique. Il ne peut exister qu’un seul hub par région Azure. Lorsque vous créez un hub à l’aide du portail Azure, le système crée un réseau virtuel et une passerelle VPN pour le hub virtuel.

  Une passerelle de hub virtuel est différente d’une passerelle de réseau virtuel que vous utilisez pour les services ExpressRoute et Passerelle VPN. Par exemple, lorsque vous utilisez le WAN virtuel, vous ne créez pas de connexion de site à site directe entre votre site local et votre réseau virtuel. Au lieu de cela, vous établissez une connexion de site à site avec le hub. Le trafic passe toujours par la passerelle du hub. Cela signifie que vos réseaux virtuels n’ont pas besoin d’avoir leur propre passerelle de réseau virtuel. Le WAN virtuel simplifie la mise à l’échelle de vos réseaux virtuels via le hub virtuel et la passerelle de hub virtuel. 

* **Connexion au réseau virtuel du hub :** La ressource de connexion au réseau virtuel du hub permet de connecter facilement le hub à votre réseau virtuel. À ce stade, vous pouvez uniquement vous connecter à des réseaux virtuels qui se trouvent dans la même région que le hub.

* **Table de routage de hub :** vous pouvez créer un itinéraire de hub virtuel et appliquer l’itinéraire à la table de routage de hub virtuel. Vous pouvez appliquer plusieurs itinéraires à la table de routage du hub virtuel.

## <a name="faq"></a>Forum Aux Questions

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]


## <a name="next-steps"></a>Étapes suivantes

* Consultez l’article [Partenaires Virtual WAN et emplacements](https://aka.ms/virtualwan)