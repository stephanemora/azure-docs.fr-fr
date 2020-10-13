---
title: 'Passerelle VPN : Locataire Azure AD pour les connexions VPN P2S : Authentification Azure AD'
description: Apprenez à configurer un locataire Azure AD pour l'authentification Open VPN P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 9ee5959c124636e64ef73b901fbc461f36cd27f9
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91817302"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Créer un locataire Azure Active Directory pour les connexions de protocole OpenVPN P2S

Pour vous connecter à votre réseau virtuel, vous pouvez utiliser l'authentification par certificat ou l'authentification RADIUS. Toutefois, lorsque vous utilisez le protocole Open VPN, vous pouvez également utiliser l'authentification Azure Active Directory. Cet article vous aide à configurer un locataire Azure AD pour l'authentification Open VPN P2S.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Vérifier le locataire Azure AD

Vérifiez que vous disposez d'un locataire Azure AD. Si vous n'avez pas de locataire Azure AD, créez-en un en suivant la procédure décrite dans l'article [Créer un locataire](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nom de l'organisation
* Nom de domaine initial

Exemple :

   ![Nouveau locataire Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Créer des utilisateurs de locataire Azure AD

Votre client Azure AD doit disposer des comptes suivants : un compte d'administrateur général et un compte d'utilisateur principal. Le compte d'utilisateur principal est utilisé comme compte d'intégration principal (compte de service). Lorsque vous créez un compte d'utilisateur de locataire Azure AD, vous définissez le rôle d'annuaire en fonction du type d'utilisateur que vous souhaitez créer.

Utilisez les étapes décrites dans [cet article](../active-directory/fundamentals/add-users-azure-active-directory.md) pour créer au moins deux utilisateurs pour votre locataire Azure AD. Veillez à modifier le **rôle d'annuaire** pour créer les types de compte :

* Administrateur général
* Utilisateur

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Activer l'authentification Azure AD sur la passerelle VPN

1. Recherchez l'ID du répertoire que vous souhaitez utiliser pour l'authentification. Celui-ci est répertorié dans la section Propriétés de la page Active Directory.

    ![Capture d’écran représentant la page « Propriétés de l’annuaire » dans laquelle l’option « ID du répertoire » est mise en surbrillance.](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Copiez l'ID du répertoire.

3. Connectez-vous au portail Azure en tant qu'utilisateur doté du rôle d'**Administrateur général**.

4. Octroyez ensuite le consentement administrateur. Copiez et collez l'URL correspondant à votre emplacement de déploiement sur la barre d'adresse de votre navigateur :

    Public

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Allemagne

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Si vous y êtes invité, sélectionnez le compte d'**Administrateur général**.

    ![ID du répertoire](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Lorsque vous y êtes invité, sélectionnez **Accepter**.

    ![Capture d’écran montrant le message Autorisations demandées - Accepter pour votre organisation avec les détails et l’option d’acceptation.](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Sous votre instance d'Azure AD, dans **Applications d'entreprise**, vous pouvez voir que **VPN Azure** apparaît dans la liste.

    ![Capture d’écran représentant la page « Toutes les applications ».](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Si vous ne disposez pas d’un environnement de point à site opérationnel, suivez les instructions de création. Pour créer et configurer une passerelle VPN de point à site, consultez [Créer un réseau VPN de point à site](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 

    > [!IMPORTANT]
    > La référence De base n’est pas prise en charge pour OpenVPN.

9. Activez l’authentification Azure AD sur la passerelle VPN en accédant à **Configuration point à site** et en choisissant **OpenVPN (SSL)** comme **Type de tunnel**. Sélectionnez **Azure Active Directory** comme **Type d’authentification**, puis renseignez les informations sous la section **Azure Active Directory**.

    ![VPN Azure](./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png)


   > [!NOTE]
   > Veillez à inclure une barre oblique finale à la fin de la valeur `AadIssuerUri`. À défaut, la connexion peut échouer.

10. Créez et téléchargez le profil en cliquant sur le lien **Télécharger le client VPN**.

11. Extrayez le fichier zip téléchargé.

12. Accédez au dossier « AzureVPN » décompressé.

13. Notez l'emplacement du fichier « azurevpnconfig.xml ». Le fichier azurevpnconfig.xml contient la configuration de la connexion VPN et peut être importé directement dans l'application Azure VPN Client. Vous pouvez également distribuer ce fichier à tous les utilisateurs qui ont besoin de se connecter par e-mail ou par d'autres moyens. L'utilisateur doit disposer d'informations d'identification Azure AD valides pour se connecter.

## <a name="next-steps"></a>Étapes suivantes

Pour vous connecter à votre réseau virtuel, vous devez créer et configurer un profil de client VPN. Consultez [Configurer un client VPN pour les connexions VPN P2S](openvpn-azure-ad-client.md).
