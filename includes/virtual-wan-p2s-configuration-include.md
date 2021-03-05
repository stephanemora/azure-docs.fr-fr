---
author: cherylmc
ms.author: cherylmc
ms.date: 02/23/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 567c0bb75c30a1f0ccdcde7ec1b0f04f5d6e54c5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048253"
---
[!INCLUDE [Portal feature rollout](virtual-wan-portal-feature-rollout.md)]

1. Accédez à **Toutes les ressources**. Sélectionnez le Virtual WAN que vous avez créé, puis **Configurations de VPN utilisateur** dans le menu de gauche.
1. Sur la page **Configurations de VPN utilisateur**, sélectionnez **+Créer une configuration de VPN utilisateur** en haut de la page pour ouvrir la page **Créer une configuration de VPN utilisateur**.

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="Capture d’écran de la page des configurations de VPN utilisateur.":::

1. Dans l’onglet **Notions de base**, sous **Détails de l’instance**, entrez le **nom** que vous souhaitez attribuer à votre configuration de VPN.
1. Pour **Type de tunnel**, sélectionnez le type de tunnel souhaité dans la liste déroulante. Les options de type de tunnel sont les suivantes : **IKEv2 VPN**, **OpenVPN**, et **OpenVpn et IkeV2**.
1. Suivez les étapes suivantes qui correspondent au type de tunnel que vous avez sélectionné. Une fois toutes les valeurs spécifiées, cliquez sur **Vérifier + créer**, puis sur **Créer** pour créer la configuration.

   **IKEv2 VPN**

   * **Exigences :** Lorsque vous sélectionnez le type de tunnel **IKEv2**, un message vous demandant de choisir une méthode d’authentification s’affiche. Vous ne pouvez spécifier qu’une seule méthode d’authentification pour IKEv2. Vous pouvez choisir l’authentification par certificat Azure, Azure Active Directory ou RADIUS.

   * **Paramètres personnalisés IPSec :** Pour personnaliser les paramètres de la phase 1 IKE et de la phase 2 IKE, basculez le commutateur IPsec sur **Personnalisé** et sélectionnez les valeurs des paramètres. Pour en savoir plus sur les paramètres personnalisables, consultez l’article [IPSec personnalisé](../articles/virtual-wan/point-to-site-ipsec.md).

     :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="Capture d’écran du commutateur IPsec à personnaliser.":::

   * **Authentification :** Pour accéder au mécanisme d’authentification que vous souhaitez utiliser, cliquez sur **Suivant** en bas de la page pour passer à la méthode d’authentification, ou sur l’onglet approprié en haut de la page. Basculez le commutateur sur **Oui** pour sélectionner la méthode.

     Dans cet exemple, l’authentification RADIUS est sélectionnée. Pour l’authentification RADIUS, vous pouvez fournir une adresse IP secondaire du serveur RADIUS et un secret de serveur.

     :::image type="content" source="media/virtual-wan-p2s-configuration/ike-radius.png" alt-text="Capture d’écran d’IKE.":::

   **OpenVPN**

   * **Exigences :** Lorsque vous sélectionnez le type de tunnel **OpenVPN**, un message vous demandant de choisir un mécanisme d’authentification s’affiche. Si OpenVPN est le type de tunnel sélectionné, vous pouvez spécifier plusieurs méthodes d’authentification. Vous pouvez choisir n’importe quel sous-ensemble d’authentification par certificat Azure, Azure Active Directory ou RADIUS. Pour l’authentification RADIUS, vous pouvez fournir une adresse IP secondaire du serveur RADIUS et un secret de serveur.

   * **Authentification :** Pour accéder aux méthodes d’authentification que vous souhaitez utiliser, cliquez sur **Suivant** en bas de la page pour passer à la méthode d’authentification, ou sur l’onglet approprié en haut de la page.
   Pour chaque méthode que vous souhaitez sélectionner, basculez le commutateur sur **Oui** et entrez les valeurs appropriées.

     Dans cet exemple, Azure Active Directory est sélectionné.

     :::image type="content" source="media/virtual-wan-p2s-configuration/openvpn.png" alt-text="Capture d’écran de la page OpenVPN.":::
