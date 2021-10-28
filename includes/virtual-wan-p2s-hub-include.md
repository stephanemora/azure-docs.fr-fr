---
ms.author: cherylmc
author: cherylmc
ms.date: 08/19/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 762b5cc965b4f9ee0af929ff32574a98da43cc6a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288169"
---
1. Dans la page de votre **WAN virtuel**, dans le volet gauche, sélectionnez **Hubs**. Sur la page **Hubs**, sélectionnez **+ Nouveau hub**.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.png" alt-text="Capture d’écran du nouveau hub.":::

1. Dans la page **Créer un hub virtuel**, affichez l’onglet **Informations de base**.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.png" alt-text="Capture d’écran de la page Créer un hub virtuel.":::

1. Sous l’onglet **Informations de base**, configurez les paramètres suivants :

   * **Région** : sélectionnez la région dans laquelle vous souhaitez déployer le hub virtuel.
   * **Nom** : nom sous lequel vous souhaitez que le hub virtuel soit connu.
   * **Espace d’adressage privé du hub** : plage d’adresses du hub en notation CIDR.

1. Cliquez sur l’onglet **Point à site** pour ouvrir la page de configuration de connexion point à site. Pour voir les paramètres de connexion point à site, cliquez sur **Oui**.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-point-to-site.png" alt-text="Capture d’écran de la configuration du hub virtuel avec sélection de l’option Point à site.":::

1. Configurez les paramètres suivants :

   * **Unités d’échelle de la passerelle** : capacité d’agrégation de la passerelle VPN utilisateur. Si vous sélectionnez 40 unités d’échelle de passerelle ou plus, planifiez votre pool d’adresses de clients en conséquence. Pour plus d’informations sur l’impact de ce paramètre sur le pool d’adresses de clients, consultez [À propos des pools d’adresses de clients](../articles/virtual-wan/about-client-address-pools.md). Pour plus d’informations sur les unités d’échelle de passerelle, consultez le [Forum aux questions](../articles/virtual-wan/virtual-wan-faq.md#for-user-vpn-point-to-site--how-many-clients-are-supported).
   * **Configuration point à site** : sélectionnez la configuration de VPN utilisateur que vous avez créée à l’étape précédente.
   * **Préférence de routage** : la préférence de routage Azure vous permet de choisir la façon dont votre trafic est routé entre Azure et Internet. Vous pouvez choisir de router le trafic via le réseau Microsoft ou le réseau du fournisseur de services Internet (Internet public). Ces options sont également appelées respectivement routage de patate froide et routage de patate chaude. L’IP publique dans Virtual WAN est attribuée par le service en fonction de l’option de routage sélectionnée. Pour plus d’informations sur la préférence de routage par le biais du réseau Microsoft ou de l’ISP, consultez l’article [Préférence de routage](../articles/virtual-network/ip-services/routing-preference-overview.md).
   * **Pool d’adresses client** : pool d’adresses à partir duquel les adresses IP sont automatiquement affectées aux clients VPN. Pour plus d’informations, consultez [À propos des pools d’adresses de clients](../articles/virtual-wan/about-client-address-pools.md).
   * **Serveurs DNS personnalisés** : adresse IP du ou des serveurs DNS que les clients vont utiliser. Vous pouvez en spécifier 5 au maximum.

1. Sélectionnez **Vérifier + créer** pour valider vos paramètres.

1. Quand la validation réussit, sélectionnez **Créer**. La création d’un hub peut prendre 30 minutes ou plus.