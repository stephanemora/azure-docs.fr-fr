---
title: 'Configurer des clients VPN pour les connexions P2S de protocole OpenVPN : authentification Azure AD'
description: Découvrez comment configurer un client VPN pour qu’il se connecte à un réseau virtuel en utilisant un VPN point à site de passerelle VPN et l’authentification Azure Active Directory.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/28/2021
ms.author: alzam
ms.openlocfilehash: ba3733f16353ad1e921430ca7699212a60d5ff2f
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108754952"
---
# <a name="azure-active-directory-authentication-configure-a-vpn-client-for-p2s-openvpn-protocol-connections"></a>Authentification Azure Active Directory : Configurer un client VPN pour les connexions P2S de protocole OpenVPN

Cet article vous aide à configurer un client VPN pour qu’il se connecte à un réseau virtuel à l’aide d’un VPN point à site et de l’authentification Azure Active Directory. Avant de pouvoir vous connecter et vous authentifier à l’aide d’Azure AD, vous devez d’abord configurer votre locataire Azure AD. Pour plus d’informations, consultez [Configurer un locataire Azure AD](openvpn-azure-ad-tenant.md). Pour plus d’informations sur la connexion point à site, consultez l’article [À propos du VPN point à site](point-to-site-about.md).

[!INCLUDE [OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="working-with-client-profiles"></a><a name="profile"></a>Utilisation de profils clients

Pour chaque ordinateur appelé à se connecter au réseau virtuel via le client VPN, vous devez télécharger Azure VPN Client sur l’ordinateur et configurer un profil client VPN. Si vous souhaitez configurer plusieurs ordinateurs, vous pouvez créer un profil client sur un ordinateur, l’exporter, puis l’importer sur les autres ordinateurs.

### <a name="to-download-the-azure-vpn-client"></a>Pour télécharger le client VPN Azure

1. Téléchargez [Azure VPN Client](https://go.microsoft.com/fwlink/?linkid=2117554) sur l’ordinateur.
1. Vérifiez qu’Azure VPN Client est autorisé à s’exécuter en arrière-plan. Pour vérifier et activer les autorisations, accédez à **Démarrer -> Paramètres -> Confidentialité -> Applications en arrière-plan**.

   * Sous **Applications en arrière-plan**, vérifiez que l’option **Autoriser les applications à s’exécuter en arrière-plan** est définie sur **Activé**.
   * Sous **Choisir les applications qui peuvent s’exécuter en arrière-plan**, définissez l’option **Azure VPN Client** sur **Activé**.

     ![Capture d’écran des autorisations.](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Pour créer un profil client basé sur un certificat

Lorsque vous utilisez un profil basé sur un certificat, assurez-vous que les certificats appropriés sont installés sur l’ordinateur client. Pour plus d’informations sur les certificats, consultez [Installer un certificat client](point-to-site-how-to-vpn-client-install-azure-cert.md).

![Capture d’écran de l’authentification par certificat.](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Pour créer un profil client RADIUS

![Capture d’écran de l’authentification RADIUS.](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Le secret du serveur peut être exporté dans le profil client VPN P2S.  Vous trouverez des instructions sur l’exportation d’un profil client [ici](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Pour exporter et distribuer un profil client

Une fois que vous disposez d’un profil fonctionnel et que vous devez le distribuer à d’autres utilisateurs, vous pouvez l’exporter en procédant comme suit :

1. Mettez en surbrillance le profil client VPN que vous souhaitez exporter, sélectionnez les points de sélection ( **...** ), puis sélectionnez **Exporter**.

    ![Capture d’écran montrant la page « Client VPN Azure », avec les points de suspension sélectionnés et « Exporter » mis en surbrillance.](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Sélectionnez l’emplacement dans lequel vous souhaitez enregistrer ce profil, laissez le nom de fichier tel quel, puis sélectionnez **Enregistrer** pour enregistrer le fichier XML.

    ![export](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Pour importer un profil client

1. Dans la page, sélectionnez **Importer**.

    ![Capture d’écran montrant le bouton « Ajouter » sélectionné et l’action « Importer » en surbrillance dans la partie inférieure gauche de la fenêtre.](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Accédez au fichier XML de profil et sélectionnez-le. Une fois le fichier sélectionné, sélectionnez **Ouvrir**.

    ![Capture d’écran montrant un fichier de profil x m l sélectionné.](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Spécifiez le nom du profil et sélectionnez **Enregistrer**.

    ![Capture d’écran montrant le « nom de la connexion » mis en surbrillance et le bouton « Enregistrer » sélectionné.](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Sélectionnez **Se connecter** pour vous connecter au VPN.

    ![Capture d’écran montrant le VPN et le bouton « Connexion » sélectionné.](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Une fois que vous êtes connecté, l’icône devient verte et indique **Connecté**.

    ![importer](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Pour supprimer un profil client

1. Sélectionnez les points de suspension en regard du profil client que vous souhaitez supprimer. Sélectionnez ensuite **Supprimer**.

    ![Capture d’écran montrant les ellipses et l’option « Supprimer » sélectionnée.](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Sélectionnez **Supprimer** pour effectuer la suppression.

    ![supprimer](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Créer une connexion

1. Sur la page, sélectionnez **+** , puis **+ Ajouter**.

    ![Capture d’écran montrant le bouton « Ajouter » sélectionné.](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Remplissez les informations de connexion. Si vous ne connaissez pas les valeurs, contactez votre administrateur. Quand vous avez rempli tous les champs, sélectionnez **Enregistrer**.

    ![Capture d’écran montrant les propriétés de la connexion VPN mises en surbrillance et le bouton « Enregistrer » sélectionné.](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Sélectionnez **Se connecter** pour vous connecter au VPN.

    ![Capture d’écran montrant le VPN et le bouton « Connexion » sélectionné.](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Sélectionnez les informations d’identification appropriées, puis sélectionnez **Continuer**.

    ![Capture d’écran montrant les informations d’identification de l’exemple en surbrillance et le bouton « Continuer » sélectionné.](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Une fois que vous êtes connecté, l’icône devient verte et indique **Connecté**.

    ![connection](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Pour se connecter automatiquement

Ces étapes vous aident à configurer votre connexion pour qu’elle se connecte automatiquement avec Always-on.

1. Sur la page d’accueil de votre client VPN, sélectionnez **Paramètres VPN**.

    ![Capture d’écran de la page d’accueil VPN avec l’option « Paramètres VPN » sélectionnée.](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Sélectionnez **Oui** dans la boîte de dialogue de commutateur d’applications.

    ![Capture d’écran de la boîte de dialogue « Souhaitiez-vous changer d’application ? » avec le bouton « Oui » sélectionné.](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Vérifiez que la connexion que vous souhaitez définir n’est pas déjà établie, puis mettez en surbrillance le profil et activez la case à cocher **Se connecter automatiquement**.

    ![Capture d’écran de la fenêtre « Paramètres », avec la case « Se connecter automatiquement » activée.](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Sélectionnez **Se connecter** pour initier la connexion VPN.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnostiquer les problèmes de connexion

1. Pour diagnostiquer les problèmes de connexion, vous pouvez utiliser l’outil **Diagnostiquer**. Sélectionnez les points de suspension ( **...** ) en regard de la connexion VPN que vous souhaitez diagnostiquer pour afficher le menu. Sélectionnez ensuite **Diagnostiquer**.

    ![Capture d’écran des ellipses et « Diagnostiquer » sélectionnés.](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Dans la page **Propriétés de connexion**, sélectionnez **Exécuter le diagnostic**.

    ![Capture d’écran montrant la page « Propriétés de connexion » avec l’option « Exécuter le diagnostic » sélectionnée.](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Connectez-vous avec vos informations d’identification.

    ![Capture d’écran montrant la boîte de dialogue « Connectez-vous » avec un « compte professionnel ou scolaire » sélectionné.](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Examinez les résultats du diagnostic.

    ![diagnostiquer](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Questions fréquentes (FAQ)

### <a name="is-the-azure-vpn-client-supported-with-windows-fips-mode"></a>Azure VPN Client est-il pris en charge avec le mode Windows FIPS ?

Oui, avec le correctif logiciel [KB4577063](https://support.microsoft.com/help/4577063/windows-10-update-kb4577063).

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Comment faire pour ajouter des suffixes DNS au client VPN ?

Vous pouvez modifier le fichier XML de profil téléchargé et ajouter les balises **\<dnssuffixes>\<dnssufix> \</dnssufix>\</dnssuffixes>** .

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Comment ajouter des serveurs DNS personnalisés au client VPN ?

Vous pouvez modifier le fichier XML de profil téléchargé et ajouter les balises **\<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** .

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> Le client OpenVPN Azure AD utilise les entrées de la table de stratégie de résolution de noms DNS, ce qui signifie que les serveurs DNS ne seront pas listés sous la sortie de `ipconfig /all`. Pour confirmer vos paramètres DNS en cours d’utilisation, consultez [Get-DnsClientNrptPolicy](/powershell/module/dnsclient/get-dnsclientnrptpolicy) dans PowerShell.
>

### <a name="can-i-configure-split-tunneling-for-the-vpn-client"></a><a name="split"></a>Puis-je configurer le tunneling fractionné pour le client VPN ?

Le tunneling fractionné est configuré par défaut pour le client VPN.

### <a name="how-do-i-direct-all-traffic-to-the-vpn-tunnel-forced-tunneling"></a><a name="forced-tunnel"></a>Comment diriger l’ensemble du trafic vers le tunnel VPN (tunneling forcé) ?

Vous pouvez configurer le tunneling forcé selon deux méthodes différentes : en publiant des routes personnalisées ou en modifiant le fichier XML de profil.    

> [!NOTE]
> Aucune connectivité Internet n’est fournie via la passerelle VPN. De ce fait, l’ensemble du trafic lié à Internet est abandonné.
>

* **Publier des routes personnalisées :** vous pouvez publier les routes personnalisées 0.0.0.0/1 et 128.0.0.0/1. Pour plus d’informations, consultez [Publier des routes personnalisées pour des clients VPN point à site](vpn-gateway-p2s-advertise-custom-routes.md).

* **Profil XML :** vous pouvez modifier le fichier XML de profil téléchargé pour ajouter les balises **\<includeroutes>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</includeroutes>** .


    ```
    <azvpnprofile>
    <clientconfig>
          
        <includeroutes>
            <route>
                <destination>0.0.0.0</destination><mask>1</mask>
            </route>
            <route>
                <destination>128.0.0.0</destination><mask>1</mask>
            </route>
        </includeroutes>
           
    </clientconfig>
    </azvpnprofile>
    ```


### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Comment ajouter des routes personnalisées au client VPN ?

Vous pouvez modifier le fichier XML de profil téléchargé et ajouter les balises **\<includeroutes>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</includeroutes>** .

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Comment bloquer (exclure) des routes à partir du client VPN ?

Vous pouvez modifier le fichier XML de profil téléchargé et ajouter les balises **\<excluderoutes>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</excluderoutes>** .

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>Puis-je importer le profil à partir d’une invite de ligne de commande ?

Vous pouvez importer le profil à partir d’une invite de ligne de commande en plaçant le fichier **azurevpnconfig.xml** téléchargé dans le dossier **%userprofile%\AppData\Local\Packages\Microsoft.AzureVpn_8wekyb3d8bbwe\LocalState** et en exécutant la commande suivante :

```
azurevpn -i azurevpnconfig.xml 
```
Pour forcer l’importation, utilisez le commutateur **-f**.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Créer un locataire Azure Active Directory qui utilise l’authentification Azure AD pour les connexions Open VPN P2S](openvpn-azure-ad-tenant.md).