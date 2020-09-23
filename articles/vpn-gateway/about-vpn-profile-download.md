---
title: 'Passerelle VPN Azure : À propos des profils clients VPN P2S'
description: Cela vous aide à utiliser le fichier de profil client.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 326abaa123ede1fbe371c69fcbed47a310b54511
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89424835"
---
# <a name="about-p2s-vpn-client-profiles"></a>À propos des profils clients VPN P2S

Le fichier de profil téléchargé contient les informations nécessaires à la configuration d’une connexion VPN. Cet article vous aide à obtenir et à comprendre les informations nécessaires pour un profil client VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* Le dossier **OpenVPN** contient le profil *ovpn* qui doit être modifié pour inclure la clé et le certificat. Pour plus d’informations, consultez [Configurer des clients OpenVPN pour la passerelle VPN Azure](vpn-gateway-howto-openvpn-clients.md#windows). Si vous sélectionnez l’authentification Azure AD sur la passerelle VPN, ce dossier sera absent du fichier zip. Au lieu de cela, accédez au dossier AzureVPN et recherchez azurevpnconfig.xml.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la fonctionnalité point à site, consultez l’article [À propos de la fonctionnalité point à site](point-to-site-about.md).
