---
title: Créer une connexion point à site vers Azure à l’aide d’Azure Virtual WAN | Microsoft Docs
description: Dans ce didacticiel, découvrez comment utiliser Azure Virtual WAN pour créer une connexion VPN point à site vers Azure.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 06/29/2020
ms.author: alzam
ms.openlocfilehash: 9c93ad0357011008c45b2898260a655509b02dc2
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560718"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Tutoriel : Créer une connexion de VPN utilisateur à l’aide d’Azure Virtual WAN

Ce didacticiel vous montre comment utiliser Azure Virtual WAN pour vous connecter à vos ressources dans Azure via une connexion VPN IPsec/IKE (IKEv2) ou OpenVPN. Pour utiliser ce type de connexion, un client doit être configuré sur l’ordinateur client. Pour plus d’informations sur le WAN virtuel, consultez [Vue d'ensemble de WAN virtuel](virtual-wan-about.md)

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un WAN
> * Créer une configuration P2S
> * Créer un hub
> * Spécifier les serveurs DNS
> * Télécharger un profil de client VPN
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

1. Accédez à la page WAN Virtuel. Dans le portail Azure, sélectionnez **+Créer une ressource**. Tapez **Virtual WAN** dans la zone de recherche et sélectionnez **Entrée**.
1. Sélectionnez **Virtual WAN** dans les résultats. Dans la page Virtual WAN, sélectionnez **Créer** pour ouvrir la page Créer un WAN.
1. Dans la page **Créer un WAN**, sous l’onglet **Fonctions de base**, renseignez les champs suivants :

   ![WAN virtuel](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Abonnement** : sélectionnez l’abonnement que vous voulez utiliser.
   * **Groupe de ressources** : créez-en un ou utilisez-en un qui existe déjà.
   * **Emplacement du groupe de ressources** : choisissez un emplacement de la ressource dans la liste déroulante. Un WAN est une ressource globale et ne réside pas dans une région particulière. Toutefois, vous devez sélectionner une région pour gérer et localiser plus facilement la ressource WAN que vous créez.
   * **Nom** : tapez le nom que vous souhaitez donner à votre WAN.
   * **Type :** Standard. Si vous créez un WAN de base, vous ne pourrez créer qu’un hub de base. Les hubs de base offrent uniquement une connectivité de site VPN à site.
1. Quand vous avez fini de renseigner les champs, cliquez sur **Vérifier + Créer**.
1. Après la validation, sélectionnez **Créer** pour créer le WAN virtuel.

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Créer une configuration P2S

Une configuration P2S définit les paramètres permettant de connecter des clients distants.

1. Naviguez jusqu’à **Toutes les ressources**.
1. Sélectionnez l’instance Virtual WAN que vous avez créée.
1. Sélectionnez **+Créer une configuration de VPN utilisateur** en haut de la page pour ouvrir la page **Créer une configuration de VPN utilisateur**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/p2s1.jpg" alt-text="Configurations de VPN utilisateur":::

1. Sur la page **Créer une configuration de VPN utilisateur**, renseignez les champs suivants :

   * **Nom de la configuration** : nom par lequel vous souhaitez faire référence à votre configuration.
   * **Type de tunnel** : protocole à utiliser pour le tunnel.
   * **Nom du certificat racine** : nom descriptif pour le certificat.
   * **Données du certificat public** : données de certificat X.509 encodé en Base 64.
  
1. Sélectionnez **Créer** pour créer la configuration.

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>Créer un hub avec une passerelle point à site

1. Sous votre WAN virtuel, sélectionnez Hubs, puis **+ Nouveau hub**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub1.jpg" alt-text="nouveau hub":::

1. Dans la page Créer un hub virtuel, renseignez les champs suivants.

   * **Région** : sélectionnez la région dans laquelle vous souhaitez déployer le hub virtuel.
   * **Nom** : entrez le nom que vous souhaitez donner à votre hub virtuel.
   * **Espace d’adressage privé du hub** : plage d’adresses du hub en notation CIDR.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub2.jpg" alt-text="créer un hub virtuel":::

1. Sous l’onglet Point à site, complétez les champs suivants :

   * **Unités d’échelle de la passerelle**, qui représentent la capacité d’agrégation de la passerelle VPN utilisateur.
   * **Configuration point à site**, que vous avez créée à l’étape précédente.
   * **Pool d’adresses des clients**, pour les utilisateurs distants.
   * **Adresse IP de serveur DNS personnalisé**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub-with-p2s.png" alt-text="hub avec point à site":::

1. Sélectionnez **Revoir + créer**.
1. Dans la page **Validation réussie**, sélectionnez **Créer**.

## <a name="specify-dns-server"></a><a name="dns"></a>Spécifier un serveur DNS

Les passerelles VPN utilisateur Virtual WAN vous permettent d’indiquer jusqu’à 5 serveurs DNS. Vous pouvez configurer cela durant le processus de création du hub, ou modifier celui-ci ultérieurement. Pour ce faire, localisez le hub virtuel. Sous **VPN utilisateur (point à site)** , cliquez sur Configurer et entrez l’adresse ou les adresses IP de serveur DNS dans les zones de texte **Serveurs DNS personnalisés**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="DNS personnalisé" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>Télécharger le profil VPN

Utilisez le profil VPN pour configurer vos clients.

1. Dans la page de votre WAN virtuel, sélectionnez **Configurations de VPN utilisateur**.
2. En haut de la page, sélectionnez **Télécharger une configuration de VPN utilisateur**. Le téléchargement d’une configuration de niveau WAN fournit un profil VPN utilisateur basé sur Traffic Manager intégré. Pour plus d’informations sur les profils globaux ou les profils basés sur un hub, consultez [Profils hub](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile).   Les scénarios de basculement sont simplifiés avec le profil global.

   Si, pour une raison quelconque, un hub n’est pas disponible, la gestion du trafic intégré fourni par le service garantit la connectivité aux ressources Azure via un hub différent pour les utilisateurs point à site. Vous pouvez toujours télécharger une configuration VPN propre à un hub en accédant au hub spécifique. Sous **VPN utilisateur (point à site)** , téléchargez le profil **VPN utilisateur** du hub virtuel.

1. Une fois la création du fichier terminée, vous pouvez sélectionner le lien pour le télécharger.
1. Utilisez le fichier de profil pour configurer les clients VPN.

### <a name="configure-user-vpn-clients"></a>Configurer les clients VPN utilisateurs

Pour configurer les clients avec accès à distance, utilisez le profil téléchargé. La procédure pour chaque système d’exploitation étant différente, veuillez suivre les instructions appropriées ci-dessous :

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Téléchargez et installez le client OpenVPN depuis le site web officiel.
1. Téléchargez le profil VPN pour la passerelle. Pour cela, accédez à l’onglet Configurations de VPN utilisateur dans le portail Azure, ou saisissez New-AzureRmVpnClientConfiguration dans PowerShell.
1. Décompressez le profil. Utilisez le bloc-notes pour ouvrir le fichier de configuration vpnconfig.ovpn à partir du dossier OpenVPN.
1. Renseignez la section du certificat client P2S avec la clé publique du certificat client P2S en base64. Dans un certificat au format PEM, vous pouvez simplement ouvrir le fichier .cer et copier la clé base64 entre les en-têtes de certificat. Pour connaître les étapes à suivre, consultez [Guide pratique pour exporter un certificat afin d’obtenir la clé publique encodée](certificates-point-to-site.md).
1. Renseignez la section de la clé privée avec la clé privée du certificat client P2S en base64. Pour connaître les étapes à suivre, consultez [Guide pratique pour extraire la clé privée](howto-openvpn-clients.md#windows).
1. Ne modifiez aucun autre champ. Utilisez la configuration complétée dans l’entrée client pour vous connecter au VPN.
1. Copiez le fichier vpnconfig.ovpn dans C:\Program Files\OpenVPN\config folder.
1. Cliquez avec le bouton droit de la souris sur l’icône OpenVPN dans la barre d’état système et sélectionnez **Connexion**.

##### <a name="ikev2"></a>IKEv2

1. Sélectionnez les fichiers de configuration du client VPN qui correspondent à l’architecture de l’ordinateur Windows. Pour une architecture de processeur 64 bits, choisissez le package du programme d’installation « VpnClientSetupAmd64 ». Pour une architecture de processeur 32 bits, choisissez le package du programme d’installation « VpnClientSetupX86 ».
1. Double-cliquez sur le package pour lancer l’installation. Si une fenêtre contextuelle SmartScreen s’affiche, sélectionnez **Plus d’infos**, puis **Exécuter quand même**.
1. Sur l’ordinateur client, accédez à **Paramètres réseau**, puis sélectionnez **VPN**. La connexion VPN indique le nom du réseau virtuel auquel elle se connecte.
1. Avant de tenter de vous connecter, vérifiez que vous avez installé un certificat client sur l’ordinateur client. Un certificat client est requis pour l’authentification lors de l’utilisation du type d’authentification de certificat Azure natif. Pour plus d’informations sur la génération de certificats, consultez [Générer des certificats](certificates-point-to-site.md). Pour plus d’informations sur l’installation d’un certificat client, consultez [Installer un certificat client](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Afficher votre WAN virtuel

1. Accédez au WAN virtuel.
1. Sur la page **Vue d’ensemble**, chaque point sur la carte représente un hub.
1. Dans la section **Hubs et connexions**, vous pouvez voir l’état du hub, le site, la région, l’état de la connexion VPN et les octets entrés et sortis.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Supprimer des ressources

Quand vous n’avez plus besoin de ces ressources, vous pouvez utiliser [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources et toutes les ressources qu’il contient. Remplacez « myResourceGroup » par le nom de votre groupe de ressources et exécutez la commande PowerShell suivante :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Virtual WAN, consultez la page [Vue d’ensemble de Virtual WAN](virtual-wan-about.md).
