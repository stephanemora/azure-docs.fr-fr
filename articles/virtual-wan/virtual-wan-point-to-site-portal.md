---
title: Créer une connexion point à site vers Azure à l’aide d’Azure Virtual WAN | Microsoft Docs
description: Dans ce didacticiel, découvrez comment utiliser Azure Virtual WAN pour créer une connexion VPN point à site vers Azure.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 03/18/2020
ms.author: alzam
ms.openlocfilehash: fd415e1da00f52a9a3b55c946a07a30cf841cf4a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80060308"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Tutoriel : Créer une connexion de VPN utilisateur à l’aide d’Azure Virtual WAN

Ce didacticiel vous montre comment utiliser Azure Virtual WAN pour vous connecter à vos ressources dans Azure via une connexion VPN IPsec/IKE (IKEv2) ou OpenVPN. Pour utiliser ce type de connexion, un client doit être configuré sur l’ordinateur client. Pour plus d’informations sur le WAN virtuel, consultez [Vue d'ensemble de WAN virtuel](virtual-wan-about.md)

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un WAN
> * Créer un hub
> * Créer une configuration P2S
> * Télécharger un profil de client VPN
> * Appliquer la configuration P2S à un hub
> * Connecter un réseau virtuel à un hub
> * Télécharger et appliquer la configuration de client VPN
> * Afficher votre WAN virtuel

![Diagramme WAN virtuel](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration :

* Vous avez un réseau virtuel auquel vous souhaitez vous connecter. Vérifiez qu’aucun des sous-réseaux de votre réseau local ne chevauche les réseaux virtuels auxquels vous souhaitez vous connecter. Pour créer un réseau virtuel dans le portail Azure, consultez le [guide de démarrage rapide](../virtual-network/quick-create-portal.md).

* Votre réseau virtuel n’a pas de passerelle de réseau virtuel. Si votre réseau virtuel dispose d’une passerelle (VPN ou ExpressRoute), vous devez supprimer toutes les passerelles. Cette configuration nécessite que les réseaux virtuels soient connectés à la passerelle hub Virtual WAN.

* Obtenez une plage d’adresses IP pour la région de votre hub. Le hub est un réseau virtuel qui est créé et utilisé par Virtual WAN. La plage d’adresses que vous spécifiez pour le hub ne peut pas chevaucher les réseaux virtuels existants auxquels vous vous connectez. Elle ne peut pas non plus chevaucher vos plages d’adresses auxquelles vous vous connectez en local. Si vous ne maîtrisez pas les plages d’adresses IP situées dans votre configuration de réseau local, contactez une personne en mesure de vous aider.

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Créer un WAN virtuel

Dans un navigateur, accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

1. Accédez à la page WAN Virtuel. Dans le portail, cliquez sur **+Créer une ressource**. Tapez **WAN virtuel** dans la zone de recherche et sélectionnez Entrée.
2. Sélectionnez **WAN virtuel** dans les résultats. Dans la page Virtual WAN, cliquez sur **Créer** pour ouvrir la page Créer un WAN.
3. Dans la page **Créer un WAN**, sous l’onglet **Fonctions de base**, renseignez les champs suivants :

   ![WAN virtuel](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Abonnement** : sélectionnez l’abonnement que vous voulez utiliser.
   * **Groupe de ressources** : créez-en un ou utilisez-en un qui existe déjà.
   * **Emplacement du groupe de ressources** : choisissez un emplacement de la ressource dans la liste déroulante. Un WAN est une ressource globale et ne réside pas dans une région particulière. Toutefois, vous devez sélectionner une région pour gérer et localiser plus facilement la ressource WAN que vous créez.
   * **Nom** : tapez le nom que vous souhaitez donner à votre WAN.
   * **Type :** Standard. Si vous créez un WAN de base, vous ne pourrez créer qu’un hub de base. Les hubs de base offrent uniquement une connectivité de site VPN à site.
4. Quand vous avez fini de renseigner les champs, cliquez sur **Vérifier + Créer**.
5. Après la validation, sélectionnez **Créer** pour créer le WAN virtuel.

## <a name="create-an-empty-virtual-hub"></a><a name="hub"></a>Créer un hub virtuel vide

1. Sous votre WAN virtuel, sélectionnez Hubs et cliquez sur **+Nouveau hub**

   ![nouveau site](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. Dans la page Créer un hub virtuel, renseignez les champs suivants.

   **Région** : sélectionnez la région dans laquelle vous souhaitez déployer le hub virtuel.

   **Nom** : entrez le nom que vous souhaitez donner à votre hub virtuel.

   **Espace d’adressage privé du hub** : plage d’adresses du hub en notation CIDR.

   ![nouveau site](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. Cliquez sur **Vérifier + créer**.
4. Sur la page **Validation effectuée**, cliquez sur **Créer**.

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Créer une configuration P2S

Une configuration P2S définit les paramètres permettant de connecter des clients distants.

1. Naviguez jusqu’à **Toutes les ressources**.
2. Cliquez sur le réseau virtuel étendu que vous avez créé.
3. Cliquez sur **+Créer une configuration de VPN utilisateur** en haut de la page pour ouvrir la page **Créer une configuration de VPN utilisateur**.

   ![nouveau site](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. Sur la page **Créer une configuration de VPN utilisateur**, renseignez les champs suivants :

   **Nom de la configuration** : nom par lequel vous souhaitez faire référence à votre configuration.

   **Type de tunnel** : protocole à utiliser pour le tunnel.

   **Nom du certificat racine** : nom descriptif pour le certificat.

   **Données du certificat public** : données de certificat X.509 encodé en Base 64.
  
5. Cliquez sur **Créer** pour créer la configuration.

## <a name="edit-hub-assignment"></a><a name="edit"></a>Modifier l’affectation du hub

1. Accédez au panneau **Hubs** sous le WAN virtuel
2. Sélectionnez le hub que vous souhaitez associer à la configuration du serveur VPN et cliquez sur **...** .

   ![nouveau site](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. Cliquez sur **Modifier le hub virtuel**.
4. Cochez la case **Inclure la passerelle point à site** et sélectionnez l’**unité d’échelle de passerelle** souhaitée.

   ![nouveau site](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Entrez le **Pool d’adresses** à partir duquel les clients VPN se verront attribuer des adresses IP.
6. Cliquez sur **Confirmer**.
7. L’opération peut prendre jusqu’à 30 minutes.

## <a name="download-vpn-profile"></a><a name="download"></a>Télécharger le profil VPN

Utilisez le profil VPN pour configurer vos clients.

1. Dans la page de votre WAN virtuel, cliquez sur **Configurations de VPN utilisateur**.
2. En haut de la page, cliquez sur **Télécharger la configuration de VPN utilisateur**.
3. Une fois que le fichier a terminé la création, vous pouvez cliquer sur le lien pour le télécharger.
4. Utilisez le fichier de profil pour configurer les clients VPN.

### <a name="configure-user-vpn-clients"></a>Configurer les clients VPN utilisateur
Pour configurer les clients avec accès à distance, utilisez le profil téléchargé. La procédure pour chaque système d’exploitation étant différente, veuillez suivre les instructions appropriées ci-dessous :

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Téléchargez et installez le client OpenVPN depuis le site web officiel.
2. Téléchargez le profil VPN pour la passerelle. Pour cela, accédez à l’onglet Configurations de VPN utilisateur dans le portail Azure, ou saisissez New-AzureRmVpnClientConfiguration dans PowerShell.
3. Décompressez le profil. Utilisez le bloc-notes pour ouvrir le fichier de configuration vpnconfig.ovpn à partir du dossier OpenVPN.
4. Renseignez la section du certificat client P2S avec la clé publique du certificat client P2S en base64. Dans un certificat au format PEM, vous pouvez simplement ouvrir le fichier .cer et copier la clé base64 entre les en-têtes de certificat. Pour connaître les étapes à suivre, consultez [Guide pratique pour exporter un certificat afin d’obtenir la clé publique encodée](certificates-point-to-site.md).
5. Renseignez la section de la clé privée avec la clé privée du certificat client P2S en base64. Pour connaître les étapes à suivre, consultez [Guide pratique pour extraire la clé privée](howto-openvpn-clients.md#windows).
6. Ne modifiez aucun autre champ. Utilisez la configuration complétée dans l’entrée client pour vous connecter au VPN.
7. Copiez le fichier vpnconfig.ovpn dans C:\Program Files\OpenVPN\config folder.
8. Cliquez avec le bouton droit de la souris sur l’icône OpenVPN de la barre d’état du système et cliquez sur l’option de connexion.

##### <a name="ikev2"></a>IKEv2

1. Sélectionnez les fichiers de configuration du client VPN qui correspondent à l’architecture de l’ordinateur Windows. Pour une architecture de processeur 64 bits, choisissez le package du programme d’installation « VpnClientSetupAmd64 ». Pour une architecture de processeur 32 bits, choisissez le package du programme d’installation « VpnClientSetupX86 ».
2. Double-cliquez sur le package pour lancer l’installation. Si une fenêtre contextuelle SmartScreen s’affiche, cliquez sur Plus d’infos, puis sur Exécuter quand même.
3. Sur l’ordinateur client, accédez à Paramètres réseau, puis cliquez sur VPN. La connexion VPN indique le nom du réseau virtuel auquel elle se connecte.
4. Avant de tenter de vous connecter, vérifiez que vous avez installé un certificat client sur l’ordinateur client. Un certificat client est requis pour l’authentification lors de l’utilisation du type d’authentification de certificat Azure natif. Pour plus d’informations sur la génération de certificats, consultez [Générer des certificats](certificates-point-to-site.md). Pour plus d’informations sur l’installation d’un certificat client, consultez [Installer un certificat client](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Afficher votre WAN virtuel

1. Accédez au WAN virtuel.
2. Dans la page Vue d’ensemble, chaque point sur la carte représente un hub.
3. Dans la section des hubs et des connexions, vous pouvez voir l’état du hub, le site, la région, l’état de la connexion VPN et les octets entrés et sortis.


## <a name="clean-up-resources"></a><a name="cleanup"></a>Supprimer des ressources

Quand vous n’avez plus besoin de ces ressources, vous pouvez utiliser [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources et toutes les ressources qu’il contient. Remplacez « myResourceGroup » par le nom de votre groupe de ressources et exécutez la commande PowerShell suivante :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Virtual WAN, consultez la page [Vue d’ensemble de Virtual WAN](virtual-wan-about.md).
