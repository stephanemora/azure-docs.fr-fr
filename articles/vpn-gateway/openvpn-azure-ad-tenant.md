---
title: 'Passerelle VPN : Locataire Azure AD pour les connexions VPN P2S : Authentification Azure AD'
description: Vous pouvez utiliser un VPN P2S pour vous connecter à votre réseau virtuel à l'aide de l'authentification Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: alzam
ms.openlocfilehash: 6357fb2d69a9c0ded430c17b77e854f63fc8f5c6
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747373"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Créer un locataire Azure Active Directory pour les connexions de protocole OpenVPN P2S

Pour vous connecter à votre réseau virtuel, vous pouvez utiliser l'authentification par certificat ou l'authentification RADIUS. Toutefois, lorsque vous utilisez le protocole Open VPN, vous pouvez également utiliser l'authentification Azure Active Directory. Cet article vous aide à configurer un locataire Azure AD pour l'authentification Open VPN P2S.

> [!NOTE]
> L’authentification Azure AD est prise en charge uniquement pour les connexions de protocole OpenVPN®.
>

## <a name="tenant"></a>1. Créer le locataire Azure AD

Pour créer un locataire Azure AD, suivez la procédure décrite dans l'article [Créer un locataire](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nom de l'organisation
* Nom de domaine initial

Exemple :

   ![Nouveau locataire Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="users"></a>2. Créer des utilisateurs de locataire Azure AD

Créez ensuite deux comptes d'utilisateur. Créez un compte d'administrateur général et un compte d'utilisateur principal. Le compte d'utilisateur principal est utilisé comme compte d'intégration principal (compte de service). Lorsque vous créez un compte d'utilisateur de locataire Azure AD, vous définissez le rôle d'annuaire en fonction du type d'utilisateur que vous souhaitez créer.

Utilisez les étapes décrites dans [cet article](../active-directory/fundamentals/add-users-azure-active-directory.md) pour créer au moins deux utilisateurs pour votre locataire Azure AD. Veillez à modifier le **rôle d'annuaire** pour créer les types de compte :

* Administrateur général
* Utilisateur

## <a name="enable-authentication"></a>3. Activer l'authentification Azure AD sur la passerelle VPN

1. Recherchez l'ID du répertoire que vous souhaitez utiliser pour l'authentification. Celui-ci est répertorié dans la section Propriétés de la page Active Directory.

    ![ID du répertoire](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Copiez l'ID du répertoire.

3. Connectez-vous au portail Azure en tant qu'utilisateur doté du rôle d'**Administrateur général**.

4. Octroyez ensuite le consentement administrateur. Copiez et collez l'URL correspondant à votre emplacement de déploiement sur la barre d'adresse de votre navigateur :

    Public

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Allemagne

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Si vous y êtes invité, sélectionnez le compte d'**Administrateur général**.

    ![ID du répertoire](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Lorsque vous y êtes invité, sélectionnez **Accepter**.

    ![Acceptation](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Sous votre instance d'Azure AD, dans **Applications d'entreprise**, vous pouvez voir que **VPN Azure** apparaît dans la liste.

    ![VPN Azure](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Si vous ne disposez pas d’un environnement de point à site opérationnel, suivez les instructions de création. Consultez l’article [Créer un VPN de point à site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) pour créer et configurer une passerelle VPN de point à site avec l’authentification par certificat native Azure. 

    > [!IMPORTANT]
    > La référence De base n’est pas prise en charge pour OpenVPN.

9. Activez l'authentification Azure AD sur la passerelle VPN en exécutant les commandes suivantes, tout en veillant à les modifier pour refléter votre propre environnement :

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24 -VpnClientProtocol OpenVPN
    ```

10. Créez et téléchargez le profil en exécutant les commandes suivantes. Modifiez les valeurs -ResourceGroupName et -Name pour qu’elles correspondent aux vôtres.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

11. Après avoir exécuté les commandes, vous obtenez un résultat semblable à celui présenté ci-dessous. Copiez l'URL du résultat dans votre navigateur pour télécharger le fichier zip du profil.

    ![VPN Azure](./media/openvpn-create-azure-ad-tenant/profile.png)

12. Extrayez le fichier zip téléchargé.

13. Accédez au dossier « AzureVPN » décompressé.

14. Notez l'emplacement du fichier « azurevpnconfig.xml ». Le fichier azurevpnconfig.xml contient la configuration de la connexion VPN et peut être importé directement dans l'application Azure VPN Client. Vous pouvez également distribuer ce fichier à tous les utilisateurs qui ont besoin de se connecter par e-mail ou par d'autres moyens. L'utilisateur doit disposer d'informations d'identification Azure AD valides pour se connecter.

## <a name="next-steps"></a>Étapes suivantes

Pour vous connecter à votre réseau virtuel, vous devez créer et configurer un profil de client VPN. Consultez [Configurer un client VPN pour les connexions VPN P2S](openvpn-azure-ad-client.md).
