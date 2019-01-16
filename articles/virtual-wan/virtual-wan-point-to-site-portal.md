---
title: Créer une connexion point à site vers Azure à l’aide d’Azure Virtual WAN | Microsoft Docs
description: Dans ce didacticiel, découvrez comment utiliser Azure Virtual WAN pour créer une connexion VPN point à site vers Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect remote users to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 9d5533932ef9ab521b623c18a0c3a27b663c56f8
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077400"
---
# <a name="tutorial-create-a-point-to-site-connection-using-azure-virtual-wan-preview"></a>Tutoriel : Créer une connexion point à site à l’aide d’Azure Virtual WAN (préversion)

Ce didacticiel vous montre comment utiliser Azure Virtual WAN pour vous connecter à vos ressources dans Azure via une connexion VPN IPsec/IKE (IKEv2) ou OpenVPN. Pour utiliser ce type de connexion, un client doit être configuré sur l’ordinateur client. Pour plus d’informations sur Azure Virtual WAN, consultez l’article [Vue d'ensemble d’Azure Virtual WAN](virtual-wan-about.md).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un WAN
> * Créer une configuration P2S
> * Créer un hub
> * Appliquer la configuration P2S à un hub
> * Connecter un réseau virtuel à un hub
> * Télécharger et appliquer la configuration de client VPN
> * Afficher votre WAN virtuel
> * Afficher l’intégrité des ressources
> * Surveiller une connexion

> [!IMPORTANT]
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>Inscrire cette fonctionnalité

Pour inscrire facilement cette fonctionnalité à l’aide d’Azure Cloud Shell, cliquez sur **Essayer**. Si vous préférez exécuter PowerShell localement, vérifiez que vous disposez de la dernière version et connectez-vous en utilisant les commandes **Connect-AzureRmAccount** et **Select-AzureRmSubscription**.

>[!NOTE]
>Si vous n’inscrivez pas cette fonctionnalité, vous ne serez pas en mesure de l’utiliser, ni de la voir dans le portail.
>
>

Après avoir cliqué sur **Essayer** pour ouvrir Azure Cloud Shell, copiez et collez les commandes suivantes :

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowP2SCortexAccess
```
 
```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowP2SCortexAccess
```

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

Une fois que la fonctionnalité s’affiche comme étant inscrite, réinscrivez l’abonnement auprès de l’espace de noms Microsoft.Network.

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Créez un réseau virtuel

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2. Créer un WAN virtuel

Dans un navigateur, accédez au [Portail Azure (préversion)](https://aka.ms/azurevirtualwanpreviewfeatures) et connectez-vous avec votre compte Azure.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Créer un hub

> [!NOTE]
> À cette étape, ne sélectionnez pas le paramètre permettant d’inclure la passerelle de point à site.
>

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="site"></a>4. Créer une configuration P2S

Une configuration P2S définit les paramètres permettant de connecter des clients distants.

1. Naviguez jusqu’à **Toutes les ressources**.
2. Cliquez sur le réseau virtuel étendu que vous avez créé.
3. Sous **Architecture Virtual WAN**, cliquez sur **Configurations point à site**.
4. Cliquez sur **+ Ajouter une configuration point à site** en haut de la page pour ouvrir la page **Créer une configuration point à site**.
5. Sur la page **Créer une configuration point à site**, renseignez les champs suivants :

  *  **Nom de la configuration** : nom par lequel vous souhaitez faire référence à votre configuration.
  *  **Type de tunnel** : protocole à utiliser pour le tunnel.
  *  **Pool d’adresses** : pool d’adresses IP de provenance des clients.
  *  **Nom du certificat racine** : nom descriptif pour le certificat.
  *  **Données du certificat racine** : données de certificat X.509 encodé en Base 64.

5. Cliquez sur **Créer** pour créer la configuration.

## <a name="hub"></a>5. Modifier l’affectation du hub

1. Dans la page de votre réseau étendu virtuel, cliquez sur **Hubs**.
2. Sélectionnez le hub auquel vous souhaitez assigner la configuration point à site.
3. Cliquez sur **« ... »** et choisissez **Edit virtual hub** (Modifier le hub virtuel)
4. Cochez **Inclure la passerelle point à site**.
5. Sélectionnez les **unités d’échelle de passerelle** et la **configuration point à site** ainsi qu’un **pool d’adresses** pour les clients.
6. Cliquez sur **Confirmer**. 
7. L’opération peut prendre jusqu’à 30 minutes.

## <a name="vnet"></a>6. Connecter votre réseau virtuel à un hub

Dans cette étape, vous créez la connexion de peering entre votre hub et un réseau virtuel. Répétez ces étapes pour chaque réseau virtuel que vous souhaitez connecter.

1. Dans la page de votre réseau étendu, cliquez sur **Connexion réseau de réseau virtuel**.
2. Dans la page de connexion de réseau virtuel, cliquez sur **+ Ajouter une connexion**.
3. Dans la page **Ajouter une connexion**, renseignez les champs suivants :

    * **Nom de connexion** : nommez votre connexion.
    * **Hubs** : sélectionnez le hub que vous souhaitez associer à cette connexion.
    * **Abonnement** : vérifiez l’abonnement.
    * **Réseau virtuel** : sélectionnez le réseau virtuel que vous souhaitez connecter à ce hub. Le réseau virtuel ne peut pas avoir une passerelle de réseau virtuel déjà existante.

## <a name="device"></a>7. Télécharger le profil VPN

Utilisez le profil VPN pour configurer vos clients.

1. Dans la page de votre réseau étendu virtuel, cliquez sur **Hubs**.
2. Sélectionnez le hub dont vous souhaitez télécharger le profil.
3. Cliquez sur **« ... »** et choisissez **Télécharger le profil**. 
4. Une fois que le fichier a terminé la création, vous pouvez cliquer sur le lien pour le télécharger.
4. Utilisez le fichier de profil pour configurer les clients de point à site.

## <a name="device"></a>8. Configurer les clients de point à site
Pour configurer les clients avec accès à distance, utilisez le profil téléchargé. La procédure pour chaque système d’exploitation étant différente, veuillez suivre les instructions appropriées ci-dessous :

### <a name="microsoft-windows"></a>Microsoft Windows
#### <a name="openvpn"></a>OpenVPN

1.  Téléchargez et installez le client OpenVPN depuis le site web officiel.
2.  Téléchargez le profil VPN pour la passerelle. Pour y parvenir, accédez à l’onglet Configurations point à site dans le portail Azure, ou saisissez New-AzureRmVpnClientConfiguration dans PowerShell.
3.  Décompressez le profil. Utilisez le bloc-notes pour ouvrir le fichier de configuration vpnconfig.ovpn à partir du dossier OpenVPN.
4.  Renseignez la section du certificat client P2S avec la clé publique du certificat client P2S en base64. Dans un certificat au format PEM, vous pouvez simplement ouvrir le fichier .cer et copier la clé base64 entre les en-têtes de certificat. Découvrez ici comment exporter un certificat pour obtenir la clé publique chiffrée.
5.  Renseignez la section de la clé privée avec la clé privée du certificat client P2S en base64. Découvrez ici comment extraire la clé privée.
6.  Ne modifiez aucun autre champ. Utilisez la configuration complétée dans l’entrée client pour vous connecter au VPN.
7.  Copiez le fichier vpnconfig.ovpn dans C:\Program Files\OpenVPN\config folder.
8.  Cliquez avec le bouton droit de la souris sur l’icône OpenVPN de la barre d’état du système et cliquez sur l’option de connexion.

#### <a name="ikev2"></a>IKEv2

1. Sélectionnez les fichiers de configuration du client VPN qui correspondent à l’architecture de l’ordinateur Windows. Pour une architecture de processeur 64 bits, choisissez le package du programme d’installation « VpnClientSetupAmd64 ». Pour une architecture de processeur 32 bits, choisissez le package du programme d’installation « VpnClientSetupX86 ».
2. Double-cliquez sur le package pour lancer l’installation. Si une fenêtre contextuelle SmartScreen s’affiche, cliquez sur Plus d’infos, puis sur Exécuter quand même.
3. Sur l’ordinateur client, accédez à Paramètres réseau, puis cliquez sur VPN. La connexion VPN indique le nom du réseau virtuel auquel elle se connecte.
4. Avant de tenter de vous connecter, vérifiez que vous avez installé un certificat client sur l’ordinateur client. Un certificat client est requis pour l’authentification lors de l’utilisation du type d’authentification de certificat Azure natif. Pour plus d’informations sur la génération de certificats, consultez l’article Générer des certificats. Pour plus d’informations sur l’installation d’un certificat client, consultez l’article Installer un certificat client.

### <a name="mac-os-x"></a>Mac (OS X)
#### <a name="openvpn"></a>OpenVPN

1.  Télécharger et installer un client OpenVPN (comme TunnelBlik) à partir de https://tunnelblick.net/downloads.html 
2.  Téléchargez le profil VPN pour la passerelle. Pour y parvenir, accédez à l’onglet Configuration point à site dans le portail Azure, ou saisissez New-AzureRmVpnClientConfiguration dans PowerShell.
3.  Décompressez le profil. Utilisez le bloc-notes pour ouvrir le fichier de configuration vpnconfig.ovpn à partir du dossier OpenVPN.
4.  Renseignez la section du certificat client P2S avec la clé publique du certificat client P2S en base64. Dans un certificat au format PEM, vous pouvez simplement ouvrir le fichier .cer et copier la clé base64 entre les en-têtes de certificat. Découvrez ici comment exporter un certificat pour obtenir la clé publique chiffrée.
5.  Renseignez la section de la clé privée avec la clé privée du certificat client P2S en base64. Découvrez ici comment extraire la clé privée.
6.  Ne modifiez aucun autre champ. Utilisez la configuration complétée dans l’entrée client pour vous connecter au VPN.
7.  Double-cliquez sur le fichier de profil pour créer le profil dans Tunnelblik.
8.  Lancez Tunnelblik à partir du dossier d’applications.
9.  Cliquez sur l’icône Tunneblik dans la barre d’état du système et choisissez l’option de connexion.

#### <a name="ikev2"></a>IKEv2

Azure ne fournit pas de fichier mobileconfig pour l’authentification par certificat Azure native. Vous devez configurer manuellement le client VPN IKEv2 natif sur chaque Mac qui se connectera à Azure. Le dossier Générique contient toutes les informations dont vous avez besoin pour la configuration. Si vous ne voyez pas le dossier Générique dans votre téléchargement, il est probable qu’IKEv2 n’était pas sélectionné comme type de tunnel. Une fois IKEv2 sélectionné, générez à nouveau le fichier zip pour récupérer le dossier Générique. Ce dossier contient les fichiers suivants :

- Le fichier VpnSettings.xml, qui contient d’importants paramètres tels que l’adresse et le type de tunnel du serveur.
- Le fichier VpnServerRoot.cer, qui contient le certificat racine requis pour valider la passerelle VPN Azure lors de la configuration de la connexion P2S.

## <a name="viewwan"></a>9. Afficher votre WAN virtuel

1. Accédez au WAN virtuel.
2. Dans la page Vue d’ensemble, chaque point sur la carte représente un hub. Placez le curseur sur n’importe quel point pour afficher le résumé de l’intégrité du hub.
3. Dans la section des hubs et des connexions, vous pouvez afficher l’état du hub, le site, la région, l’état de la connexion VPN et les octets entrés et sortis.

## <a name="viewhealth"></a>10. Afficher l’intégrité des ressources

1. Accédez à votre réseau étendu.
2. Sur la page de votre WAN, dans la section **SUPPORT + Dépannage**, cliquez sur **Intégrité** pour afficher votre ressource.

## <a name="connectmon"></a>11. Surveiller une connexion

Créez une connexion pour surveiller les communications entre une machine virtuelle Azure et un site distant. Pour plus d’informations sur la façon de configurer un contrôleur de connexion, consultez [Surveiller une communication réseau](~/articles/network-watcher/connection-monitor.md). Le champ source est l’adresse IP de la machine virtuelle dans Azure, et l’adresse IP de destination est le l’adresse IP du site.

## <a name="cleanup"></a>12. Supprimer des ressources

Quand vous n’avez plus besoin de ces ressources, vous pouvez utiliser [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources et toutes les ressources qu’il contient. Remplacez « myResourceGroup » par le nom de votre groupe de ressources et exécutez la commande PowerShell suivante :

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer un WAN
> * Créer un site
> * Créer un hub
> * Connecter un hub à un site
> * Connecter un réseau virtuel à un hub
> * Télécharger et appliquer la configuration de périphérique VPN
> * Afficher votre WAN virtuel
> * Afficher l’intégrité des ressources
> * Surveiller une connexion

Pour plus d’informations sur le WAN virtuel, consultez la page [Vue d'ensemble de WAN virtuel](virtual-wan-about.md).
