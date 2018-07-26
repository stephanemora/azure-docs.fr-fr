---
title: Présentation du WAN virtuel Azure | Microsoft Docs
description: Apprenez-en davantage sur la connectivité de branche à branche évolutive et automatisée via le WAN virtuel.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 07/18/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 67dd6ba9b94ed9d58d91fb644ce9ee9e44ae9e45
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159167"
---
# <a name="what-is-azure-virtual-wan-preview"></a>Qu’est-ce que le WAN virtuel Azure ? (Préversion)

Un WAN virtuel Azure est un service de mise en réseau qui offre une connectivité de branche à branche optimisée et automatisée via Azure. Le WAN virtuel vous permet de vous connecter et de configurer des appareils de branche pour communiquer avec Azure. Cela peut se faire manuellement ou à l’aide d’appareils de fournisseur de votre choix via un WAN virtuel partenaire. L’utilisation d’appareils de fournisseur permet une utilisation facile, une simplification de la connectivité et une gestion de la configuration. Le tableau de bord intégré du WAN Azure fournit des informations de dépannage en temps réel qui peuvent vous faire gagner du temps et vous permettre d’afficher en toute simplicité la connectivité de site à site à grande échelle.

> [!IMPORTANT]
> Le WAN virtuel Azure est actuellement disponible en préversion publique gérée. Pour utiliser le WAN virtuel, vous devez vous [inscrire à la préversion](#enroll).
>
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article fournit un aperçu rapide de la connectivité réseau de vos charges de travail Azure et non Azure. Le WAN virtuel offre les avantages suivants :

* **Solutions de connectivité intégrées dans une architecture hub and spoke :** Automatisez la configuration et la connectivité site à site entre les sites locaux et un hub Azure à partir de diverses sources, y compris les solutions de WAN virtuel partenaires.
* **Installation et configuration spoke automatisées :** Connectez en toute transparence vos charges de travail et vos réseaux virtuels au hub Azure.
* **Dépannage intuitif :** Vous pouvez afficher le flux de bout en bout au sein d’Azure et utiliser ces informations pour prendre les mesures nécessaires.

![Diagramme WAN virtuel](./media/virtual-wan-about/virtualwan.png)

## <a name="vendor"></a>Collaboration avec un partenaire de WAN virtuel

1. Le contrôleur de l’appareil de branche (VPN/SDWAN) est authentifié pour exporter des informations orientées Site vers Azure à l’aide d’un principal de service Azure.
2. Le contrôleur de l’appareil de branche (VPN/SDWAN) obtient la configuration de la connectivité Azure et met à jour l’appareil local. Cela permet d’automatiser le téléchargement, la modification et la mise à jour de la configuration sur le périphérique VPN local.
3. Une fois que l’appareil possède la bonne configuration Azure, une connexion de site à site (deux tunnels actifs) est établie avec le WAN Azure. Le contrôleur de branche (VPN/SDWAN) doit prendre en charge IKEv2. La prise en charge du protocole BGP est facultative.

## <a name="resources"></a>Ressources du WAN virtuel

Pour configurer un WAN virtuel de bout en bout, vous devez créer les ressources suivantes :

* **virtualWAN :** la ressource virtualWAN représente une superposition virtuelle de votre réseau Azure et est constituée de plusieurs ressources. Elle contient des liens vers tous les hubs virtuels que vous souhaitez faire figurer dans le WAN virtuel. Les ressources du WAN virtuel sont isolées les unes des autres et ne peuvent pas contenir de hub commun. Les hubs virtuels d’un WAN virtuel ne communiquent pas entre eux.

* **Site :** la ressource de site appelée vpnsite représente votre périphérique VPN local et ses paramètres. En faisant appel à un partenaire de WAN virtuel, vous disposez d’une solution intégrée pour exporter automatiquement ces informations vers Azure.

* **Hub :** Un hub virtuel est un réseau virtuel géré par Microsoft. Le hub contient différents points de terminaison de service pour activer la connectivité à partir de votre réseau local (vpnsite). Le hub est le cœur de votre réseau au sein d’une région spécifique. Il ne peut exister qu’un seul hub par région Azure. Lorsque vous créez un hub à l’aide du portail Azure, le système crée automatiquement un réseau virtuel et une passerelle VPN pour le hub virtuel.

  Une passerelle de hub virtuel est différente d’une passerelle de réseau virtuel que vous utilisez pour les services ExpressRoute et Passerelle VPN. Par exemple, lorsque vous utilisez le WAN virtuel, vous ne créez pas de connexion de site à site directe entre votre site local et votre réseau virtuel. Au lieu de cela, vous établissez une connexion de site à site avec le hub. Le trafic passe toujours par la passerelle du hub. Cela signifie que vos réseaux virtuels n’ont pas besoin d’avoir leur propre passerelle de réseau virtuel. Le WAN virtuel simplifie la mise à l’échelle de vos réseaux virtuels via le hub virtuel et la passerelle de hub virtuel. 

* **Connexion au réseau virtuel du hub :** La ressource de connexion au réseau virtuel du hub permet de connecter facilement le hub à votre réseau virtuel. À ce stade, vous pouvez uniquement vous connecter à des réseaux virtuels qui se trouvent dans la même région que le hub.

##<a name="enroll"></a>S’inscrire à la préversion

Avant de pouvoir configurer le WAN virtuel, vous devez tout d’abord inscrire automatiquement votre abonnement à la préversion. Sinon, vous ne serez pas en mesure d’utiliser le WAN virtuel depuis le portail. Pour vous abonner, envoyez un e-mail à <azurevirtualwan@microsoft.com> avec votre ID d’abonnement. Vous recevrez un e-mail une fois votre abonnement inscrit.

## <a name="faq"></a>Forum Aux Questions

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="feedback"></a>Commentaires de la préversion

Nous aimerions recevoir vos commentaires. Envoyez un e-mail à <azurevirtualwan@microsoft.com> pour signaler des problèmes ou pour fournir des commentaires (positifs ou négatifs) sur le WAN virtuel. Ajoutez le nom de votre société entre crochets « [] » dans la ligne Objet. Ajoutez également votre ID d’abonnement, si vous signalez un problème.

## <a name="next-steps"></a>Étapes suivantes

Pour créer une connexion de site à site à l’aide du WAN virtuel, vous pouvez faire appel à un [partenaire de WAN virtuel](https://aka.ms/virtualwan) ou établir la connexion manuellement. Pour établir la connexion manuellement, consultez l’article [Créer une connexion de site à site à l’aide du WAN virtuel](virtual-wan-site-to-site-portal.md).
