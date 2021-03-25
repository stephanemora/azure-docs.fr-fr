---
title: 'Azure Virtual WAN : Profils clients VPN utilisateurs'
description: Cela vous aide à utiliser le fichier de profil client.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: d83b6ed2ae83db569d3c61e3cf4cd887f875eb25
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99980913"
---
# <a name="working-with-user-vpn-client-profile-files"></a>Utilisation de fichiers de profil client VPN utilisateur

Le fichier de profil contient les informations nécessaires à la configuration d’une connexion VPN. Cet article permet d’obtenir et de comprendre les informations nécessaires pour un profil client VPN utilisateur.

## <a name="download-the-profile"></a>Télécharger le profil

Vous pouvez utiliser les étapes décrites dans l’article [Télécharger des profils](global-hub-profile.md) pour télécharger le fichier zip de profil client.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* Le dossier **OpenVPN** contient le profil *ovpn* qui doit être modifié pour inclure la clé et le certificat. Pour plus d’informations, consultez [Configuration de clients OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le VPN utilisateur Virtual WAN, consultez [Création d’une connexion VPN utilisateur](virtual-wan-point-to-site-portal.md).
