---
title: 'Passerelle VPN Azure : À propos des profils clients VPN P2S'
description: Cela vous aide à utiliser le fichier de profil client.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79528504"
---
# <a name="about-p2s-vpn-client-profiles"></a>À propos des profils clients VPN P2S

Le fichier de profil téléchargé contient les informations nécessaires à la configuration d’une connexion VPN. Cet article vous aide à obtenir et à comprendre les informations nécessaires pour un profil client VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* Le dossier **OpenVPN** contient le profil *ovpn* qui doit être modifié pour inclure la clé et le certificat. Pour plus d’informations, consultez [Configurer des clients OpenVPN pour la passerelle VPN Azure](vpn-gateway-howto-openvpn-clients.md#windows). Ce dossier ne sera pas présent dans le fichier zip si l’authentification Azure AD est sélectionnée sur la passerelle VPN. À la place, le fichier azurevpnconfig.xml se trouve dans le dossier AzureVPN.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la fonctionnalité point à site, consultez l’article [À propos de la fonctionnalité point à site](point-to-site-about.md).
