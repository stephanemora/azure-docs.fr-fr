---
title: 'Passerelle VPN : Locataire Azure AD pour différents groupes d’utilisateurs : Authentification Azure AD'
description: Vous pouvez utiliser un VPN P2S pour vous connecter à votre réseau virtuel à l'aide de l'authentification Azure AD
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: 90244b9dcf30c2ef01d4e57c9d8e35fa1d71f434
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985643"
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
   ```

> [!NOTE]
> N’utilisez pas l’ID d’application du client VPN Azure dans les commandes ci-dessus, car il accorde à tous les utilisateurs l’accès à la passerelle VPN. Utilisez l’ID de la ou des applications que vous avez inscrites.

## <a name="hub"></a>7. Modifier l’affectation du hub

1. Accédez au panneau **Hubs** situé sous le WAN virtuel.
2. Sélectionnez le hub à associer à la configuration du serveur VPN, puis cliquez sur le bouton de sélection (...).

   ![nouveau site](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Cliquez sur **Modifier le hub virtuel**.
4. Cochez la case **Inclure la passerelle point à site** et sélectionnez l’**unité d’échelle de passerelle** souhaitée.

   ![nouveau site](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Entrez le **Pool d’adresses** à partir duquel les clients VPN se verront attribuer des adresses IP.
6. Cliquez sur **Confirmer**.
7. L’opération peut prendre jusqu’à 30 minutes.

## <a name="device"></a>8. Télécharger le profil VPN

Utilisez le profil VPN pour configurer vos clients.

1. Sur la page de votre WAN virtuel, cliquez sur **Configurations de VPN utilisateur**.
2. En haut de la page, cliquez sur **Télécharger la configuration de VPN utilisateur**.
3. Une fois que le fichier a terminé la création, vous pouvez cliquer sur le lien pour le télécharger.
4. Utilisez le fichier de profil pour configurer les clients VPN.

4. Extrayez le fichier zip téléchargé.

5. Accédez au dossier « AzureVPN » décompressé.

6. Notez l'emplacement du fichier « azurevpnconfig.xml ». Le fichier azurevpnconfig.xml contient la configuration de la connexion VPN et peut être importé directement dans l'application Azure VPN Client. Vous pouvez également distribuer ce fichier à tous les utilisateurs qui ont besoin de se connecter par e-mail ou par d'autres moyens. L'utilisateur doit disposer d'informations d'identification Azure AD valides pour se connecter.
## <a name="configure-user-vpn-clients"></a>Configurer les clients VPN utilisateur

Pour établir une connexion, vous devez télécharger Azure VPN Client (préversion) et importer le profil de client VPN téléchargé au cours des étapes précédentes sur chaque ordinateur à connecter au réseau virtuel.

> [!NOTE]
> L’authentification Azure AD est prise en charge uniquement pour les connexions de protocole OpenVPN®.
>

#### <a name="to-download-the-azure-vpn-client"></a>Pour télécharger le client VPN Azure

Utilisez ce [lien](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) pour télécharger le client Azure VPN (préversion).

#### <a name="import"></a>Pour importer un profil client

1. Dans la page, sélectionnez **Importer**.

    ![importation](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Accédez au fichier XML de profil et sélectionnez-le. Ensuite, sélectionnez **Ouvrir**.

    ![importation](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Spécifiez le nom du profil et sélectionnez **Enregistrer**.

    ![importation](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Sélectionnez **Se connecter** pour vous connecter au VPN.

    ![importation](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Une fois que vous êtes connecté, l’icône devient verte et indique **Connecté**.

    ![importation](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>Pour supprimer un profil client

1. Sélectionnez le bouton de sélection (...) en regard du profil de client à supprimer. Sélectionnez ensuite **Supprimer**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Sélectionnez **Supprimer** pour effectuer la suppression.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Diagnostiquer les problèmes de connexion

1. Pour diagnostiquer les problèmes de connexion, vous pouvez utiliser l’outil **Diagnostiquer**. Sélectionnez le bouton de sélection (...) en regard de la connexion VPN à diagnostiquer pour afficher le menu. Sélectionnez ensuite **Diagnostiquer**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Sur la page **Propriétés de connexion**, sélectionnez **Exécuter le diagnostic**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Connectez-vous avec vos informations d’identification.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Affichez les résultats du diagnostic.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>Afficher votre WAN virtuel

1. Accédez au WAN virtuel.
2. Dans la page Vue d’ensemble, chaque point sur la carte représente un hub. Placez le curseur sur n’importe quel point pour afficher le résumé de l’intégrité du hub.
3. Dans la section des hubs et des connexions, vous pouvez afficher l’état du hub, le site, la région, l’état de la connexion VPN et les octets entrés et sortis.

## <a name="viewhealth"></a>Afficher l’intégrité des ressources

1. Accédez à votre réseau étendu.
2. Sur la page de votre WAN, dans la section **SUPPORT + Dépannage**, cliquez sur **Intégrité** pour afficher votre ressource.


## <a name="cleanup"></a>Supprimer des ressources

Quand vous n’avez plus besoin de ces ressources, vous pouvez utiliser [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources et toutes les ressources qu’il contient. Remplacez « myResourceGroup » par le nom de votre groupe de ressources et exécutez la commande PowerShell suivante :

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le WAN virtuel, consultez la page [Vue d'ensemble de WAN virtuel](virtual-wan-about.md).
