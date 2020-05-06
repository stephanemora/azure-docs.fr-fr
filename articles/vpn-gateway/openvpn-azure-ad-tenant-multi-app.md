---
title: 'Passerelle VPN : Locataire Azure AD pour différents groupes d’utilisateurs : Authentification Azure AD'
description: Vous pouvez utiliser un VPN P2S pour vous connecter à votre réseau virtuel à l'aide de l'authentification Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77485636"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Créer un locataire Azure Active Directory pour les connexions de protocole OpenVPN P2S

Pour vous connecter à votre réseau virtuel, vous pouvez utiliser l'authentification par certificat ou l'authentification RADIUS. Toutefois, lorsque vous utilisez le protocole Open VPN, vous pouvez également utiliser l'authentification Azure Active Directory. Si vous souhaitez que différents ensembles d’utilisateurs puissent se connecter à différentes passerelles VPN, vous pouvez inscrire plusieurs applications dans AD et les lier à différentes passerelles VPN. Cet article vous aide à configurer un locataire Azure AD pour l’authentification P2S OpenVPN et à créer et inscrire plusieurs applications dans Azure AD pour autoriser différents accès pour différents utilisateurs et groupes.

> [!NOTE]
> L’authentification Azure AD est prise en charge uniquement pour les connexions de protocole OpenVPN®.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Activer l’authentification sur la passerelle

Au cours de cette étape, vous allez activer l’authentification Azure AD sur la passerelle VPN.

1. Activez l’authentification Azure AD sur la passerelle VPN en exécutant les commandes suivantes. Veillez à modifier les commandes pour les adapter à votre propre environnement :

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > N’utilisez pas l’ID d’application du client VPN Azure dans les commandes ci-dessus : Il accorde à tous les utilisateurs l’accès à la passerelle VPN. Utilisez l’ID de la ou des applications que vous avez inscrites.

2. Créez et téléchargez le profil en exécutant les commandes suivantes. Modifiez les valeurs -ResourcGroupName et -Name pour qu'elles correspondent aux vôtres.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. Après avoir exécuté les commandes, vous obtenez un résultat semblable à celui présenté ci-dessous. Copiez l'URL du résultat dans votre navigateur pour télécharger le fichier zip du profil.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Extrayez le fichier zip téléchargé.

5. Accédez au dossier « AzureVPN » décompressé.

6. Notez l'emplacement du fichier « azurevpnconfig.xml ». Le fichier azurevpnconfig.xml contient la configuration de la connexion VPN et peut être importé directement dans l'application Azure VPN Client. Vous pouvez également distribuer ce fichier à tous les utilisateurs qui ont besoin de se connecter par e-mail ou par d'autres moyens. L'utilisateur doit disposer d'informations d'identification Azure AD valides pour se connecter.

## <a name="next-steps"></a>Étapes suivantes

Pour vous connecter à votre réseau virtuel, vous devez créer et configurer un profil de client VPN. Consultez [Configurer un client VPN pour les connexions VPN P2S](openvpn-azure-ad-client.md).
