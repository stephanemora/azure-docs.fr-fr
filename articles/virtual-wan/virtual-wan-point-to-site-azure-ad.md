---
title: Configurer une connexion VPN utilisateur P2S à l’aide de l’authentification Azure Active Directory
titleSuffix: Azure Virtual WAN
description: Découvrez comment configurer l’authentification Azure Active Directory pour un réseau privé virtuel utilisateur Virtual WAN (connexion point à site).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 08/20/2021
ms.author: cherylmc
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 38006136ddd5a5331699b2768c35ebd794e563a8
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778509"
---
# <a name="configure-a-point-to-site-user-vpn-connection---azure-active-directory-authentication"></a>Configurer une connexion VPN utilisateur point à site - Authentification Azure Active Directory

Cet article vous montre comment configurer l’authentification Azure AD pour un VPN utilisateur dans Virtual WAN afin de vous connecter à vos ressources dans Azure par le biais d’une connexion VPN OpenVPN. L’authentification Azure Active Directory est disponible uniquement pour les passerelles basées sur le protocole OpenVPN. Pour plus d’informations sur Azure Virtual WAN, consultez l’article [Vue d’ensemble d’Azure Virtual WAN](virtual-wan-about.md).

[!INCLUDE [OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

Dans cet article, vous apprendrez comment :

* Créer un WAN virtuel
* Créer une configuration VPN d’utilisateur
* Télécharger un profil VPN utilisateur du WAN virtuel
* Créer un hub virtuel
* Modifier un hub pour ajouter une passerelle P2S
* Connecter un réseau virtuel à un hub virtuel
* Télécharger et appliquer la configuration du client VPN utilisateur
* Afficher votre WAN virtuel

:::image type="content" source="./media/virtual-wan-about/virtualwanp2s.png" alt-text="Diagramme WAN virtuel.":::

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration :

* Vous avez un réseau virtuel auquel vous souhaitez vous connecter. Vérifiez qu’aucun des sous-réseaux de votre réseau local ne chevauche les réseaux virtuels auxquels vous souhaitez vous connecter. Pour créer un réseau virtuel dans le portail Azure, consultez le [guide de démarrage rapide](../virtual-network/quick-create-portal.md).

* Votre réseau virtuel n’a pas de passerelle de réseau virtuel. Si votre réseau virtuel dispose d’une passerelle (VPN ou ExpressRoute), vous devez supprimer toutes les passerelles. Cette configuration nécessite que les réseaux virtuels soient connectés à la passerelle hub Virtual WAN.

* Obtenez une plage d’adresses IP pour la région de votre hub. Le hub est un réseau virtuel qui est créé et utilisé par Virtual WAN. La plage d’adresses que vous spécifiez pour le hub ne peut pas chevaucher les réseaux virtuels existants auxquels vous vous connectez. Elle ne peut pas non plus chevaucher vos plages d’adresses auxquelles vous vous connectez en local. Si vous ne maîtrisez pas les plages d’adresses IP situées dans votre configuration de réseau local, contactez une personne en mesure de vous aider.

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Créer un WAN virtuel

Dans un navigateur, accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-user-vpn-configuration"></a><a name="user-config"></a>Créer une configuration VPN d’utilisateur

Une configuration VPN d’utilisateur définit les paramètres permettant de connecter des clients distants. Il est important de créer la configuration VPN d’utilisateur avant de configurer votre hub virtuel avec les paramètres P2S, car vous devez spécifier la configuration VPN d’utilisateur que vous souhaitez utiliser.

1. Accédez à la page **WAN virtuel -> Configurations de VPN utilisateur**, puis cliquez sur **+ Créer une configuration de VPN utilisateur**.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/user-vpn.png" alt-text="Capture d’écran de la page Créer une configuration de VPN utilisateur.":::
1. Sur la page **Informations de base**, spécifiez les paramètres.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/basics.png" alt-text="Capture d’écran de la page Informations de base.":::

   * **Nom de la configuration** : entrez le nom que vous souhaitez utiliser pour votre configuration VPN d’utilisateur.
    * **Type de tunnel** : sélectionnez OpenVPN dans le menu déroulant.
1. Cliquez sur **Azure Active Directory** pour ouvrir la page.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/values.png" alt-text="Capture d’écran de la page Azure Active Directory.":::

    Basculez **Azure Active Directory** sur **Oui** et fournissez les valeurs suivantes en fonction des détails de votre locataire. Vous pouvez afficher les valeurs nécessaires sur la page Azure Active Directory pour les applications d’entreprise dans le portail.
   * **Méthode d’authentification** : sélectionnez Azure Active Directory.
   * **Public ciblé** : saisissez l’ID de l’application d’entreprise [Azure VPN](openvpn-azure-ad-tenant.md) inscrite dans votre locataire Azure AD. 
   * **Émetteur** - `https://sts.windows.net/<your Directory ID>/`
   * **Locataire AAD** - `https://login.microsoftonline.com/<your Directory ID>`
1. Cliquez sur **Créer** pour créer la configuration de VPN utilisateur. Vous sélectionnerez cette configuration plus tard dans l’exercice.

## <a name="create-an-empty-hub"></a><a name="site"></a>Créer un hub vide

Pour cet exercice, nous créons un hub virtuel vide. Dans la section suivante, vous allez ajouter une passerelle à un hub existant. Toutefois, il est également possible de combiner ces étapes et de créer le hub avec les paramètres de passerelle P2S en une seule fois.

[!INCLUDE [Create an empty hub](../../includes/virtual-wan-empty-hub-include.md)]

## <a name="add-a-p2s-gateway-to-a-hub"></a><a name="hub"></a>Ajouter une passerelle P2S à un hub

Cette section vous montre comment ajouter une passerelle à un hub virtuel existant. Cette étape peut prendre jusqu’à 30 minutes, le temps de finaliser la mise à jour du hub.

1. Accédez à la page **Hubs** sous le WAN virtuel.
1. Sélectionnez le hub auquel associer la configuration du serveur VPN, puis cliquez sur le bouton de sélection ( **...** ) pour afficher le menu. Cliquez ensuite sur **Modifier le hub virtuel**.

   :::image type="content" source="media/virtual-wan-point-to-site-azure-ad/select-hub.png" alt-text="Capture d’écran montrant l’option Modifier le hub virtuel sélectionnée dans le menu." lightbox="media/virtual-wan-point-to-site-azure-ad/select-hub.png":::

1. Sur la page **Modifier le hub virtuel**, activez les cases à cocher **Inclure la passerelle VPN pour les sites VPN** et **Inclure la passerelle point à site** pour afficher les paramètres. Configurez ensuite les valeurs.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/edit-virtual-hub.png" alt-text="Capture d’écran de la page Modifier le hub virtuel.":::

   * **Unités d’échelle de la passerelle** : sélectionnez les unités d’échelle de la passerelle. Les unités d’échelle représentent la capacité d’agrégation de la passerelle VPN utilisateur. Si vous sélectionnez 40 unités d’échelle de passerelle ou plus, planifiez votre pool d’adresses de clients en conséquence. Pour plus d’informations sur l’impact de ce paramètre sur le pool d’adresses de clients, consultez [À propos des pools d’adresses de clients](about-client-address-pools.md). Pour plus d’informations sur les unités d’échelle de passerelle, consultez la [FAQ](virtual-wan-faq.md#for-user-vpn-point-to-site--how-many-clients-are-supported).
   * **Configuration de VPN utilisateur** : sélectionnez la configuration que vous avez créée plus tôt.
   * **Pool d’adresses client** : spécifiez le pool d’adresses client à partir duquel les clients VPN se verront attribuer des adresses IP. Ce paramètre correspond aux unités d’échelle de la passerelle que vous 
1. Cliquez sur **Confirmer**. La mise à jour du hub peut prendre jusqu’à 30 minutes.

## <a name="connect-vnet-to-hub"></a><a name="connect-vnet"></a>Connecter un réseau virtuel à un hub

Dans cette section, vous créez une connexion entre votre hub virtuel et votre réseau virtuel.

[!INCLUDE [Connect virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-user-vpn-profile"></a><a name="device"></a>Télécharger un profil VPN d’utilisateur

Tous les paramètres de configuration nécessaires aux clients VPN sont contenus dans un fichier config zip de client VPN. Les paramètres dans le fichier zip vous aident à configurer facilement les clients VPN. Les fichiers de configuration du client VPN que vous générez sont spécifiques à la configuration VPN utilisateur de votre passerelle. Dans cette section, vous allez générer et télécharger les fichiers utilisés pour configurer vos clients VPN.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-user-vpn-clients"></a>Configurer les clients VPN utilisateurs

Un client doit être installé sur chaque ordinateur qui se connecte. Vous configurez chaque client à l’aide des fichiers de profil client d’utilisateur VPN que vous avez téléchargés lors des étapes précédentes. Utilisez l’article qui se rapporte au système d’exploitation que vous souhaitez connecter.

### <a name="to-configure-macos-vpn-clients-preview"></a>Pour configurer des clients VPN macOS (préversion)

Pour obtenir des instructions pour les clients **macOS**, consultez [Configurer un client VPN - macOS (préversion)](openvpn-azure-ad-client-mac.md).

### <a name="to-configure-windows-vpn-clients"></a>Pour configurer des clients VPN Windows

[!INCLUDE [Download Azure VPN client](../../includes/vpn-gateway-download-vpn-client.md)]

#### <a name="to-import-a-vpn-client-profile-windows"></a><a name="import"></a>Pour importer un profil client VPN (Windows)

1. Dans la page, sélectionnez **Importer**.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/import/import-1.png" alt-text="Capture d’écran de la page d’importation.":::

1. Accédez au fichier XML de profil et sélectionnez-le. Une fois le fichier sélectionné, sélectionnez **Ouvrir**.

    ![Capture d’écran montrant une boîte de dialogue Ouvrir dans laquelle vous pouvez sélectionner un fichier.](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

1. Spécifiez le nom du profil et sélectionnez **Enregistrer**.

    ![Capture d’écran montrant le nom de la connexion ajouté et le bouton Enregistrer sélectionné.](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

1. Sélectionnez **Se connecter** pour vous connecter au VPN.

    ![Capture d’écran montrant le bouton Se connecter pour la connexion que vous venez de créer.](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

1. Une fois que vous êtes connecté, l’icône devient verte et indique **Connecté**.

    ![Capture d’écran montrant la connexion dans un état Connecté avec l’option de déconnexion.](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile---windows"></a><a name="delete"></a>Pour supprimer un profil client - Windows

1. Sélectionnez le bouton de sélection (...) en regard du profil de client à supprimer. Sélectionnez ensuite **Supprimer**.

    ![Capture d’écran montrant l’option Supprimer sélectionnée dans le menu.](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

1. Sélectionnez **Supprimer** pour effectuer la suppression.

    ![Capture d’écran représentant une boîte de dialogue de confirmation avec l’option Supprimer ou Annuler.](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues---windows"></a><a name="diagnose"></a>Diagnostiquer les problèmes de connexion - Windows

1. Pour diagnostiquer les problèmes de connexion, vous pouvez utiliser l’outil **Diagnostiquer**. Sélectionnez le bouton de sélection (...) en regard de la connexion VPN à diagnostiquer pour afficher le menu. Sélectionnez ensuite **Diagnostiquer**.

    ![Capture d’écran montrant l’option de diagnostic sélectionnée dans le menu.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

1. Dans la page **Propriétés de connexion**, sélectionnez **Exécuter le diagnostic**.

    ![Capture d’écran montre le bouton d’exécution du diagnostic pour une connexion.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

1. Connectez-vous avec vos informations d’identification.

    ![Capture d’écran montrant la boîte de dialogue de connexion pour cette action.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

1. Examinez les résultats du diagnostic.

    ![Capture d’écran affichant les résultats du diagnostic.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Afficher votre WAN virtuel

1. Accédez au WAN virtuel.
2. Dans la page Vue d’ensemble, chaque point sur la carte représente un hub.
3. Dans la section des hubs et des connexions, vous pouvez voir l’état du hub, le site, la région, l’état de la connexion VPN et les octets entrés et sortis.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Supprimer des ressources

Quand vous n’avez plus besoin des ressources que vous avez créées, supprimez-les. Certaines des ressources Virtual WAN doivent être supprimées dans un certain ordre en raison des dépendances. La suppression peut prendre environ 30 minutes.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Virtual WAN, consultez la page [Vue d’ensemble de Virtual WAN](virtual-wan-about.md).
