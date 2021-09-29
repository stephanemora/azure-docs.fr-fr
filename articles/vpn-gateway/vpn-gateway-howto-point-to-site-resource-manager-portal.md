---
title: 'Connexion à un réseau virtuel à l’aide de P2S VPN et l’authentification par certificat : portail'
titleSuffix: Azure VPN Gateway
description: Apprenez à connecter des clients Windows, macOS et Linux à un réseau virtuel en toute sécurité à l'aide de connexions point à site de passerelle VPN et de certificats auto-signés ou délivrés par une autorité de certification.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 08/19/2021
ms.author: cherylmc
ms.openlocfilehash: 70bb2f54d6e56574a5cbbb98d1c0c467f4715f3d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818153"
---
# <a name="configure-a-point-to-site-vpn-connection-using-azure-certificate-authentication-azure-portal"></a>Configurer une connexion VPN point à site à l’aide de l’authentification par certificat Azure : Portail Azure

Cet article vous permet de connecter de façon sécurisée des clients individuels qui exécutent Windows, Linux ou macOS à un réseau virtuel Azure. Les connexions VPN point à site sont utiles lorsque vous souhaitez vous connecter à votre réseau virtuel à partir d’un emplacement distant, par exemple lorsque vous travaillez à distance depuis votre domicile ou en conférence. La connexion P2S est une solution alternative au VPN de site à site lorsque seul un nombre restreint de clients doivent se connecter à un réseau virtuel. Les connexions de point à site ne nécessitent pas de périphérique VPN ou d’adresse IP publique. La connexion P2S crée la connexion VPN via SSTP (Secure Socket Tunneling Protocol) ou IKEv2. Pour plus d’informations sur le VPN de point à site, consultez l’article [À propos du VPN de point à site](point-to-site-about.md).

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Diagramme de connexion point à site d’un ordinateur à un réseau virtuel Azure.":::

Pour plus d’informations sur le VPN point à site, consultez [À propos du VPN point à site](point-to-site-about.md). Pour créer cette configuration à l’aide d’Azure PowerShell, consultez [Configurer un VPN point à site à l’aide d’Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Prérequis

Assurez-vous de disposer d’un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial).

### <a name="example-values"></a><a name="example"></a>Exemples de valeurs

Vous pouvez utiliser ces valeurs pour créer un environnement de test ou vous y référer pour mieux comprendre les exemples de cet article :

**Réseau virtuel**

* **Nom du réseau virtuel :** VNet1
* **Espace d’adressage :** 10.1.0.0/16<br>Pour cet exemple, nous n’utilisons qu’un seul espace d’adressage. Vous pouvez avoir plusieurs espaces d’adressage pour votre réseau virtuel.
* **Nom du sous-réseau** : FrontEnd
* **Plage d'adresses du sous-réseau :** 10.1.0.0/24
* **Abonnement :** vérifiez que vous utilisez l’abonnement approprié si vous en possédez plusieurs.
* **Groupe de ressources :** TestRG1
* **Emplacement :** USA Est

**Passerelle de réseau virtuel**

* **Nom de la passerelle de réseau virtuel :** VNet1GW
* **Type de passerelle :** VPN
* **Type de VPN :** basé sur la route
* **Référence SKU :** VpnGw2
* **Génération :** Génération 2
* **Plage d’adresses du sous-réseau de passerelle :** 10.1.255.0/27
* **Nom de l'adresse IP publique :** VNet1GWpip

**Type de connexion et pool d’adresses des clients**

* **Type de connexion :** Point à site
* **Pool d'adresses des clients :** 172.16.201.0/24<br>Les clients VPN qui se connectent au réseau virtuel à l’aide de cette connexion point à site reçoivent une adresse IP de ce pool d’adresses des clients.

## <a name="create-a-vnet"></a><a name="createvnet"></a>Créer un réseau virtuel

Dans cette section, vous créez un réseau virtuel.

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vpn-gateway"></a><a name="creategw"></a>Créer la passerelle VPN

Dans cette étape, vous créez la passerelle de réseau virtuel de votre réseau virtuel. La création d’une passerelle nécessite généralement au moins 45 minutes, selon la référence SKU de passerelle sélectionnée.

>[!NOTE]
>La référence SKU de passerelle De base ne prend pas en charge IKEv2 ou l’authentification RADIUS. Si vous envisagez de connecter des clients Mac à votre réseau virtuel, n’utilisez pas la référence SKU De base.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-portal-include.md)]
[!INCLUDE [Configure PIP settings](../../includes/vpn-gateway-add-gw-pip-portal-include.md)]

Vous pouvez voir l’état du déploiement dans la page Vue d’ensemble pour votre passerelle. Une fois la passerelle créée, examinez le réseau virtuel dans le portail pour obtenir l’adresse IP affectée à la passerelle. Cette dernière apparaît sous la forme d’un appareil connecté.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="generate-certificates"></a><a name="generatecert"></a>Générer des certificats

Les certificats sont utilisés par Azure pour authentifier les clients qui se connectent à un réseau virtuel via une connexion VPN point à site. Une fois que vous avez obtenu le certificat racine, vous [chargez](#uploadfile) les informations de la clé publique du certificat racine vers Azure. Le certificat racine est alors considéré comme « approuvé » par Azure pour la connexion via P2S sur le réseau virtuel. Vous générez également des certificats de client à partir du certificat racine approuvé, puis vous les installez sur chaque ordinateur client. Le certificat permet d’authentifier le client lorsqu’il établit une connexion avec le réseau virtuel.

### <a name="generate-a-root-certificate"></a><a name="getcer"></a>Générer un certificat racine

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-client-certificates"></a><a name="generateclientcert"></a>Générer des certificats clients

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="add-the-vpn-client-address-pool"></a><a name="addresspool"></a>Ajouter le pool d’adresses du client VPN

Le pool d’adresses des clients est une plage d’adresses IP privées que vous spécifiez. Les clients qui se connectent via un réseau virtuel de point à site reçoivent de façon dynamique une adresse IP de cette plage. Utilisez une plage d’adresses IP privées qui ne chevauche ni l’emplacement local à partir duquel vous vous connectez ni le réseau virtuel auquel vous souhaitez vous connecter. Si vous configurez plusieurs protocoles et que SSTP est l’un d’entre eux, le pool d’adresses configuré est réparti de manière égale entre les protocoles configurés.

1. Une fois la passerelle de réseau virtuel créée, accédez à la section **Paramètres** de la page Passerelle de réseau virtuel. Dans **Paramètres**, sélectionnez **Configuration de point à site**. Sélectionnez **Configurer** pour ouvrir la page de configuration.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Page de la configuration point à site" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. Dans la page **Configuration point à site**, dans la zone **Pool d’adresses**, ajoutez la plage d’adresses IP privées que vous souhaitez utiliser. Les clients VPN reçoivent dynamiquement une adresse IP à partir de la plage que vous spécifiez. Le masque minimal de sous-réseau est de 29 bits pour la configuration active/passive, et de 28 bits pour la configuration active/active.
1. Passez à la section suivante pour configurer les différents types d’authentification et de tunnels.

## <a name="specify-tunnel-type-and-authentication-type"></a><a name="type"></a>Spécifier le type de tunnel et le type d’authentification

Dans cette section, vous précisez le type de tunnel et le type d’authentification. Si vous ne voyez pas le type de tunnel ou le type d’authentification sur la page de la configuration point à site, votre passerelle utilise la référence SKU de base. La référence SKU de base ne prend pas en charge IKEv2 ou l’authentification RADIUS. Si vous souhaitez utiliser ces paramètres, vous devez supprimer et recréer la passerelle à l’aide d’une autre référence SKU de passerelle.

### <a name="tunnel-type"></a><a name="tunneltype"></a>Type de tunnel

Dans la page **Configuration point à site**, sélectionnez le **Type de tunnel**. Lorsque vous sélectionnez le type de tunnel, notez les points suivants :

* Le client strongSwan, sur Android et Linux, et le client VPN IKEv2 natif, sur iOS et macOS, n’utiliseront que le type de tunnel IKEv2 pour se connecter.
* Les clients Windows essaieront IKEv2 en premier lieu. En cas d’échec de la connexion, ils utiliseront SSTP.
* Vous pouvez utiliser le client OpenVPN pour la connexion au type de tunnel OpenVPN.

### <a name="authentication-type"></a><a name="authenticationtype"></a>Type d'authentification

Pour le **Type d’authentification**, sélectionnez **Certificat Azure**.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/authentication.png" alt-text="Capture d’écran du type d’authentification avec le certificat Azure sélectionné." :::

## <a name="upload-root-certificate-public-key-information"></a><a name="uploadfile"></a>Charger les informations de la clé publique du certificat racine

Dans cette section, vous allez charger dans Azure les données de certificat racine publiques. Une fois que les données de certificat public sont chargées, Azure peut les utiliser pour authentifier les clients qui ont installé un certificat client généré à partir du certificat racine approuvé.

1. Accédez à votre page **Passerelle de réseau virtuel -> Configuration point à site** dans la section **Certificat racine**. Cette section est visible uniquement si vous avez sélectionné **Certificat Azure** comme type d’authentification.
1. Vérifiez que vous avez exporté le certificat racine sous la forme d’un fichier **X.509 codé en base 64 (.cer)** dans les étapes précédentes. Vous devez exporter le certificat dans ce format pour être en mesure de l’ouvrir avec un éditeur de texte. Vous n’avez pas besoin d’exporter la clé privée.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/base-64.png" alt-text="Capture d’écran montrant l’exportation au format X.509 codé en base 64." :::
1. Ouvrez le certificat avec un éditeur de texte, Bloc-notes par exemple. Lors de la copie des données de certificat, assurez-vous que vous copiez le texte en une seule ligne continue sans retour chariot ou sauts de ligne. Vous devrez peut-être modifier l’affichage dans l’éditeur de texte en activant « Afficher les symboles/Afficher tous les caractères » pour afficher les retours chariot et sauts de ligne. Copiez uniquement la section suivante sur une seule ligne continue :

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png" alt-text="Capture d’écran montrant des informations de certificat racine dans le Bloc-notes." border="false":::
1. Dans la section **Certificat racine**, vous pouvez ajouter jusqu’à 20 certificats racine approuvés.

   * Collez les données du certificat dans le champ **Données de certificat public**. 
   * Donnez un **Nom** au certificat. 

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/root-certificate.png" alt-text="Capture d’écran du champ des données de certificat." :::
1. Sélectionnez **Enregistrer** en haut de la page pour enregistrer tous les paramètres de configuration.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save-configuration.png" alt-text="Capture d’écran de la configuration de P2S avec l’option Enregistrer sélectionnée." :::

## <a name="install-exported-client-certificate"></a><a name="installclientcert"></a>Installer un certificat client exporté

Si vous souhaitez créer une connexion P2S à partir d’un ordinateur client différent de celui que vous avez utilisé pour générer les certificats clients, vous devez installer un certificat client. Quand vous installez un certificat client, vous avez besoin du mot de passe créé lors de l’exportation du certificat client.

Assurez-vous que le certificat client a été exporté dans un fichier .pfx avec la totalité de la chaîne du certificat (qui est la valeur par défaut). Dans le cas contraire, les informations du certificat racine ne sont pas présentes sur l’ordinateur client et le client ne pourra pas s’authentifier correctement.

Pour la procédure d’installation, consultez [Installer un certificat client](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="configure-settings-for-vpn-clients"></a><a name="clientconfig"></a>Configurer les paramètres des clients VPN

Pour vous connecter à la passerelle de réseau virtuel à l’aide de P2S, chaque ordinateur utilise le client VPN qui est installé en mode natif dans le cadre du système d’exploitation. Par exemple, quand vous accédez aux paramètres VPN sur votre ordinateur Windows, vous pouvez ajouter des connexions VPN sans installer de client VPN distinct. Vous configurez chaque client VPN à l’aide d’un package de configuration client. Le package de configuration du client contient des paramètres spécifiques à la passerelle VPN que vous avez créée. 

Pour connaître les étapes permettant de générer et d’installer les fichiers de configuration du client VPN, consultez [Créer et installer les fichiers de configuration du client VPN pour les configurations P2S d’authentification par certificat Azure](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="connect-to-azure"></a><a name="connect"></a>Connexion à Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Se connecter à partir d’un client VPN Windows

[!INCLUDE [Connect from a Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Pour se connecter à partir d’un client VPN Mac

Dans la boîte de dialogue Réseau, recherchez le profil client que vous souhaitez utiliser, spécifiez les paramètres du fichier [VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac), puis sélectionnez **Se connecter**. Pour obtenir des instructions détaillées, consultez [Générer et installer des fichiers de configuration du client VPN - macOS](./point-to-site-vpn-client-configuration-azure-cert.md#installmac). 

Si vous rencontrez des problèmes de connexion, vérifiez que la passerelle de réseau virtuel n’utilise pas une référence SKU de base. La référence SKU de base n’est pas prise en charge pour les clients Mac.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/select-connect.png" alt-text="Capture d’écran montrant le bouton de connexion." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/select-connect.png":::

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

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Vérifiez que le package de configuration du client VPN a été généré après que les adresses IP du serveur DNS ont été spécifiées pour le réseau virtuel. Si vous avez mis à jour les adresses IP du serveur DNS, générez et installez un package de configuration du client VPN.

* Utilisez « ipconfig » pour vérifier l’adresse IPv4 attribuée à l’adaptateur Ethernet sur l’ordinateur à partir duquel vous vous connectez. Si l’adresse IP est comprise dans la plage d’adresses du réseau virtuel auquel vous vous connectez, ou dans la plage d’adresses de votre VPNClientAddressPool, cette situation est désignée sous le terme d’espaces d’adressage qui se chevauchent. Lorsque vos espaces d’adressage se chevauchent de cette façon, le trafic réseau n’atteint pas Azure et reste sur le réseau local.

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Pour ajouter ou supprimer des certificats racine approuvés

Vous pouvez ajouter et supprimer des certificats racines approuvés à partir d'Azure. Lorsque vous supprimez un certificat racine, les clients qui possèdent un certificat généré à partir de cette racine ne seront plus en mesure de s’authentifier, et donc de se connecter. Si vous souhaitez que des clients s’authentifient et se connectent, vous devez installer un nouveau certificat client généré à partir d’un certificat racine approuvé (téléchargé) dans Azure.

Vous pouvez ajouter jusqu’à 20 fichiers .cer de certificat racine approuvés dans Azure. Pour obtenir des instructions, consultez la section [Charger un certificat racine approuvé](#uploadfile).

Pour supprimer un certificat racine approuvé :

1. Accédez à la page **Configuration de point à site** pour votre passerelle de réseau virtuel.
1. Dans la section **Certificat racine** de la page, recherchez le certificat que vous souhaitez supprimer.
1. Sélectionnez les points de suspension à côté du certificat, puis sélectionnez **Supprimer**.

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>Révocation d'un certificat client

Vous pouvez révoquer des certificats clients. La liste de révocation de certificat vous permet de refuser sélectivement la connexion point à site en fonction des certificats clients individuels. Cela est différent de la suppression d’un certificat racine approuvé. Si vous supprimez un fichier .cer de certificat racine approuvé d’Azure, vous révoquez l’accès pour tous les certificats clients générés/signés par le certificat racine révoqué. Le fait de révoquer un certificat client plutôt que le certificat racine permet de continuer à utiliser les autres certificats générés à partir du certificat racine pour l’authentification.

La pratique courante consiste à utiliser le certificat racine pour gérer l'accès au niveaux de l'équipe ou de l'organisation, tout en utilisant des certificats clients révoqués pour le contrôle d'accès précis des utilisateurs individuels.

Vous pouvez révoquer un certificat client en ajoutant son empreinte à la liste de révocation.

1. Récupérez l’empreinte du certificat client. Pour plus d’informations, consultez l’article [Comment : récupérer l’empreinte numérique d’un certificat](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
1. Copiez les informations dans un éditeur de texte et supprimez tous les espaces afin d’obtenir une chaîne continue.
1. Accédez à la page **Configuration de point à site** de la passerelle de réseau virtuel. Il s’agit de la page que vous avez utilisé pour [charger un certificat racine approuvé](#uploadfile).
1. Dans la section **Certificats révoqués**, entrez un nom convivial pour le certificat (il ne s’agit pas forcément du nom commun du certificat).
1. Copiez et collez la chaîne d’empreinte numérique dans le champ **Empreinte**.
1. L’empreinte est validée, puis automatiquement ajoutée à la liste de révocation. Un message apparaît pour indiquer que la liste est en cours de mise à jour. 
1. Une fois la mise à jour terminée, le certificat ne peut plus être utilisé pour se connecter. Les clients qui tentent de se connecter à l’aide de ce certificat reçoivent un message indiquant que le certificat n’est plus valide.

## <a name="point-to-site-faq"></a><a name="faq"></a>Forum Aux Questions sur les connexions point à site

Pour accéder aux questions fréquentes (FAQ), consultez la [FAQ](vpn-gateway-vpn-faq.md#P2S).

## <a name="next-steps"></a>Étapes suivantes
Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez [Machines virtuelles](../index.yml). Pour plus d’informations sur la mise en réseau et les machines virtuelles, consultez [Vue d’ensemble du réseau de machines virtuelles Azure et Linux](../virtual-network/network-overview.md).

Pour plus d’informations sur la résolution des problèmes liés aux connexions de point à site, consultez l’article [Résolution des problèmes de connexion de point à site Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).