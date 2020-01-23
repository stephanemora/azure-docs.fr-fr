---
title: 'Passerelle VPN : Locataire Azure AD pour différents groupes d’utilisateurs : Authentification Azure AD'
description: Vous pouvez utiliser un VPN P2S pour vous connecter à votre réseau virtuel à l'aide de l'authentification Azure AD
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: b390b5f8b00f61994db820a3af7bce26a3e0a30d
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934886"
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
    -   Entrez **538ee9e6-310a-468d-afef-ea97365856a9** pour Azure **Germany**
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

## <a name="site"></a>6. Créer une configuration P2S

Une configuration P2S définit les paramètres permettant de connecter des clients distants.

1. Définissez les variables suivantes en remplaçant les valeurs selon les besoins de votre environnement.

   ```powershell
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Exécutez les commandes suivantes pour créer la configuration :

   ```powershell
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
> [!NOTE]
> Do not use the Azure VPN client's application ID in the commands above as it will grant all users access to the VPN gateway. Use the ID of the application(s) you registered.

## <a name="hub"></a>7. Edit hub assignment

1. Navigate to the **Hubs** blade under the virtual WAN.
2. Select the hub that you want to associate the vpn server configuration to and click the ellipsis (...).

   ![new site](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Click **Edit virtual hub**.
4. Check the **Include point-to-site gateway** check box and pick the **Gateway scale unit** that you want.

   ![new site](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Enter the **Address pool** from which the VPN clients will be assigned IP addresses.
6. Click **Confirm**.
7. The operation will can take up to 30 minutes to complete.

## <a name="device"></a>8. Download VPN profile

Use the VPN profile to configure your clients.

1. On the page for your virtual WAN, click **User VPN configurations**.
2. At the top of the  page, click **Download user VPN config**.
3. Once the file has finished creating, you can click the link to download it.
4. Use the profile file to configure the VPN clients.

4. Extract the downloaded zip file.

5. Browse to the unzipped “AzureVPN” folder.

6. Make a note of the location of the “azurevpnconfig.xml” file. The azurevpnconfig.xml contains the setting for the VPN connection and can be imported directly into the Azure VPN Client application. You can also distribute this file to all the users that need to connect via e-mail or other means. The user will need valid Azure AD credentials to connect successfully.
## Configure user VPN clients

To connect, you need to download the Azure VPN Client (Preview) and import the VPN client profile that was downloaded in the previous steps on every computer that wants to connect to the VNet.

> [!NOTE]
> Azure AD authentication is supported only for OpenVPN® protocol connections.
>

#### To download the Azure VPN client

Use this [link](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) to download the Azure VPN Client (Preview).

#### <a name="import"></a>To import a client profile

1. On the page, select **Import**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Browse to the profile xml file and select it. With the file selected, select **Open**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Specify the name of the profile and select **Save**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Select **Connect** to connect to the VPN.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Once connected, the icon will turn green and say **Connected**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>To delete a client profile

1. Select the ellipsis (...) next to the client profile that you want to delete. Then, select **Remove**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Select **Remove** to delete.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Diagnose connection issues

1. To diagnose connection issues, you can use the **Diagnose** tool. Select the ellipsis (...) next to the VPN connection that you want to diagnose to reveal the menu. Then select **Diagnose**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. On the **Connection Properties** page, select **Run Diagnosis**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Sign in with your credentials.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. View the diagnosis results.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>View your virtual WAN

1. Navigate to the virtual WAN.
2. On the Overview page, each point on the map represents a hub. Hover over any point to view the hub health summary.
3. In the Hubs and connections section, you can view hub status, site, region, VPN connection status, and bytes in and out.

## <a name="viewhealth"></a>View your resource health

1. Navigate to your WAN.
2. On your WAN page, in the **SUPPORT + Troubleshooting** section, click **Health** and view your resource.


## <a name="cleanup"></a>Clean up resources

When you no longer need these resources, you can use [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) to remove the resource group and all of the resources it contains. Replace "myResourceGroup" with the name of your resource group and run the following PowerShell command:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le WAN virtuel, consultez la page [Vue d'ensemble de WAN virtuel](virtual-wan-about.md).