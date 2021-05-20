---
title: 'Guide pratique pour créer un locataire Azure AD pour les connexions P2S de protocole OpenVPN : authentification Azure AD'
titleSuffix: Azure VPN Gateway
description: Découvrez comment configurer un locataire Azure AD pour l’authentification P2S OpenVPN, puis inscrivez plusieurs applications dans Azure AD pour autoriser différents accès pour différents utilisateurs et groupes.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/05/2021
ms.author: cherylmc
ms.openlocfilehash: f5b67a1843dd363c727122d77c4c9d574e90a4ab
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108760478"
---
# <a name="create-an-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>Créer un locataire Active Directory (AD) pour les connexions P2S de protocole OpenVPN

Lorsque vous vous connectez à votre réseau virtuel à l’aide d’une connexion point à site, vous avez le choix du protocole à utiliser. Le protocole que vous utilisez détermine les options d’authentification qui sont à votre disposition. Si vous souhaitez utiliser l’authentification Azure Active Directory, vous pouvez le faire lorsque vous utilisez le protocole OpenVPN. Si vous souhaitez que différents ensembles d’utilisateurs puissent se connecter à différentes passerelles VPN, vous pouvez inscrire plusieurs applications dans AD et les lier à différentes passerelles VPN. Cet article vous aide à configurer un locataire Azure AD pour P2S OpenVPN et à créer et inscrire plusieurs applications dans Azure AD pour autoriser différents accès pour différents utilisateurs et groupes. Pour plus d’informations sur les protocoles et l’authentification point à site, consultez [À propos du VPN point à site](point-to-site-about.md).

[!INCLUDE [OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Activer l’authentification sur la passerelle

Au cours de cette étape, vous allez activer l’authentification Azure AD sur la passerelle VPN.

1. Activez l’authentification Azure AD sur la passerelle VPN en accédant à **Configuration point à site** et en choisissant **OpenVPN (SSL)** comme **Type de tunnel**. Sélectionnez **Azure Active Directory** comme **Type d’authentification**, puis renseignez les informations sous la section **Azure Active Directory**.

    ![Vue Portail Azure](./media/openvpn-azure-ad-tenant-multi-app/azure-ad-auth-portal.png)

    > [!NOTE]
    > N’utilisez pas l’ID d’application du client VPN Azure : Il accorde à tous les utilisateurs l’accès à la passerelle VPN. Utilisez l’ID de la ou des applications que vous avez inscrites.

2. Créez et téléchargez le profil en cliquant sur le lien **Télécharger le client VPN**.

3. Extrayez le fichier zip téléchargé.

4. Accédez au dossier « AzureVPN » décompressé.

5. Notez l'emplacement du fichier « azurevpnconfig.xml ». Le fichier azurevpnconfig.xml contient la configuration de la connexion VPN et peut être importé directement dans l'application Azure VPN Client. Vous pouvez également distribuer ce fichier à tous les utilisateurs qui ont besoin de se connecter par e-mail ou par d'autres moyens. L'utilisateur doit disposer d'informations d'identification Azure AD valides pour se connecter.

## <a name="next-steps"></a>Étapes suivantes

Pour vous connecter à votre réseau virtuel, vous devez créer et configurer un profil de client VPN. Consultez [Configurer un client VPN pour les connexions VPN P2S](openvpn-azure-ad-client.md).
