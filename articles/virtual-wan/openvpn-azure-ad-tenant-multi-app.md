---
title: 'Virtual WAN : Locataire Azure AD pour différents groupes d’utilisateurs : Authentification Azure AD'
description: Vous pouvez utiliser un VPN P2S pour vous connecter à votre réseau virtuel à l'aide de l'authentification Azure AD
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: a1471226bcc22373904e65f95d0c07c2d5041f55
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84749830"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Créer un locataire Azure Active Directory pour les connexions de protocole OpenVPN P2S

Pour vous connecter à votre réseau virtuel, vous pouvez utiliser l'authentification par certificat ou l'authentification RADIUS. Toutefois, lorsque vous utilisez le protocole Open VPN, vous pouvez également utiliser l'authentification Azure Active Directory. Si vous souhaitez que différents ensembles d’utilisateurs puissent se connecter à différentes passerelles, vous pouvez inscrire plusieurs applications dans AD et les lier à différentes passerelles.

Cet article vous aide à configurer un locataire Azure AD pour l’authentification P2S OpenVPN, ainsi qu’à créer et inscrire plusieurs applications dans Azure AD pour autoriser différents accès pour différents utilisateurs et groupes.

> [!NOTE]
> L’authentification Azure AD n’est prise en charge que pour les connexions de protocole OpenVPN&reg;.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. Créer une configuration P2S

Une configuration P2S définit les paramètres permettant de connecter des clients distants.

1. Définissez les variables suivantes en remplaçant les valeurs selon les besoins de votre environnement.

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Exécutez les commandes suivantes pour créer la configuration :

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > N’utilisez pas l’ID d’application du client VPN Azure dans les commandes ci-dessus : il accorde l’accès à la passerelle à tous les utilisateurs. Utilisez l’ID de la ou des applications que vous avez inscrites.

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7. Modifier l’affectation du hub

1. Accédez au panneau **Hubs** situé sous le WAN virtuel.

2. Sélectionnez le hub à associer à la configuration du serveur VPN, puis cliquez sur le bouton de sélection (...).

    ![nouveau site](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Cliquez sur **Modifier le hub virtuel**.

4. Cochez la case **Inclure la passerelle point à site** et sélectionnez l’**unité d’échelle de passerelle** souhaitée.

    ![nouveau site](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Entrez le **Pool d’adresses** à partir duquel les clients VPN se verront attribuer des adresses IP.

6. Cliquez sur **Confirmer**.

7. L’opération peut prendre jusqu’à 30 minutes.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. Télécharger le profil VPN

Utilisez le profil VPN pour configurer vos clients.

1. Dans la page de votre WAN virtuel, cliquez sur **Configurations de VPN utilisateur**.

2. En haut de la page, cliquez sur **Télécharger la configuration de VPN utilisateur**.

3. Une fois que le fichier a terminé la création, vous pouvez cliquer sur le lien pour le télécharger.

4. Utilisez le fichier de profil pour configurer les clients VPN.

5. Extrayez le fichier zip téléchargé.

6. Accédez au dossier « AzureVPN » décompressé.

7. Notez l’emplacement du fichier « azurevpnconfig.xml ». Le fichier azurevpnconfig.xml contient la configuration de la connexion VPN et peut être importé directement dans l'application Azure VPN Client. Vous pouvez également distribuer ce fichier à tous les utilisateurs qui ont besoin de se connecter par e-mail ou par d'autres moyens. L'utilisateur doit disposer d'informations d'identification Azure AD valides pour se connecter.

## <a name="9-configure-user-vpn-clients"></a>9. Configurer les clients VPN utilisateurs

Pour établir une connexion, vous devez télécharger Azure VPN Client et importer le profil de client VPN téléchargé au cours des étapes précédentes sur chaque ordinateur à connecter au réseau virtuel.

> [!NOTE]
> L’authentification Azure AD n’est prise en charge que pour les connexions de protocole OpenVPN&reg;.
>

#### <a name="to-download-the-azure-vpn-client"></a>Pour télécharger le client VPN Azure

Utilisez ce [lien](https://go.microsoft.com/fwlink/?linkid=2117554) pour télécharger Azure VPN Client.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Pour importer un profil client

1. Dans la page, sélectionnez **Importer**.

    ![importer](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Accédez au fichier XML de profil et sélectionnez-le. Une fois le fichier sélectionné, sélectionnez **Ouvrir**.

    ![importer](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Spécifiez le nom du profil et sélectionnez **Enregistrer**.

    ![importer](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. Sélectionnez **Se connecter** pour vous connecter au VPN.

    ![importer](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. Une fois que vous êtes connecté, l’icône devient verte et indique **Connecté**.

    ![importer](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Pour supprimer un profil client

1. Sélectionnez le bouton de sélection (...) en regard du profil de client à supprimer. Sélectionnez ensuite **Supprimer**.

    ![supprimer](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Sélectionnez **Supprimer** pour effectuer la suppression.

    ![supprimer](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>Pour diagnostiquer des problèmes de connexion

1. Pour diagnostiquer les problèmes de connexion, vous pouvez utiliser l’outil **Diagnostiquer**. Sélectionnez le bouton de sélection (...) en regard de la connexion VPN à diagnostiquer pour afficher le menu. Sélectionnez ensuite **Diagnostiquer**.

    ![diagnostiquer](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. Dans la page **Propriétés de connexion**, sélectionnez **Exécuter le diagnostic**.

    ![diagnostiquer](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Connectez-vous avec vos informations d’identification.

    ![diagnostiquer](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Examinez les résultats du diagnostic.

    ![diagnostiquer](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. Afficher votre WAN virtuel

1. Accédez au WAN virtuel.

2. Dans la page Vue d’ensemble, chaque point sur la carte représente un hub.

3. Dans la section des hubs et des connexions, vous pouvez voir l’état du hub, le site, la région, l’état de la connexion VPN et les octets entrés et sortis.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Supprimer des ressources

Quand vous n’avez plus besoin de ces ressources, vous pouvez utiliser [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources et toutes les ressources qu’il contient. Remplacez « myResourceGroup » par le nom de votre groupe de ressources et exécutez la commande PowerShell suivante :

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Virtual WAN, consultez la page [Vue d’ensemble de Virtual WAN](virtual-wan-about.md).
