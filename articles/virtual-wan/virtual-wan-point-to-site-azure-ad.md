---
title: Configurer l’authentification Azure AD pour une connexion point à site à Azure | Microsoft Docs
description: Dans ce tutoriel, découvrez comment configurer l’authentification Azure Active Directory pour une connexion VPN utilisateur.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: alzam
ms.openlocfilehash: b3508c4c8da5b4987fb5f38cf3bf701f2dda1097
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122033"
---
# <a name="tutorial-create-a-user-vpn-connection-by-using-azure-virtual-wan"></a>Tutoriel : Créer une connexion VPN utilisateur avec Azure Virtual WAN

Ce tutoriel vous montre comment configurer l’authentification Azure AD pour un VPN utilisateur dans Virtual WAN afin de vous connecter à vos ressources dans Azure par le biais d’une connexion VPN OpenVPN. L’authentification Azure Active Directory est disponible uniquement pour les passerelles basées sur le protocole OpenVPN et les clients Windows.

Pour utiliser ce type de connexion, un client doit être configuré sur l’ordinateur client. Pour plus d’informations sur Azure Virtual WAN, consultez l’article [Vue d’ensemble d’Azure Virtual WAN](virtual-wan-about.md).

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
> * Afficher l’intégrité des ressources

![Diagramme WAN virtuel](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration :

* Vous avez un réseau virtuel auquel vous souhaitez vous connecter. Vérifiez qu’aucun des sous-réseaux de votre réseau local ne chevauche les réseaux virtuels auxquels vous souhaitez vous connecter. Pour créer un réseau virtuel dans le portail Azure, consultez le [guide de démarrage rapide](../virtual-network/quick-create-portal.md).

* Votre réseau virtuel n’a pas de passerelle de réseau virtuel. Si votre réseau virtuel dispose d’une passerelle (VPN ou ExpressRoute), vous devez supprimer toutes les passerelles. Cette configuration nécessite que les réseaux virtuels soient connectés à la passerelle hub Virtual WAN.

* Obtenez une plage d’adresses IP pour la région de votre hub. Le hub est un réseau virtuel qui est créé et utilisé par Virtual WAN. La plage d’adresses que vous spécifiez pour le hub ne peut pas chevaucher les réseaux virtuels existants auxquels vous vous connectez. Elle ne peut pas non plus chevaucher vos plages d’adresses auxquelles vous vous connectez en local. Si vous ne maîtrisez pas les plages d’adresses IP situées dans votre configuration de réseau local, contactez une personne en mesure de vous aider.

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="wan"></a>Créer un WAN virtuel

Dans un navigateur, accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

1. Accédez à la page WAN Virtuel. Dans le portail, cliquez sur **+Créer une ressource**. Tapez **WAN virtuel** dans la zone de recherche et sélectionnez Entrée.
2. Sélectionnez **WAN virtuel** dans les résultats. Dans la page Virtual WAN, cliquez sur **Créer** pour ouvrir la page Créer un WAN.
3. Dans la page **Créer un WAN**, sous l’onglet **Fonctions de base**, renseignez les champs suivants :

   ![WAN virtuel](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Abonnement** : sélectionnez l’abonnement que vous voulez utiliser.
   * **Groupe de ressources** : créez-en un ou utilisez-en un qui existe déjà.
   * **Emplacement du groupe de ressources** : choisissez un emplacement de la ressource dans la liste déroulante. Un WAN est une ressource globale et ne réside pas dans une région particulière. Toutefois, vous devez sélectionner une région pour gérer et localiser plus facilement la ressource WAN que vous créez.
   * **Nom** : tapez le nom que vous souhaitez donner à votre WAN.
   * **Type :** Standard. Si vous créez un WAN de base, vous ne pourrez créer qu’un hub de base. Les hubs de base offrent uniquement une connectivité de site VPN à site.
4. Quand vous avez fini de renseigner les champs, cliquez sur **Vérifier + Créer**.
5. Une fois la validation effectuée, sélectionnez **Créer** pour créer le WAN virtuel.

## <a name="site"></a>Créer un hub virtuel vide

1. Sous votre WAN virtuel, sélectionnez Hubs, puis cliquez sur **+ Nouveau hub**.

   ![nouveau site](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. Dans la page Créer un hub virtuel, renseignez les champs suivants.

   **Région** : sélectionnez la région dans laquelle vous souhaitez déployer le hub virtuel.

   **Nom** : entrez le nom que vous souhaitez donner à votre hub virtuel.

   **Espace d’adressage privé du hub** : plage d’adresses du hub en notation CIDR.

   ![nouveau site](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Cliquez sur **Vérifier + créer**.
4. Dans la page **Validation effectuée**, cliquez sur **Créer**.

## <a name="site"></a>Créer une configuration P2S

Une configuration P2S définit les paramètres permettant de connecter des clients distants.

1. Sous votre WAN virtuel, sélectionnez **Configurations de VPN utilisateur**.

   ![nouvelle configuration](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. Cliquez sur **+ Créer une configuration de VPN utilisateur**.

   ![nouvelle configuration](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. Entrez les informations et cliquez sur **Créer**

   ![nouvelle configuration](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="hub"></a>Modifier l’affectation du hub

1. Accédez au panneau **Hubs** situé sous le WAN virtuel.
2. Sélectionnez le hub à associer à la configuration du serveur VPN, puis cliquez sur le bouton de sélection (...).

   ![nouveau site](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Cliquez sur **Modifier le hub virtuel**.
4. Cochez la case **Inclure la passerelle point à site** et sélectionnez l’**unité d’échelle de passerelle** souhaitée.

   ![nouveau site](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Entrez le **Pool d’adresses** à partir duquel les clients VPN se verront attribuer des adresses IP.
6. Cliquez sur **Confirmer**.
7. L’opération peut prendre jusqu’à 30 minutes.

## <a name="device"></a>Télécharger le profil VPN

Utilisez le profil VPN pour configurer vos clients.

1. Dans la page de votre WAN virtuel, cliquez sur **Configurations de VPN utilisateur**.
2. En haut de la page, cliquez sur **Télécharger la configuration de VPN utilisateur**.
3. Une fois que le fichier a terminé la création, vous pouvez cliquer sur le lien pour le télécharger.
4. Utilisez le fichier de profil pour configurer les clients VPN.

## <a name="configure-user-vpn-clients"></a>Configurer les clients VPN utilisateur

Pour établir une connexion, vous devez télécharger Azure VPN Client et importer le profil de client VPN téléchargé au cours des étapes précédentes sur chaque ordinateur à connecter au réseau virtuel.

> [!NOTE]
> L’authentification Azure AD est prise en charge uniquement pour les connexions de protocole OpenVPN®.
>

#### <a name="to-download-the-azure-vpn-client"></a>Pour télécharger le client VPN Azure

Utilisez ce [lien](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) pour télécharger Azure VPN Client.

#### <a name="import"></a>Pour importer un profil client

1. Dans la page, sélectionnez **Importer**.

    ![importer](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Accédez au fichier XML de profil et sélectionnez-le. Une fois le fichier sélectionné, sélectionnez **Ouvrir**.

    ![importer](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Spécifiez le nom du profil et sélectionnez **Enregistrer**.

    ![importer](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Sélectionnez **Se connecter** pour vous connecter au VPN.

    ![importer](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Une fois que vous êtes connecté, l’icône devient verte et indique **Connecté**.

    ![importer](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>Pour supprimer un profil client

1. Sélectionnez le bouton de sélection (...) en regard du profil de client à supprimer. Sélectionnez ensuite **Supprimer**.

    ![supprimer](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Sélectionnez **Supprimer** pour effectuer la suppression.

    ![supprimer](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Diagnostiquer les problèmes de connexion

1. Pour diagnostiquer les problèmes de connexion, vous pouvez utiliser l’outil **Diagnostiquer**. Sélectionnez le bouton de sélection (...) en regard de la connexion VPN à diagnostiquer pour afficher le menu. Sélectionnez ensuite **Diagnostiquer**.

    ![diagnostiquer](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Dans la page **Propriétés de connexion**, sélectionnez **Exécuter le diagnostic**.

    ![diagnostiquer](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Connectez-vous avec vos informations d’identification.

    ![diagnostiquer](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Examinez les résultats du diagnostic.

    ![diagnostiquer](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>Afficher votre WAN virtuel

1. Accédez au WAN virtuel.
2. Dans la page Vue d’ensemble, chaque point sur la carte représente un hub. Pointez sur n’importe quel point pour voir le résumé de l’intégrité du hub.
3. Dans la section des hubs et des connexions, vous pouvez voir l’état du hub, le site, la région, l’état de la connexion VPN et les octets entrés et sortis.

## <a name="viewhealth"></a>Afficher l’intégrité des ressources

1. Accédez à votre réseau étendu.
2. Dans la page de votre WAN, dans la section **SUPPORT + Dépannage**, cliquez sur **Intégrité** pour voir votre ressource.


## <a name="cleanup"></a>Supprimer des ressources

Quand vous n’avez plus besoin de ces ressources, vous pouvez utiliser [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources et toutes les ressources qu’il contient. Remplacez « myResourceGroup » par le nom de votre groupe de ressources et exécutez la commande PowerShell suivante :

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Virtual WAN, consultez la page [Vue d’ensemble de Virtual WAN](virtual-wan-about.md).
