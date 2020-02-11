---
title: Azure VMware Solutions (AVS) - Configurer une connexion VPN entre un réseau local et un cloud privé AVS
description: Explique comment configurer une connexion VPN site à site ou point à site entre votre réseau local et votre cloud privé AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fbd2b227c9292593a7652044ef4c013bf0cfaf8e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77017001"
---
# <a name="configure-a-vpn-connection-to-your-avs-private-cloud"></a>Configurer une connexion VPN à votre cloud privé AVS

Les passerelles VPN vous permettent de vous connecter à un réseau AVS à partir de votre réseau local et d'un ordinateur client à distance. Dans cet article, vous trouverez des informations sur la configuration des passerelles VPN à partir du portail AVS. Une connexion VPN entre votre réseau local et votre réseau AVS vous permet d'accéder à l'environnement vCenter et aux charges de travail sur votre cloud privé AVS. AVS prend en charge les passerelles VPN point à site et site à site.

## <a name="vpn-gateway-types"></a>Types de passerelle VPN

* La connexion **VPN point à site** constitue le moyen le plus simple de vous connecter à votre cloud privé AVS à partir de votre ordinateur. Utilisez la connectivité VPN point à site pour vous connecter au cloud privé AVS à distance.
* La connexion **VPN site à site** vous permet de configurer les charges de travail de votre cloud privé AVS pour accéder aux services locaux. Vous pouvez également utiliser le service Active Directory local comme source d'identité pour l'authentification auprès du serveur vCenter de votre cloud privé AVS. Actuellement, le type **VPN basé sur une stratégie** est pris en charge.

Dans une région, vous pouvez créer une passerelle VPN de site à site et une passerelle VPN de point à site.

## <a name="point-to-site-vpn"></a>VPN de point à site

Pour créer une passerelle VPN de point à site, consultez [Créer une passerelle VPN de point à site](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-avs-using-point-to-site-vpn"></a>Se connecter à AVS à l'aide d'un VPN point à site

Un client VPN est nécessaire pour la connexion à AVS depuis votre ordinateur. Téléchargez le [client OpenVPN](https://openvpn.net/community-downloads/) pour Windows ou [Viscosity](https://www.sparklabs.com/viscosity/download/) pour Mac OS et OS X.

1. Lancez le portail AVS et sélectionnez **Réseau**.
2. Sélectionnez **Passerelle VPN**.
3. Dans la liste des passerelles VPN, cliquez sur la passerelle VPN de point à site.
4. Sélectionnez **Utilisateurs**.
5. Cliquez sur **Télécharger la configuration de mon VPN**

    ![Télécharger une configuration VPN](media/download-p2s-vpn-configuration.png)

6. Importer la configuration sur votre client VPN

    * Instructions pour [l’importation de la configuration sur un client Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instructions pour [l’importation de la configuration sur un client macOS ou OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Connectez-vous à la passerelle VPN AVS.

L’exemple ci-dessous illustre l’importation de la connexion à l’aide du client **Viscosity**.

#### <a name="import-connection-on-viscosity-client"></a>Importer la connexion sur le client Viscosity

1. Extrayez le contenu de la configuration VPN à partir du fichier .zip téléchargé.

2. Ouvrez Viscosity sur votre ordinateur.

3. Cliquez sur l’icône **+** et sélectionnez **Importer connexion** > **À partir d’un fichier**.

    ![Importer la configuration VPN à partir d’un fichier](media/import-p2s-vpn-config.png)

4. Sélectionnez le fichier de configuration OpenVPN (. ovpn) pour le protocole que vous souhaitez utiliser, puis cliquez sur **Ouvrir**.

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

La connexion s’affiche maintenant dans le menu Viscosity.

#### <a name="connect-to-the-vpn"></a>Connexion au VPN

Pour vous connecter au VPN à l’aide du client OpenVPN Viscosity, sélectionnez la connexion dans le menu. L’icône de menu se met à jour pour indiquer que la connexion est établie.

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-avs-private-clouds"></a>Connexion à plusieurs clouds privés AVS

Une connexion VPN point à site résout les noms DNS du premier cloud privé AVS que vous créez. Pour accéder à d'autres clouds privés AVS, vous devez mettre à jour le serveur DNS sur votre client VPN.

1. Lancez le [portail AVS](access-cloudsimple-portal.md).

2. Accédez à **Ressources** > **Clouds privés AVS**, puis sélectionnez le cloud privé AVS auquel vous souhaitez vous connecter.

3. Sur la page **Résumé** du cloud privé AVS, copiez l'adresse IP du serveur DNS du cloud privé AVS sous **Informations de base**.

    ![Serveurs DNS de cloud privé AVS](media/private-cloud-dns-server.png)

4. Cliquez avec le bouton droit sur l’icône de Viscosity dans la barre d' état système de votre ordinateur, puis sélectionnez **Préférences**.

    ![VPN](media/vis00.png)

5. Sélectionnez la connexion VPN AVS.

    ![Connexion VPN](media/viscosity-client.png)

6. Cliquez sur **Modifier** pour modifier les propriétés de connexion.

    ![Modifier la connexion VPN](media/viscosity-edit-connection.png)

7. Cliquez sur l'onglet **Réseau** et entrez les adresses IP du serveur DNS du cloud privé AVS, séparées par une virgule ou un espace, ainsi que le domaine sous la forme ```AVS.io```. Sélectionnez **Ignorer les paramètres DNS envoyés par le serveur VPN**.

    ![Mise en réseau VPN](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Pour vous connecter à votre premier cloud privé AVS, supprimez ces paramètres et connectez-vous au serveur VPN.

## <a name="site-to-site-vpn"></a>VPN de site à site

Pour créer une passerelle VPN de site à site, consultez [Créer une passerelle VPN de site à site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway). La connexion VPN site à site entre votre réseau local et votre cloud privé AVS offre les avantages suivants. 

* Accessibilité du serveur vCenter de votre cloud privé AVS à partir de n'importe quelle station de travail de votre réseau local
* Utilisation de votre Active Directory local comme source d’identité vCenter
* Facilité de transfert des modèles de machines virtuelles, des fichiers ISO et d'autres fichiers entre vos ressources locales et le serveur vCenter de votre cloud privé AVS
* Accessibilité des charges de travail exécutées sur votre cloud privé AVS depuis votre réseau local

Pour configurer votre passerelle VPN locale en mode haute disponibilité, consultez [Configurer une connexion VPN à haute disponibilité](high-availability-vpn-connection.md).

> [!IMPORTANT]
>    1. Définissez le clamping du MSS TCP sur 1200 sur votre périphérique VPN. Dans le cas où vos périphériques VPN ne prendraient pas en charge le réglage de la taille maximale de segment, vous pouvez à la place définir l’unité de transmission maximale dans l’interface de tunnel sur 1240 octets.
> 2. Après la configuration du VPN site à site, transférez les requêtes DNS relatives à *.AVS.io vers les serveurs DNS du cloud privé AVS. Suivez les instructions de [Configuration du DNS local](on-premises-dns-setup.md).
