---
title: 'Créer et installer des fichiers config de client VPN P2S : authentification par certificat'
titleSuffix: Azure VPN Gateway
description: Créez et installez des fichiers de configuration du client VPN Windows, Linux, Linux (strongSwan) et Mac OS X pour l’authentification de certificat P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/15/2020
ms.author: cherylmc
ms.openlocfilehash: 9342a70e539c4a2717fe45426c26595285172681
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045785"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Créer et installer des fichiers de configuration du client VPN avec des configurations d’authentification par certificat de connexions P2S Azure natives

Les fichiers de configuration du client VPN se trouvent dans un fichier zip. Les fichiers de configuration fournissent les paramètres nécessaires à un client natif Windows, Mac IKEv2 VPN ou à des clients Linux pour une connexion point à site à un réseau virtuel qui utilise une authentification par certificat Azure native.

Les fichiers de configuration du client sont spécifiques à la configuration VPN du réseau virtuel. Si vous apportez des modifications à la configuration du VPN en point à site après avoir généré les fichiers de configuration du client VPN, comme le type de protocole ou d’authentification du VPN, veillez à générer de nouveaux fichiers de configuration du client VPN pour vos appareils utilisateurs. 

* Pour plus d’informations sur les connexions point à site, consultez [À propos des VPN point à site](point-to-site-about.md).
* Pour obtenir des instructions sur OpenVPN, consultez [Configurer OpenVPN pour P2S](vpn-gateway-howto-openvpn.md) et [Configurer des clients OpenVPN](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate"></a>Générer les fichiers de configuration du client VPN

Avant de commencer, veillez à ce que tous les utilisateurs qui se connectent disposent d’un certificat valide installer sur l’appareil utilisateur. Pour plus d’informations sur l’installation d’un certificat client, consultez [Installer un certificat client](point-to-site-how-to-vpn-client-install-azure-cert.md).

Vous pouvez générer des fichiers de configuration client à l’aide de PowerShell, ou en utilisant le portail Azure. L’une ou l’autre des méthodes retourne le même fichier zip. Décompressez le fichier pour afficher les dossiers suivants :

  * Les dossiers **WindowsAmd64** et **WindowsX86**, dans lesquels se trouvent respectivement les packages de programme d’installation pour Windows 32 bits et Windows 64 bits. Le package d’installation **WindowsAmd64** est pour tous les clients Windows 64 bits pris en charge, pas seulement Amd.
  * Le dossier **Générique**, dans lequel se trouvent les informations générales utilisées pour créer la configuration de votre client VPN. Le dossier Générique est fourni si le protocole IKEv2 ou SSTP+IKEv2 a été configuré sur la passerelle. S’il n’y a que le protocole SSTP qui a été configuré, alors le dossier Générique n’apparaîtra pas.

### <a name="zipportal"></a>Générer les fichiers à l’aide du portail Azure

1. Dans le portail Azure, accédez à la passerelle du réseau virtuel auquel vous souhaitez vous connecter.
2. Dans la page de passerelle de réseau virtuel, cliquez sur **Configuration de point à site**.
3. En haut de la page Configuration de point à site, cliquez sur **Télécharger le client VPN**. La génération du package de configuration du client prend quelques minutes.
4. Votre navigateur indique qu’un fichier zip de configuration du client est disponible. Il porte le même nom que votre passerelle. Décompressez le fichier pour afficher les dossiers.

### <a name="zipps"></a>Générer des fichiers à l’aide de PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Lorsque vous générez les fichiers de configuration du client VPN, la valeur de la méthode « -AuthenticationMethod » est « EapTls ». Générez les fichiers de configuration du client VPN à l’aide de la commande suivante :

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Copiez l’URL dans votre navigateur pour télécharger le fichier zip, puis décompressez le fichier pour afficher les dossiers.

## <a name="installwin"></a>Windows

Vous pouvez utiliser le même package de configuration du client VPN sur chaque ordinateur client Windows, tant que la version correspond à l’architecture du client. Pour obtenir la liste des systèmes d’exploitation clients pris en charge, consultez la section Point à site de la [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Vous devez disposer de droits d’administrateur sur l’ordinateur client Windows à partir duquel vous vous connectez.
>
>

Suivez les étapes suivantes pour configurer le client VPN Windows natif pour une authentification par certificat :

1. Sélectionnez les fichiers de configuration du client VPN qui correspondent à l’architecture de l’ordinateur Windows. Pour une architecture de processeur 64 bits, choisissez le package du programme d’installation « VpnClientSetupAmd64 ». Pour une architecture de processeur 32 bits, choisissez le package du programme d’installation « VpnClientSetupX86 ». 
2. Double-cliquez sur le package pour lancer l’installation. Si une fenêtre contextuelle SmartScreen s’affiche, cliquez sur **Plus d’infos**, puis sur **Exécuter quand même**.
3. Sur l’ordinateur client, accédez à **Paramètres réseau**, puis cliquez sur **VPN**. La connexion VPN indique le nom du réseau virtuel auquel elle se connecte. 
4. Avant de tenter de vous connecter, vérifiez que vous avez installé un certificat client sur l’ordinateur client. Un certificat client est requis pour l’authentification lors de l’utilisation du type d’authentification de certificat Azure natif. Pour plus d’informations sur la génération de certificats, consultez [Générer des certificats](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Pour plus d’informations sur l’installation d’un certificat client, consultez [Installer un certificat client](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>Mac (OS X)

 Vous devez configurer manuellement le client VPN IKEv2 natif sur chaque Mac qui se connectera à Azure. Azure ne fournit pas de fichier mobileconfig pour l’authentification par certificat Azure native. Le dossier **Générique** contient toutes les informations dont vous avez besoin pour la configuration. Si vous ne voyez pas le dossier Générique dans votre téléchargement, il est probable qu’IKEv2 n’était pas sélectionné comme type de tunnel. Notez que la référence SKU de base de passerelle VPN ne prend pas en charge IKEv2. Une fois IKEv2 sélectionné, générez à nouveau le fichier zip pour récupérer le dossier Générique.<br>Ce dossier contient les fichiers suivants :

* Le fichier **VpnSettings.xml**, qui contient d’importants paramètres tels que l’adresse et le type de tunnel du serveur. 
* Le fichier **VpnServerRoot.cer**, qui contient le certificat racine requis pour valider la passerelle VPN Azure lors de la configuration de la connexion P2S.

Suivez les étapes ci-dessous afin de configurer le client VPN Mac natif pour une authentification par certificat. Vous devez effectuer ces étapes sur chaque Mac qui se connectera à Azure :

1. Importez le certificat racine **VpnServerRoot** sur votre Mac. Vous pouvez les importer en copiant les fichiers sur votre Mac, puis en double-cliquant dessus. Cliquez sur **Ajouter** pour importer.

   ![ajouter le certificat](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Il se peut qu’un double-clic sur le certificat n’affiche pas la boîte de dialogue **Ajouter**, mais le certificat est installé dans le magasin approprié. Vous pouvez vérifier le certificat dans le trousseau de connexion sous la catégorie des certificats.
    >
  
2. Vérifiez que vous avez installé un certificat client émis par le certificat racine que vous avez téléchargé vers Azure lors de la configuration de vos paramètres P2S. Cela est différent du VPNServerRoot que vous avez installé à l’étape précédente. Le certificat client est utilisé pour l’authentification et est obligatoire. Pour plus d’informations sur la génération de certificats, consultez [Générer des certificats](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Pour plus d’informations sur l’installation d’un certificat client, consultez [Installer un certificat client](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Ouvrez la boîte de dialogue **Réseau** dans **Préférences réseau**, puis cliquez sur l’icône **+** pour créer un profil de connexion du client VPN pour une connexion P2S au réseau virtuel Azure.

   La valeur **Interface** est « VPN » et la valeur **VPN Type** est « IKEv2 ». Spécifiez un nom pour le profil dans le champ **Nom du service**, puis cliquez sur **Créer** pour créer le profil de connexion du client VPN.

   ![réseau](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. Dans le dossier **Générique**, depuis le fichier **VpnSettings.xml**, copiez la valeur de la balise **VpnServer**. Collez cette valeur dans les champs **Adresse du serveur** et **ID distant** du profil.

   ![informations du serveur](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Cliquez sur **Paramètres d’authentification** et sélectionnez **Certificat**. Pour **Catalina**, cliquez sur **Aucun**, puis sur **Certificat**

   ![paramètres d’authentification](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)

   * Pour Catalina, sélectionnez **Aucun**, puis **Certificat**. **Sélectionnez** le certificat approprié :
   
   ![catalina](./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png)

6. Cliquez sur **Sélectionner** pour choisir le certificat client que vous souhaitez utiliser pour l’authentification. Il s’agit du certificat que vous avez installé à l’étape 2.

   ![certificat](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Choisir une identité** affiche une liste de certificats à choisir. Sélectionnez le certificat approprié, puis cliquez sur **Continuer**.

   ![identité](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. Dans le champ **ID local**, spécifiez le nom du certificat (renseigné à l’étape 6). Dans cet exemple, nous lui avons donné le nom « ikev2Client.com ». Cliquez ensuite sur le bouton **Appliquer** pour enregistrer les modifications.

   ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. Dans la boîte de dialogue **Réseau**, cliquez sur **Appliquer** pour enregistrer toutes les modifications. Cliquez ensuite sur **Connexion** pour lancer la connexion P2S au réseau virtuel Azure.

## <a name="linuxgui"></a>Linux (interface graphique utilisateur strongSwan)

### <a name="installstrongswan"></a>Installer strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="genlinuxcerts"></a>Générer des certificats

Si vous n’avez pas encore généré de certificats, procédez comme suit :

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install"></a>Installer et configurer

Les instructions suivantes ont été créées sur Ubuntu 18.0.4. Ubuntu 16.0.10 ne prend pas en charge l’interface graphique utilisateur strongSwan. Si vous souhaitez utiliser Ubuntu 16.0.10, vous devez utiliser la [ligne de commande](#linuxinstallcli). Les exemples ci-dessous ne correspondent pas aux écrans affichés, selon votre version de Linux et strongSwan.

1. Ouvrez le **Terminal** pour installer **strongSwan** et son gestionnaire de réseau en exécutant la commande dans l’exemple.

   ```
   sudo apt install network-manager-strongswan
   ```
2. Sélectionnez **Paramètres**, puis **Réseau**.

   ![modifier les connexions](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Cliquez sur le bouton **+** pour créer une connexion.

   ![ajouter une connexion](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Sélectionnez **IPsec/IKEv2 (strongSwan)** dans le menu, puis double-cliquez dessus. Vous pouvez nommer votre connexion à cette étape.

   ![choisir un type de connexion](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Ouvrez le fichier **VpnSettings.xml** à partir du dossier **Générique** contenu dans les fichiers de configuration du client téléchargé. Recherchez la balise appelée **VpnServer** et copiez le nom, en commençant par « azuregateway » et en terminant par « .cloudapp.net ».

   ![copier le nom](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Collez ce nom dans le champ **Adresse** de votre nouvelle connexion VPN sous la section **Passerelle**. Ensuite, sélectionnez l’icône du dossier à la fin du champ **Certificat**, accédez au dossier **Générique**, puis sélectionnez le fichier **VpnServerRoot**.
7. Dans la section **Client** de la connexion, pour **Authentification**, sélectionnez **Certificat/clé privée**. Pour **Certificat** et **Clé privée**, choisissez le certificat et la clé privée qui ont été créés précédemment. Dans **Options**, sélectionnez **Demander une adresse IP interne**. Cliquez ensuite sur **Ajouter**.

   ![demander une adresse IP interne](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. **Activez** la connexion.

## <a name="linuxinstallcli"></a>Linux (interface de ligne de commande strongSwan)

### <a name="install-strongswan"></a>Installer strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Générer des certificats

Si vous n’avez pas encore généré de certificats, procédez comme suit :

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Installation et configuration

1. Téléchargez le package client VPN à partir du portail Azure.
2. Extrayez le fichier.
3. À partir du dossier **Générique**, copiez ou déplacez le VpnServerRoot.cer dans /etc/ipsec.d/cacerts.
4. Copiez ou déplacez client.p12 cp dans /etc/ipsec.d/private/. Ce fichier est un certificat client pour la passerelle VPN Azure.
5. Ouvrez le fichier VpnSettings.xml et copiez la valeur `<VpnServer>`. Vous utiliserez cette valeur à l'étape suivante.
6. Ajustez les valeurs de l’exemple ci-dessous, puis ajoutez l’exemple à la configuration /etc/ipsec.conf.
  
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
6. Ajoutez le code suivant à */etc/ipsec.secrets*.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Exécutez les commandes suivantes :

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Étapes suivantes

Revenez à l’article pour [terminer la configuration P2S](vpn-gateway-howto-point-to-site-rm-ps.md).

Pour dépanner les connexions P2S, consultez les articles suivants :

  * [Résolution des problèmes de connexion de point à site Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Résoudre les problèmes de connexion VPN à partir de clients Mac OS X](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
