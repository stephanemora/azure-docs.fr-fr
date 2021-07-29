---
title: 'Créer un locataire Azure AD pour les connexions VPN P2S : Authentification Azure AD'
titleSuffix: Azure VPN Gateway
description: Découvrez comment configurer un locataire Azure AD pour l’authentification Azure AD P2S – Protocole OpenVPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 05/27/2021
ms.author: cherylmc
ms.openlocfilehash: 34ea18e67752ed28986a08c4132ca10fbedce3c6
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110652474"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Créer un locataire Azure Active Directory pour les connexions de protocole OpenVPN P2S

Lorsque vous vous connectez à votre réseau virtuel à l’aide d’une connexion point à site, vous avez le choix du protocole à utiliser. Le protocole que vous utilisez détermine les options d’authentification qui sont à votre disposition. Si vous souhaitez utiliser l’authentification Azure Active Directory, vous pouvez le faire lorsque vous utilisez le protocole OpenVPN. Cet article vous aide à configurer un locataire Azure AD. Pour plus d’informations sur les protocoles et l’authentification point à site, consultez [À propos du VPN point à site](point-to-site-about.md).

[!INCLUDE [OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Vérifier le locataire Azure AD

Vérifiez que vous disposez d'un locataire Azure AD. Si vous n'avez pas de locataire Azure AD, créez-en un en suivant la procédure décrite dans l'article [Créer un locataire](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nom de l'organisation
* Nom de domaine initial

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/newtenant.png" alt-text="Capture d’écran de la page de création d’un répertoire." border="false":::

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Créer des utilisateurs de locataire Azure AD

Votre client Azure AD doit disposer des comptes suivants : un compte d'administrateur général et un compte d'utilisateur principal. Le compte d'utilisateur est utilisé comme compte d'intégration (compte de service). Lorsque vous créez un compte d'utilisateur de locataire Azure AD, vous définissez le rôle d'annuaire en fonction du type d'utilisateur que vous souhaitez créer.

Suivez les étapes décrites dans [Ajouter ou supprimer des utilisateurs - Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md) afin de créer au moins deux utilisateurs pour votre locataire Azure AD. Veillez à modifier le **rôle d'annuaire** pour créer les types de compte :

* Administrateur général
* Utilisateur

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Activer l'authentification Azure AD sur la passerelle VPN

1. Recherchez l'ID du répertoire que vous souhaitez utiliser pour l'authentification. Celui-ci est répertorié dans la section Propriétés de la page Active Directory.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/directory-id.png" alt-text="Capture d'écran des Propriétés du répertoire." lightbox="./media/openvpn-create-azure-ad-tenant/directory-id.png":::

1. Copiez l'ID du répertoire.

1. Connectez-vous au portail Azure en tant qu'utilisateur doté du rôle d'**Administrateur général**.

1. Octroyez ensuite le consentement administrateur. Copiez et collez l'URL correspondant à votre emplacement de déploiement sur la barre d'adresse de votre navigateur :

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

   > [!NOTE]
   > Si vous utilisez un compte d’administrateur général qui n’est pas natif pour le locataire Azure AD pour fournir un consentement, remplacez « Common » par l’ID d’annuaire Azure AD dans l’URL. Dans d’autres cas, vous devrez peut-être également remplacer « Common » par votre ID d’annuaire.
   >

1. Si vous y êtes invité, sélectionnez le compte d'**Administrateur général**.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/pick.png" alt-text="Capture présentant la page Sélection du compte." border="false":::
1. Lorsque vous y êtes invité, sélectionnez **Accepter**.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/accept.jpg" alt-text="Capture d’écran montrant le message Autorisations demandées - Accepter pour votre organisation avec les détails et l’option d’acceptation." border="false":::
1. Sous votre instance d'Azure AD, dans **Applications d'entreprise**, vous pouvez voir que **VPN Azure** apparaît dans la liste.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azurevpn.png" alt-text="Capture d'écran représentant la page Toutes les applications." lightbox="./media/openvpn-create-azure-ad-tenant/azurevpn.png" :::
1. Si vous ne disposez pas d’un environnement de point à site opérationnel, suivez les instructions de création. Pour créer et configurer une passerelle VPN de point à site, consultez [Créer un réseau VPN de point à site](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

    > [!IMPORTANT]
    > La référence De base n’est pas prise en charge pour OpenVPN.

1. Activez l’authentification Azure AD sur la passerelle VPN en accédant à **Configuration point à site** et en choisissant **OpenVPN (SSL)** comme **Type de tunnel**. Sélectionnez **Azure Active Directory** comme **Type d'authentification**, puis renseignez la section **Azure Active Directory**.

   * **Locataire :** TenantID du locataire Azure AD ```https://login.microsoftonline.com/{AzureAD TenantID}/```

   * **Public** : Identifiant de l’application d’entreprise Azure AD « Azure VPN »

       * Entrez 41b23e61-6c1e-4545-b367-cd054e0ed4b4 pour Azure Public
       * Entrez 51bb15d4-3a4f-4EBF-9dca-40096fe32426 pour Azure Government
       * Entrez 538ee9e6-310a-468d-afef-ea97365856a9 pour Azure German
       * Entrez 49f817b6-84AE-4cc0-928c-73f27289b3aa pour Azure China21Vianet


   * **Émetteur** : URL de Secure Token Service (Service d'émission de jeton de sécurité - STS) ```https://sts.windows.net/{AzureAD TenantID}/```


   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png" alt-text="Capture d’écran montrant les paramètres du type de Tunnel, du type Authentification et des paramètres de Azure Active Directory." border="false":::

   > [!NOTE]
   > Veillez à inclure une barre oblique finale à la fin de la valeur `AadIssuerUri`. À défaut, la connexion peut échouer.
   >

1. Créez et téléchargez le profil en cliquant sur le lien **Télécharger le client VPN**.

1. Extrayez le fichier zip téléchargé.

1. Accédez au dossier « AzureVPN » décompressé.

1. Notez l'emplacement du fichier « azurevpnconfig.xml ». Le fichier azurevpnconfig.xml contient la configuration de la connexion VPN et peut être importé directement dans l'application Azure VPN Client. Vous pouvez également distribuer ce fichier à tous les utilisateurs qui ont besoin de se connecter par e-mail ou par d'autres moyens. L'utilisateur doit disposer d'informations d'identification Azure AD valides pour se connecter.

## <a name="next-steps"></a>Étapes suivantes

Créez et configurez un profil client VPN. Consultez [Configurer un client VPN pour les connexions VPN P2S](openvpn-azure-ad-client.md).
