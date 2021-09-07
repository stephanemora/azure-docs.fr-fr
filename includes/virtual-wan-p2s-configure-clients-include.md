---
ms.author: cherylmc
author: cherylmc
ms.date: 07/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 06b4d9f2830bfd9599aee30c68235d3078eb32fa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734697"
---
**OpenVPN**

* [Configurer un client OpenVPN pour Azure Virtual WAN](../articles/virtual-wan/howto-openvpn-clients.md)
* [Azure AD Authentication - Windows 10](../articles/virtual-wan/openvpn-azure-ad-client.md)
* [Azure AD Authentication - macOS](../articles/virtual-wan/openvpn-azure-ad-client-mac.md)

**IKEv2**

1. Sélectionnez les fichiers de configuration du client VPN qui correspondent à l’architecture de l’ordinateur Windows. Pour une architecture de processeur 64 bits, choisissez le package du programme d’installation « VpnClientSetupAmd64 ». Pour une architecture de processeur 32 bits, choisissez le package du programme d’installation « VpnClientSetupX86 ».

1. Double-cliquez sur le package pour lancer l’installation. Si une fenêtre contextuelle SmartScreen s’affiche, sélectionnez **Plus d’infos**, puis **Exécuter quand même**.

1. Sur l’ordinateur client, accédez à **Paramètres réseau**, puis sélectionnez **VPN**. La connexion VPN indique le nom du réseau virtuel auquel elle se connecte.

1. Avant de tenter de vous connecter, vérifiez que vous avez installé un certificat client sur l’ordinateur client. Un certificat client est requis pour l’authentification lors de l’utilisation du type d’authentification de certificat Azure natif. Pour plus d’informations sur la génération de certificats, consultez [Générer des certificats](../articles/virtual-wan/certificates-point-to-site.md). Pour plus d’informations sur l’installation d’un certificat client, consultez [Installer un certificat client](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).
