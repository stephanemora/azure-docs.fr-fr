---
title: 'Connexion à un réseau virtuel à l’aide de P2S VPN et l’authentification par certificat : portail'
titleSuffix: Azure VPN Gateway
description: Connectez des clients Windows, Mac OS X et Linux en toute sécurité à un réseau virtuel Azure à l’aide d’une connexion P2S et de certificats auto-signés ou délivrés par une autorité de certification. Cet article utilise le portail Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 03dbc481950ed2a020a26dc3af8668c516b66115
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89435999"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Configurez une connexion point à site à un réseau virtuel à l’aide de l’authentification par certificat Azure native : Portail Azure

Cet article vous permet de connecter en toute sécurité des clients individuels qui exécutent Windows, Linux ou Mac OS X à un réseau virtuel Azure. Les connexions VPN point à site sont utiles lorsque vous souhaitez vous connecter à votre réseau virtuel à partir d’un emplacement distant, par exemple lorsque vous travaillez à distance depuis votre domicile ou en conférence. La connexion P2S est une solution alternative au VPN de site à site lorsque seul un nombre restreint de clients doivent se connecter à un réseau virtuel. Les connexions de point à site ne nécessitent pas de périphérique VPN ou d’adresse IP publique. La connexion P2S crée la connexion VPN via SSTP (Secure Socket Tunneling Protocol) ou IKEv2. Pour plus d’informations sur le VPN de point à site, consultez l’article [À propos du VPN de point à site](point-to-site-about.md).

![Diagramme de connexion d’un ordinateur à un réseau virtuel Azure à l’aide d’une passerelle point à site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)

## <a name="architecture"></a>Architecture

Les connexions d’authentification avec certificat Azure natif de point à site utilisent les éléments suivants, que vous pouvez configurer dans cet exercice :

* Une passerelle VPN RouteBased.
* La clé publique (fichier .cer) d’un certificat racine, chargée sur Azure. Une fois le certificat chargé, il est considéré comme un certificat approuvé et est utilisé pour l’authentification.
* Un certificat client généré à partir du certificat racine. Le certificat client installé sur chaque ordinateur client qui se connecte au réseau virtuel. Ce certificat est utilisé pour l’authentification du client.
* Une configuration du client VPN. Les fichiers de configuration du client VPN contiennent les informations nécessaires permettant au client de se connecter sur le réseau virtuel. Les fichiers configurent le client VPN existant qui est natif au système d’exploitation. Chaque client qui se connecte doit être configuré à l’aide des paramètres dans les fichiers de configuration.

#### <a name="example-values"></a><a name="example"></a>Exemples de valeurs

Vous pouvez utiliser ces valeurs pour créer un environnement de test ou vous y référer pour mieux comprendre les exemples de cet article :

* **Nom du réseau virtuel :** VNet1
* **Espace d’adressage :** 10.1.0.0/16<br>Pour cet exemple, nous n’utilisons qu’un seul espace d’adressage. Vous pouvez avoir plusieurs espaces d’adressage pour votre réseau virtuel.
* **Nom du sous-réseau** : FrontEnd
* **Plage d'adresses du sous-réseau :** 10.1.0.0/24
* **Abonnement :** vérifiez que vous utilisez l’abonnement approprié si vous en possédez plusieurs.
* **Groupe de ressources :** TestRG1
* **Emplacement :** USA Est
* **GatewaySubnet :** 10.1.255.0/27<br>
* **Nom de la passerelle de réseau virtuel :** VNet1GW
* **Type de passerelle :** VPN
* **Type de VPN :** basé sur la route
* **Nom de l'adresse IP publique :** VNet1GWpip
* **Type de connexion :** Point à site
* **Pool d'adresses des clients :** 172.16.201.0/24<br>Les clients VPN qui se connectent au réseau virtuel à l’aide de cette connexion point à site reçoivent une adresse IP de ce pool d’adresses des clients.

## <a name="1-create-a-virtual-network"></a><a name="createvnet"></a>1. Créez un réseau virtuel

Avant de commencer, assurez-vous que vous disposez d’un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial).
[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. Créer une passerelle de réseau virtuel

Dans cette étape, vous créez la passerelle de réseau virtuel de votre réseau virtuel. La création d’une passerelle nécessite généralement au moins 45 minutes, selon la référence SKU de passerelle sélectionnée.

>[!NOTE]
>La référence SKU de passerelle De base ne prend pas en charge IKEv2 ou l’authentification RADIUS. Si vous envisagez de connecter des clients Mac à votre réseau virtuel, n’utilisez pas la référence SKU De base.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="3-generate-certificates"></a><a name="generatecert"></a>3. Générer des certificats

Les certificats sont utilisés par Azure pour authentifier les clients qui se connectent à un réseau virtuel via une connexion VPN point à site. Une fois que vous avez obtenu le certificat racine, vous [chargez](#uploadfile) les informations de la clé publique du certificat racine vers Azure. Le certificat racine est alors considéré comme « approuvé » par Azure pour la connexion via P2S sur le réseau virtuel. Vous générez également des certificats de client à partir du certificat racine approuvé, puis vous les installez sur chaque ordinateur client. Le certificat permet d’authentifier le client lorsqu’il établit une connexion avec le réseau virtuel. 

### <a name="1-obtain-the-cer-file-for-the-root-certificate"></a><a name="getcer"></a>1. Obtenir le fichier .cer pour le certificat racine

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="2-generate-a-client-certificate"></a><a name="generateclientcert"></a>2. Générer un certificat client

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="4-add-the-client-address-pool"></a><a name="addresspool"></a>4. Ajouter le pool d’adresses des clients

Le pool d’adresses des clients est une plage d’adresses IP privées que vous spécifiez. Les clients qui se connectent via un réseau virtuel de point à site reçoivent de façon dynamique une adresse IP de cette plage. Utilisez une plage d’adresses IP privées qui ne chevauche ni l’emplacement local à partir duquel vous vous connectez ni le réseau virtuel auquel vous souhaitez vous connecter. Si vous configurez plusieurs protocoles et que SSTP est l’un d’entre eux, le pool d’adresses configuré est réparti de manière égale entre les protocoles configurés.

1. Une fois la passerelle de réseau virtuel créée, accédez à la section **Paramètres** de la page Passerelle de réseau virtuel. Dans la section **Paramètres**, sélectionnez **Configuration de point à site**. Sélectionnez **Configurer** pour ouvrir la page de configuration.

   ![Point-to-Site page](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-configure.png "Configuration de point à site maintenant")
2. Sur la page **Configuration de point à site**, vous pouvez configurer divers paramètres. Si vous ne voyez pas le type de tunnel ou d’authentification sur cette page, votre passerelle utilise la référence SKU de base. La référence SKU de base ne prend pas en charge IKEv2 ou l’authentification RADIUS. Si vous souhaitez utiliser ces paramètres, vous devez supprimer et recréer la passerelle à l’aide d’une autre référence SKU de passerelle.

   [![Point-to-site configuration page](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/certificate-settings-address.png "spécifier le pool d’adresses")](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/certificate-settings-expanded.png#lightbox)
3. Dans la zone **Pool d’adresses**, ajoutez la plage d’adresses IP privées que vous souhaitez utiliser. Les clients VPN reçoivent dynamiquement une adresse IP à partir de la plage que vous spécifiez. Le masque minimal de sous-réseau est de 29 bits pour la configuration active/passive, et de 28 bits pour la configuration active/active.
4. Passez à la section suivante pour configurer le type de tunnel.

## <a name="5-configure-tunnel-type"></a><a name="tunneltype"></a>5. Configurer le type de tunnel

Vous pouvez sélectionner le type de tunnel. Les types de tunnels disponibles sont SSTP et IKEv2.

* Le client strongSwan sur Android et Linux et le client VPN IKEv2 natif sur iOS et OSX n’utiliseront que le tunnel IKEv2 pour se connecter.
* Les clients Windows essaient IKEv2 en premier lieu. En cas d’échec de la connexion, ils utilisent SSTP.
* Vous pouvez utiliser le client OpenVPN pour la connexion au type de tunnel OpenVPN.

![Tunnel type](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/tunnel.png "spécifier le type de tunnel")

## <a name="6-configure-authentication-type"></a><a name="authenticationtype"></a>6. Configurer le type d’authentification

Pour le **Type d’authentification**, sélectionnez **Certificat Azure**.

  ![Type d’authentification](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/authentication-type.png "spécifier le type d’authentification")

## <a name="7-upload-the-root-certificate-public-certificate-data"></a><a name="uploadfile"></a>7. Charger les données de certificat public du certificat racine

Vous pouvez charger d’autres certificats racines approuvés, jusqu’à 20 au total. Une fois que les données de certificat public sont chargées, Azure peut les utiliser pour authentifier les clients qui ont installé un certificat client généré à partir du certificat racine approuvé. Chargez les informations de la clé publique du certificat racine dans Azure.

1. Les certificats sont ajoutés sur la page **Configuration de point à site**, dans la section **Certificat racine**.
2. Vérifiez que vous avez exporté le certificat racine en tant que fichier Base-64 codé X.509 (.cer). Vous devez exporter le certificat dans ce format pour être en mesure de l’ouvrir avec un éditeur de texte.
3. Ouvrez le certificat avec un éditeur de texte, Bloc-notes par exemple. Lors de la copie des données de certificat, assurez-vous que vous copiez le texte en une seule ligne continue sans retour chariot ou sauts de ligne. Vous devrez peut-être modifier l’affichage dans l’éditeur de texte en activant « Afficher les symboles/Afficher tous les caractères » pour afficher les retours chariot et sauts de ligne. Copiez uniquement la section suivante sur une seule ligne continue :

   ![Certificate data](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png "copier les données du certificat racine")
4. Collez les données du certificat dans le champ **Données du certificat public**. Donnez un **Nom** au certificat, puis sélectionnez **Enregistrer**. Vous pouvez ajouter jusqu’à 20 certificats racine approuvés.

   ![Paste certificate data](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png "coller les données du certificat")
5. Sélectionnez **Enregistrer** en haut de la page pour enregistrer tous les paramètres de configuration.

   ![Save configuration](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png "enregistrer la configuration")

## <a name="8-install-an-exported-client-certificate"></a><a name="installclientcert"></a>8. Installer un certificat client exporté

Si vous souhaitez créer une connexion P2S à partir d’un ordinateur client différent de celui que vous avez utilisé pour générer les certificats clients, vous devez installer un certificat client. Quand vous installez un certificat client, vous avez besoin du mot de passe créé lors de l’exportation du certificat client.

Assurez-vous que le certificat client a été exporté dans un fichier .pfx avec la totalité de la chaîne du certificat (qui est la valeur par défaut). Dans le cas contraire, les informations du certificat racine ne sont pas présentes sur l’ordinateur client et le client ne pourra pas s’authentifier correctement.

Pour la procédure d’installation, consultez [Installer un certificat client](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="9-generate-and-install-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>9. Générer et installer le package de configuration du client VPN

Les fichiers de configuration du client VPN contiennent des paramètres pour configurer les appareils afin qu’ils puissent se connecter à un réseau virtuel via une connexion P2S. Pour obtenir des instructions permettant de générer et d’installer les fichiers de configuration du client VPN, consultez [Créer et installer les fichiers de configuration du client VPN pour les configurations P2S d’authentification par certificat Azure native](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. Connexion à Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Se connecter à partir d’un client VPN Windows

>[!NOTE]
>Vous devez disposer de droits d’administrateur sur l’ordinateur client Windows à partir duquel vous vous connectez.
>
>

1. Pour vous connecter à votre réseau virtuel, sur l’ordinateur client, accédez aux connexions VPN et recherchez celle que vous avez créée. Elle porte le même nom que votre réseau virtuel. Sélectionnez **Connecter**. Un message contextuel faisant référence à l’utilisation du certificat peut s’afficher. Sélectionnez **Continuer** pour utiliser des privilèges élevés.

2. Sur la page d'état **Connexion**, sélectionnez **Connecter** pour établir la connexion. Si un écran **Sélectionner un certificat** apparaît, vérifiez que le certificat client affiché est celui que vous souhaitez utiliser pour la connexion. Dans le cas contraire, utilisez la flèche déroulante pour sélectionner le certificat approprié, puis sélectionnez **OK**.

   ![VPN client connects to Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png "se connecter")
3. Votre connexion est établie.

   ![Connection established](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png "connexion établie")

#### <a name="troubleshoot-windows-p2s-connections"></a>Résolution des problèmes liés aux connexions P2S Windows

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Pour se connecter à partir d’un client VPN Mac

Dans la boîte de dialogue Réseau, recherchez le profil client que vous souhaitez utiliser, spécifiez les paramètres du fichier [VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac), puis sélectionnez **Se connecter**.

Pour obtenir des instructions détaillées, consultez la section [Installer - Mac (OS X)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac). Si vous rencontrez des problèmes de connexion, vérifiez que la passerelle de réseau virtuel n’utilise pas une référence SKU de base. La référence SKU de base n’est pas prise en charge pour les clients Mac.

  ![Mac connection](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png "Se connecter")

## <a name="to-verify-your-connection"></a><a name="verify"></a>Pour vérifier votre connexion

Ces instructions s’appliquent aux clients Windows.

1. Pour vérifier que votre connexion VPN est active, ouvrez une invite de commandes avec élévation de privilèges, puis exécutez *ipconfig/all*.
2. Affichez les résultats. Notez que l’adresse IP que vous avez reçue est l’une des adresses du pool d’adresses de client VPN point à site que vous avez spécifiées dans votre configuration. Les résultats ressemblent à l’exemple qui suit :

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Se connecter à une machine virtuelle

Ces instructions s’appliquent aux clients Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Pour ajouter ou supprimer des certificats racine approuvés

Vous pouvez ajouter et supprimer des certificats racines approuvés à partir d'Azure. Lorsque vous supprimez un certificat racine, les clients qui possèdent un certificat généré à partir de cette racine ne seront plus en mesure de s’authentifier, et donc de se connecter. Si vous souhaitez que des clients s’authentifient et se connectent, vous devez installer un nouveau certificat client généré à partir d’un certificat racine approuvé (téléchargé) dans Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Ajout d’un certificat racine approuvé

Vous pouvez ajouter jusqu’à 20 fichiers .cer de certificat racine approuvés dans Azure. Pour obtenir des instructions, consultez la section [Télécharger un certificat racine approuvé](#uploadfile) dans cet article.

### <a name="to-remove-a-trusted-root-certificate"></a>Suppression d’un certificat racine approuvé

1. Pour supprimer un certificat racine approuvé, accédez à la page **Configuration Point à site** de votre passerelle de réseau virtuel.
2. Dans la section **Certificat racine** de la page, recherchez le certificat que vous souhaitez supprimer.
3. Sélectionnez les points de suspension à côté du certificat, puis sélectionnez « Supprimer ».

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>Révocation d’un certificat client

Vous pouvez révoquer des certificats clients. La liste de révocation de certificat vous permet de refuser sélectivement la connexion point à site en fonction des certificats clients individuels. Cela est différent de la suppression d’un certificat racine approuvé. Si vous supprimez un fichier .cer de certificat racine approuvé d’Azure, vous révoquez l’accès pour tous les certificats clients générés/signés par le certificat racine révoqué. Le fait de révoquer un certificat client plutôt que le certificat racine permet de continuer à utiliser les autres certificats générés à partir du certificat racine pour l’authentification.

La pratique courante consiste à utiliser le certificat racine pour gérer l'accès au niveaux de l'équipe ou de l'organisation, tout en utilisant des certificats clients révoqués pour le contrôle d'accès précis des utilisateurs individuels.

### <a name="revoke-a-client-certificate"></a>Révocation d'un certificat client

Vous pouvez révoquer un certificat client en ajoutant son empreinte à la liste de révocation.

1. Récupérez l’empreinte du certificat client. Pour plus d’informations, consultez l’article [Comment : récupérer l’empreinte numérique d’un certificat](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copiez les informations dans un éditeur de texte et supprimez tous les espaces afin d’obtenir une chaîne continue.
3. Accédez à la page **Configuration de point à site** de la passerelle de réseau virtuel. Il s’agit de la page que vous avez utilisé pour [charger un certificat racine approuvé](#uploadfile).
4. Dans la section **Certificats révoqués**, entrez un nom convivial pour le certificat (il ne s’agit pas forcément du nom commun du certificat).
5. Copiez et collez la chaîne d’empreinte numérique dans le champ **Empreinte**.
6. L’empreinte est validée, puis automatiquement ajoutée à la liste de révocation. Un message apparaît pour indiquer que la liste est en cours de mise à jour. 
7. Une fois la mise à jour terminée, le certificat ne peut plus être utilisé pour se connecter. Les clients qui tentent de se connecter à l’aide de ce certificat reçoivent un message indiquant que le certificat n’est plus valide.

## <a name="point-to-site-faq"></a><a name="faq"></a>Forum Aux Questions sur les connexions point à site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez [Machines virtuelles](https://docs.microsoft.com/azure/). Pour plus d’informations sur la mise en réseau et les machines virtuelles, consultez [Vue d’ensemble du réseau de machines virtuelles Azure et Linux](../virtual-machines/linux/azure-vm-network-overview.md).

Pour plus d’informations sur la résolution des problèmes liés aux connexions de point à site, consultez l’article [Résolution des problèmes de connexion de point à site Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
