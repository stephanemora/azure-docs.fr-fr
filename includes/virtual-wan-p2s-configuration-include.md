---
author: cherylmc
ms.author: cherylmc
ms.date: 07/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 21bda32ddb1c87bb07b6679499e648e0b2f7a809
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734691"
---
1. Accédez au réseau WAN virtuel que vous avez créé. 

1. Dans le menu de gauche, sélectionnez **Configurations de VPN utilisateur**.

1. Dans la page **Configurations de VPN utilisateur**, sélectionnez **+Créer une configuration de VPN utilisateur**.

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="Capture d’écran de la page des configurations de VPN utilisateur.":::

1. Dans la page **Créer une configuration de VPN utilisateur**, sous l’onglet **Informations de base**, sous **Détails de l’instance**, entrez le **nom** à affecter à la configuration VPN. 

   :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="Capture d’écran du commutateur IPsec à personnaliser.":::

1. Pour **Type de tunnel**, sélectionnez le type de tunnel de votre choix dans la liste déroulante. Les options de type de tunnel sont les suivantes : **IKEv2 VPN, OpenVPN,** et **OpenVpn et IKEv2**. Chaque type de tunnel a des paramètres obligatoires différents.

   **Impératifs et paramètres** :

     **IKEv2 VPN**

     * **Exigences :** Lorsque vous sélectionnez le type de tunnel **IKEv2**, un message vous demandant de choisir une méthode d’authentification s’affiche. Vous ne pouvez spécifier qu’une seule méthode d’authentification pour IKEv2. Vous pouvez choisir l’authentification par certificat Azure, Azure Active Directory ou RADIUS.

     * **Paramètres personnalisés IPSec :** Pour personnaliser les paramètres de la phase 1 IKE et de la phase 2 IKE, basculez le commutateur IPsec sur **Personnalisé** et sélectionnez les valeurs des paramètres. Pour en savoir plus sur les paramètres personnalisables, consultez l’article [IPSec personnalisé](../articles/virtual-wan/point-to-site-ipsec.md).

     **OpenVPN**

     * **Exigences :** Lorsque vous sélectionnez le type de tunnel **OpenVPN**, un message vous demandant de choisir un mécanisme d’authentification s’affiche. Si OpenVPN est le type de tunnel sélectionné, vous pouvez spécifier plusieurs méthodes d’authentification. Vous pouvez choisir n’importe quel sous-ensemble d’authentification par certificat Azure, Azure Active Directory ou RADIUS. Pour l’authentification RADIUS, vous pouvez fournir une adresse IP secondaire du serveur RADIUS et un secret de serveur.

1. Configurez les méthodes d’**authentification** à utiliser. Chaque méthode d’authentification se trouve sous un onglet distinct : **Certificat Azure**, **Authentification RADIUS** et **Azure Active Directory**. Certaines méthodes d’authentification sont disponibles uniquement pour certains types de tunnel.

   Sous l’onglet de la méthode d’authentification à configurer, sélectionnez **Oui** pour faire apparaître les paramètres de configuration disponibles.

   :::image type="content" source="media/virtual-wan-p2s-configuration/certificate-no.png" alt-text="Capture d’écran montrant le paramètre Non sélectionné. Le paramètre Oui est mis en évidence.":::

   * **Exemple - Authentification par certificat**

      :::image type="content" source="media/virtual-wan-p2s-configuration/certificate-authentication.png" alt-text="Capture d’écran montrant le paramètre Oui sélectionné.":::

   * **Exemple - Authentification RADIUS**

      :::image type="content" source="media/virtual-wan-p2s-configuration/radius-authentication.png" alt-text="Capture d’écran de la page d’authentification RADIUS.":::

   * **Exemple - Authentification Azure Active Directory**

      :::image type="content" source="media/virtual-wan-p2s-configuration/azure-active-directory.png" alt-text="Page d’authentification Azure Active Directory.":::

1. Une fois que vous avez fini de configurer les paramètres, cliquez sur **Vérifier + créer** au bas de la page.

1. Cliquez sur **Créer** pour créer la configuration de VPN utilisateur.
