---
title: 'Passerelle VPN : Locataire Azure AD pour différents groupes d’utilisateurs : Authentification Azure AD'
description: Vous pouvez utiliser un VPN P2S pour vous connecter à votre réseau virtuel à l'aide de l'authentification Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: alzam
ms.openlocfilehash: 73a47b20010be0b4eadec790dd8fb254f912ff2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474119"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Créer un locataire Azure Active Directory pour les connexions de protocole OpenVPN P2S

Pour vous connecter à votre réseau virtuel, vous pouvez utiliser l'authentification par certificat ou l'authentification RADIUS. Toutefois, lorsque vous utilisez le protocole Open VPN, vous pouvez également utiliser l'authentification Azure Active Directory. Si vous souhaitez que différents ensembles d’utilisateurs puissent se connecter à différentes passerelles VPN, vous pouvez inscrire plusieurs applications dans AD et les lier à différentes passerelles VPN. Cet article vous aide à configurer un locataire Azure AD pour l’authentification P2S OpenVPN et à créer et inscrire plusieurs applications dans Azure AD pour autoriser différents accès pour différents utilisateurs et groupes.

> [!NOTE]
> L’authentification Azure AD est prise en charge uniquement pour les connexions de protocole OpenVPN®.
>

## <a name="tenant"></a>1. Créer le locataire Azure AD

Pour créer un locataire Azure AD, suivez la procédure décrite dans l'article [Créer un locataire](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nom de l'organisation
* Nom de domaine initial

Exemple :

   ![Nouveau locataire Azure AD](./media/openvpn-azure-ad-tenant-multi-app/newtenant.png)

## <a name="users"></a>2. Créer des utilisateurs de locataire Azure AD

Créez ensuite deux comptes d'utilisateur. Créez un compte d'administrateur général et un compte d'utilisateur principal. Le compte d'utilisateur principal est utilisé comme compte d'intégration principal (compte de service). Lorsque vous créez un compte d'utilisateur de locataire Azure AD, vous définissez le rôle d'annuaire en fonction du type d'utilisateur que vous souhaitez créer.

Utilisez les étapes décrites dans [cet article](../active-directory/fundamentals/add-users-azure-active-directory.md) pour créer au moins deux utilisateurs pour votre locataire Azure AD. Veillez à modifier le **rôle d'annuaire** pour créer les types de compte :

* Administrateur général
* Utilisateur

## <a name="enable-authentication"></a>3. Inscrire un client VPN Azure dans un locataire Azure AD

1. Recherchez l'ID du répertoire que vous souhaitez utiliser pour l'authentification. Celui-ci est répertorié dans la section Propriétés de la page Active Directory.

    ![ID du répertoire](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

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

    ![ID du répertoire](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Lorsque vous y êtes invité, sélectionnez **Accepter**.

    ![Acceptation](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Sous votre Azure AD, dans **Applications d’entreprise**, vous verrez **VPN Azure**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/azurevpn.png)

## <a name="enable-authentication"></a>4. Inscrire des applications supplémentaires pour différents utilisateurs/groupes

1. Sous votre Azure Active Directory, cliquez sur **Inscriptions d’applications**, puis **+ nouvelle inscription**

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Dans le panneau **Enregistrer une application**, saisissez le **nom** et sélectionnez les **types de compte pris en charge** souhaités, puis cliquez sur **Enregistrer**

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Une fois que la nouvelle application a été inscrite, cliquez sur **Exposer une API** sous le panneau de l’application.

4. Cliquez sur **+ Ajouter une étendue**.
5. Laissez l’**URI de l’ID d’application** par défaut puis cliquez sur **Enregistrer et continuer**

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app3.png)
6. Renseignez les champs requis et assurez-vous que **État** est **Activé**. Cliquez sur **Ajouter une étendue**

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app4.png)
7. Cliquez sur **Exposer une API** puis sur **+ Ajouter une application client**.  Pour **ID client**, saisissez les valeurs suivantes en fonction du Cloud :
    -   Entrez **41b23e61-6c1e-4545-b367-cd054e0ed4b4** pour Azure **Public**
    -   Entrez **51bb15d4-3a4f-4EBF-9dca-40096fe32426** pour Azure **Government**
    -   Entrez **538ee9e6-310a-468d-afef-ea97365856a9** pour Azure **German**
    -   Entrez **49f817b6-84AE-4cc0-928c-73f27289b3aa** pour Azure **China21Vianet**


8. Cliquez sur **Ajouter une application**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app5.png)
9. Copiez l’**ID d’application (client)** à partir de la page **Vue d’ensemble**. Vous en aurez besoin pour configurer vos passerelles VPN

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Répétez les étapes de cette section (4) pour créer le nombre d’applications nécessaires à vos besoins de sécurité. Chaque application est associée à une passerelle VPN et peut avoir un ensemble différent d’utilisateurs. Une seule application peut être associée à une passerelle.

## <a name="enable-authentication"></a>5. Affecter des utilisateurs à vos applications

1. Sous Azure AD, **Applications d’entreprise**, sélectionnez l’application que vous venez d’inscrire, puis cliquez sur **Propriétés**. Assurez-vous que **Affectation d’utilisateur requise ?** est défini sur **Oui**. Cliquez sur **Enregistrer**.

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Sur la page de l’application, cliquez sur **Utilisateurs et groupes** puis sur **Ajouter un utilisateur**

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/user3.png)
3. Sous **Ajouter une attribution**, cliquez sur **Utilisateurs et groupes**. Sélectionnez les utilisateurs auxquels vous souhaitez autoriser l’accès à cette application VPN. Cliquez sur **Sélectionner**

    ![VPN Azure](./media/openvpn-azure-ad-tenant-multi-app/user4.png)

## <a name="enable-authentication"></a>6. Activer l'authentification Azure AD sur la passerelle VPN

1. Activez l'authentification Azure AD sur la passerelle VPN en exécutant les commandes suivantes, tout en veillant à les modifier pour refléter votre propre environnement :

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
> [!NOTE]
> N’utilisez pas l’ID d’application du client VPN Azure dans les commandes ci-dessus, car il accorde à tous les utilisateurs l’accès à la passerelle VPN. Utilisez l’ID de la ou des applications que vous avez inscrites.

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
