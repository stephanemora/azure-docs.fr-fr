---
title: 'Créer et installer des fichiers config de client VPN P2S : authentification par certificat'
titleSuffix: Azure VPN Gateway
description: Découvrez comment générer et installer des fichiers config de client VPN pour Windows, Linux (strongSwan) et macOS. Cet article s’applique aux configurations de passerelle VPN P2S utilisant l’authentification par certificat.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/15/2021
ms.author: cherylmc
ms.openlocfilehash: efa2c4c120ab54e27126d1f40c433cdc1b66a85e
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114288868"
---
# <a name="generate-and-install-vpn-client-configuration-files-for-p2s-certificate-authentication"></a>Générer et installer des fichiers config de client VPN pour l’authentification par certificat P2S

Lorsque vous vous connectez à un réseau virtuel Azure à l’aide d’une connexion point à site et d’une authentification par certificat, vous utilisez le client VPN installé de façon native sur le système d’exploitation à partir duquel vous vous connectez. Tous les paramètres de configuration nécessaires aux clients VPN sont contenus dans un fichier config zip de client VPN. Les paramètres du fichier zip vous aident à configurer facilement les clients VPN pour Windows, Mac IKEv2 VPN ou Linux.

Les fichiers config du client VPN que vous générez sont spécifiques à la configuration de la passerelle VPN P2S pour le réseau virtuel. Si des modifications ont été apportées à la configuration du VPN de point à site après avoir généré les fichiers (modifications apportées au type de protocole VPN ou au type d’authentification, par exemple), vous devez générer les nouveaux fichiers config du client VPN et appliquer la nouvelle configuration à tous les clients VPN que vous souhaitez connecter.

* Pour plus d’informations sur les connexions point à site, consultez [À propos des VPN point à site](point-to-site-about.md).
* Pour obtenir des instructions sur OpenVPN, consultez [Configurer OpenVPN pour P2S](vpn-gateway-howto-openvpn.md) et [Configurer des clients OpenVPN](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Générer les fichiers de configuration du client VPN

Vous pouvez générer des fichiers de configuration client à l’aide de PowerShell, ou en utilisant le portail Azure. L’une ou l’autre des méthodes retourne le même fichier zip. Décompressez le fichier pour afficher les dossiers suivants :

* Les dossiers **WindowsAmd64** et **WindowsX86**, dans lesquels se trouvent respectivement les packages de programme d’installation pour Windows 32 bits et Windows 64 bits. Le package d’installation **WindowsAmd64** est pour tous les clients Windows 64 bits pris en charge, pas seulement Amd.
* Le dossier **Générique**, dans lequel se trouvent les informations générales utilisées pour créer la configuration de votre client VPN. Le dossier Générique est fourni si le protocole IKEv2 ou SSTP+IKEv2 a été configuré sur la passerelle. S’il n’y a que le protocole SSTP qui a été configuré, alors le dossier Générique n’apparaîtra pas.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Générer les fichiers à l’aide du portail Azure

1. Dans le portail Azure, accédez à la passerelle du réseau virtuel auquel vous souhaitez vous connecter.
1. Dans la page correspondant à la passerelle de réseau virtuel, sélectionnez **Configuration de point à site** pour ouvrir la page Configuration de point à site.
1. En haut de la page Configuration de point à site, sélectionnez **Télécharger le client VPN**. Cela ne permet pas de télécharger le logiciel du client VPN mais de générer le package de configuration utilisé pour configurer les clients VPN. La génération du package de configuration du client prend quelques minutes. Pendant ce temps, vous ne verrez peut-être aucune indication tant que le paquet n’a pas été généré.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/download-client.png" alt-text="Téléchargez la configuration du client VPN." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/download-client.png":::
1. Une fois le package de configuration généré, votre navigateur indique qu’un fichier config zip de client est disponible. Il porte le même nom que votre passerelle. Décompressez le fichier pour afficher les dossiers.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Générer des fichiers à l’aide de PowerShell

1. Lorsque vous générez les fichiers de configuration du client VPN, la valeur de la méthode « -AuthenticationMethod » est « EapTls ». Générez les fichiers de configuration du client VPN à l’aide de la commande suivante :

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Copiez l’URL dans votre navigateur pour télécharger le fichier zip, puis décompressez le fichier pour afficher les dossiers.

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="mac-macos"></a><a name="installmac"></a>Mac (macOS)

Pour vous connecter à Azure, vous devez configurer manuellement le client VPN IKEv2 natif. Azure ne fournit pas un fichier *mobileconfig*. Vous trouverez toutes les informations nécessaires pour la configuration dans le dossier **Générique**. 

Si vous ne voyez pas le dossier Générique dans votre téléchargement, il est probable qu’IKEv2 n’était pas sélectionné comme type de tunnel. Notez que la référence SKU de base de passerelle VPN ne prend pas en charge IKEv2. Sur la passerelle VPN, vérifiez que la référence SKU n’est pas De base. Ensuite, sélectionnez IKEv2 et générez à nouveau le fichier zip pour récupérer le dossier Générique.

Ce dossier contient les fichiers suivants :

* Le fichier **VpnSettings.xml**, qui contient d’importants paramètres tels que l’adresse et le type de tunnel du serveur. 
* Le fichier **VpnServerRoot.cer**, qui contient le certificat racine requis pour valider la passerelle VPN Azure lors de la configuration de la connexion P2S.

Suivez les étapes ci-dessous afin de configurer le client VPN Mac natif pour une authentification par certificat. Ces étapes doivent être effectuées sur chaque Mac que vous souhaitez connecter à Azure.

### <a name="import-root-certificate-file"></a>Importer le fichier du certificat racine

1. Copiez le fichier du certificat racine sur votre Mac. Double-cliquez sur le certificat. Le certificat s’installera automatiquement, ou bien vous verrez la page **Ajouter des certificats**.
1. Dans la page **Ajouter des certificats**, sélectionnez **connexion** dans la liste déroulante.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/login.png" alt-text="La capture d’écran montre la page Ajouter des certificats avec l’option connexion sélectionnée.":::
1. Cliquez sur **Ajouter** pour importer le fichier.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add.png" alt-text="La capture d’écran montre la page Ajouter des certificats avec l’option Ajouter sélectionnée.":::

### <a name="verify-certificate-install"></a>Vérifier l’installation du certificat

Vérifiez que le client et le certificat racine sont installés. Le certificat client est utilisé pour l’authentification et est obligatoire. Pour plus d’informations sur l’installation d’un certificat client, consultez [Installer un certificat client](point-to-site-how-to-vpn-client-install-azure-cert.md).

1. Ouvrez l’application **Keychain Access**.
1. Accédez à l’onglet **Certificats**.
1. Vérifiez que le client et le certificat racine sont installés.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/keychain.png" alt-text="La capture d’écran montre Keychain Access avec les certificats installés." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/keychain.png":::
  
### <a name="create-vpn-client-profile"></a>Créer le profil du client VPN

1. Accédez à **Préférences système -> Réseau**. Dans la page Réseau, sélectionnez **’+’** pour créer un profil de connexion de client VPN pour une connexion de point à site sur le réseau virtuel Azure.
1. Pour **Interface**, dans la liste déroulante, sélectionnez **VPN**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/select-vpn.png" alt-text="La capture d’écran affiche la fenêtre Réseau avec l’option permettant de sélectionner une interface, VPN est sélectionné." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/select-vpn.png":::

1. Pour **Type VPN**, dans la liste déroulante, sélectionnez **IKEv2**. Dans le champ **Nom du service**, spécifiez un nom convivial pour le profil.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-type.png" alt-text="Capture d’écran montrant la fenêtre Réseau avec l’option permettant de sélectionner une interface, de sélectionner le type de VPN et d’entrer un nom de service." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/vpn-type.png":::

1. Sélectionnez **Créer** pour créer le profil de connexion client VPN.
1. Dans le dossier **Générique**, ouvrez le fichier **VpnSettings.xml** à l’aide d’un éditeur de texte, et copiez la valeur de la balise **VpnServer**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/server-tag.png" alt-text="Capture d’écran affichant le fichier VpnSettings.xml ouvert avec la balise VpnServer mise en surbrillance." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/server-tag.png":::

1. Collez la valeur de balise **VpnServer** dans les champs **Adresse du serveur** et **ID distant** du profil.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/paste-value.png" alt-text="Capture d’écran affiche la fenêtre Réseau avec la valeur collée." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/paste-value.png":::

1. Configurez les paramètres d’authentification. Il existe deux ensembles d’instructions : Choisissez les instructions qui correspondent à la version de votre système d’exploitation.

   **Catalina :** 

     * Pour **Paramètres d’authentification**, sélectionnez **Aucun**. 
     * Sélectionnez **Certificat**, cliquez sur **Sélectionner** et sélectionnez le certificat client correct que vous avez installé précédemment. Cliquez ensuite sur **OK**.
   
        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png" alt-text="Capture d’écran montrant la fenêtre Réseau avec la valeur Aucun sélectionnée pour Paramètres d’authentification et Certificat sélectionné.":::

   **Big Sur :**

      * Cliquez sur **Paramètres d’authentification**, puis sélectionnez **Certificat**. 

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/authentication-certificate.png" alt-text="Capture d’écran montrant des paramètres d’authentification avec le certificat sélectionné." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/authentication-certificate.png":::

      * Cliquez sur **Sélectionner** pour ouvrir la page **Choisir une identité**. La page **Choisir une identité** affiche une liste de certificats à choisir. Si vous n’êtes pas sûr du certificat à utiliser, vous pouvez cliquer sur **Afficher le certificat** pour afficher plus d’informations sur chaque certificat.

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/show-certificate.png" alt-text="Capture d’écran montrant les propriétés du certificat." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/show-certificate.png":::
      * Sélectionnez le certificat approprié, puis **Continuer**.

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-identity.png" alt-text="Capture d’écran montrant Choisir identité, où vous pouvez sélectionner un certificat." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/choose-identity.png":::
   
      * Sur la page **Paramètres d’authentification**, vérifiez que le certificat approprié est affiché, puis cliquez sur **OK**.

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png" alt-text="Capture d’écran montrant la boîte de dialogue Choisir une identité dans laquelle vous pouvez sélectionner le certificat approprié." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/certificate.png":::

1. Pour Catalina et Big Sur, dans le champ **ID local**, spécifiez le nom du certificat. Dans cet exemple, il s’agit de `P2SChildCert`.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/local-id.png" alt-text="Capture d’écran affichant la valeur de l’ID local." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/local-id.png":::
1. Sélectionnez **Appliquer** pour enregistrer toutes les modifications. 
1. Sélectionnez **Connecter** pour lancer la connexion point à site au réseau virtuel Azure.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/select-connect.png" alt-text="Capture d’écran montrant le bouton de connexion." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/select-connect.png":::

1. Une fois la connexion établie, l’état indique **Connecté** et vous pouvez afficher l’adresse IP qui a été extraite du pool d’adresses des clients VPN.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/connected.png" alt-text="La capture d’écran montre la connexion." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/connected.png":::

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (interface graphique utilisateur strongSwan)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>Installer strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>Générer des certificats

Si vous n’avez pas encore généré de certificats, procédez comme suit :

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>Installer et configurer

Les instructions suivantes ont été créées sur Ubuntu 18.0.4. Ubuntu 16.0.10 ne prend pas en charge l’interface graphique utilisateur strongSwan. Si vous souhaitez utiliser Ubuntu 16.0.10, vous devez utiliser la [ligne de commande](#linuxinstallcli). Les exemples ci-dessous ne correspondent pas aux écrans affichés, selon votre version de Linux et strongSwan.

1. Ouvrez le **Terminal** pour installer **strongSwan** et son gestionnaire de réseau en exécutant la commande dans l’exemple.

   ```
   sudo apt install network-manager-strongswan
   ```
1. Sélectionnez **Paramètres**, puis **Réseau**. Sélectionnez le bouton **+** pour créer une connexion.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/edit-connections.png" alt-text="Capture d’écran montrant la page des connexions réseau." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/edit-connections.png":::

1. Sélectionnez **IPsec/IKEv2 (strongSwan)** dans le menu, puis double-cliquez dessus.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-connection.png" alt-text="Capture d’écran montrant la page Ajouter un VPN." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/add-connection.png":::

1. Dans la page **Ajouter un VPN**, ajoutez un nom pour votre connexion VPN.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-type.png" alt-text="Capture d’écran montrant Choisir un type de connexion." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/choose-type.png":::
1. Ouvrez le fichier **VpnSettings.xml** à partir du dossier **Générique** contenu dans les fichiers de configuration du client téléchargé. Recherchez la balise appelée **VpnServer** et copiez le nom, en commençant par « azuregateway » et en terminant par « .cloudapp.net ».

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-server.png" alt-text="Capture d’écran montre la copie de données." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/vpn-server.png":::
1. Collez le nom dans le champ **Adresse** de votre nouvelle connexion VPN sous la section **Passerelle**. Ensuite, sélectionnez l’icône du dossier à la fin du champ **Certificat**, accédez au dossier **Générique**, puis sélectionnez le fichier **VpnServerRoot**.
1. Dans la section **Client** de la connexion, pour **Authentification**, sélectionnez **Certificat/clé privée**. Pour **Certificat** et **Clé privée**, choisissez le certificat et la clé privée qui ont été créés précédemment. Dans **Options**, sélectionnez **Demander une adresse IP interne**. Sélectionnez ensuite **Ajouter**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/ip-request.png" alt-text="Capture d’écran montrant Demander une adresse IP interne." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/ip-request.png":::

1. **Activez** la connexion.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/turn-on.png" alt-text="Capture d’écran montrant la copie." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/turn-on.png":::

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (interface de ligne de commande strongSwan)

### <a name="install-strongswan"></a>Installer strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Générer des certificats

Si vous n’avez pas encore généré de certificats, procédez comme suit :

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Installation et configuration

1. Téléchargez le package client VPN à partir du portail Azure.
1. Extrayez le fichier.
1. À partir du dossier **Générique**, copiez ou déplacez le fichier **VpnServerRoot.cer** dans **/etc/ipsec.d/cacerts**.
1. Copiez ou déplacez **cp client.p12** dans **/etc/ipsec.d/private/** . Ce fichier est un certificat client pour la passerelle VPN.
1. Ouvrez le fichier **VpnSettings.xml** et copiez la valeur `<VpnServer>`. Vous utiliserez cette valeur à l'étape suivante.
1. Ajustez les valeurs de l’exemple ci-dessous, puis ajoutez l’exemple à la configuration **/etc/ipsec.conf**.
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
1. Ajoutez les valeurs suivantes à **/etc/ipsec.secrets**.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

1. Exécutez les commandes suivantes :

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Étapes suivantes

Revenez à l’article d’origine à partir duquel vous travailliez, puis terminez votre configuration P2S.

* [Étapes de configuration pour PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).
* [Étapes de configuration pour le portail Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
