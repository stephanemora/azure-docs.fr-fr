---
title: Créer une passerelle VPN basée sur des itinéraires - Portail Azure | Microsoft Docs
description: Créer une passerelle VPN basée sur des itinéraires à l’aide du portail Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/18/2018
ms.author: cherylmc
ms.openlocfilehash: 7139b2de79b4e092ca761a4e51061c233e6031b5
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470300"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Créer une passerelle VPN basée sur des itinéraires à l’aide du portail Azure

Cet article vous aidera à créer rapidement une passerelle VPN basée sur des itinéraires à l’aide du portail Azure.  Une passerelle VPN est utilisée lors de la création d’une connexion VPN à votre réseau local. Vous pouvez également vous en servir pour connecter des réseaux virtuels. 

Les étapes fournies dans cet article permettent de créer un réseau virtuel, un sous-réseau, un sous-réseau de passerelle et une passerelle VPN basée sur des itinéraires (passerelle de réseau virtuel). Une fois la passerelle créée, vous pourrez créer des connexions. Ces étapes nécessitent un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="vnet"></a>Créer un réseau virtuel

1. Dans un navigateur, accédez au [portail Azure](http://portal.azure.com) et connectez-vous avec votre compte Azure.
2. Cliquez sur **Créer une ressource**. Dans le champ **Rechercher dans le marketplace**, tapez « réseau virtuel ». Localisez **Réseau virtuel** dans la liste renvoyée et cliquez sur cet élément pour ouvrir la page **Réseau virtuel**.
3. Dans la liste déroulante **Sélectionner un modèle de déploiement** en bas de la page Réseau virtuel, vérifiez que **Resource Manager** est sélectionné, puis cliquez sur **Créer**. La page **Créer un réseau virtuel** s’ouvre.
4. Sur la page **Créer un réseau virtuel**, configurez les paramètres du réseau virtuel. Lorsque vous renseignez les champs, le point d’exclamation rouge se transforme en coche verte si les caractères saisis dans le champ sont valides. Utilisez les valeurs suivantes :

  - **Nom** : TestVNet1
  - **Espace d’adressage** : 10.1.0.0/16
  - **Abonnement** : vérifiez que l’abonnement indiqué est celui que vous souhaitez utiliser. Vous pouvez modifier des abonnements à l’aide de la liste déroulante.
  - **Groupe de ressources :** TestRG1
  - **Emplacement** : USA Est
  - **Sous-réseau** : Serveur frontal
  - **Plage d’adresses** : 10.1.0.0/24

  ![Page Créer un réseau virtuel](./media/create-routebased-vpn-gateway-portal/create-virtual-network.png "Page Créer un réseau virtuel")
5. Après avoir entré les valeurs, sélectionnez **Épingler au tableau de bord** pour trouver facilement votre réseau virtuel sur le tableau de bord, puis cliquez sur **Créer**. Une fois que vous avez cliqué sur **Créer**, une vignette apparaît sur votre tableau de bord. Celle-ci indique la progression de la création de votre réseau virtuel. La vignette change lorsque le réseau virtuel est créé.

## <a name="gwsubnet"></a>Ajouter un sous-réseau de passerelle

Le sous-réseau de passerelle contient les adresses IP réservées utilisées par les services de passerelle de réseau virtuel. Créez un sous-réseau de passerelle.

1. Dans le portail, accédez au réseau virtuel pour lequel vous souhaitez créer une passerelle de réseau virtuel.
2. Dans la page de votre réseau virtuel, cliquez sur **Sous-réseaux** pour développer la page **VNet1 - Sous-réseaux**.
3. Cliquez sur **+ Sous-réseau de passerelle** en haut de la page pour ouvrir la page **Ajouter un sous-réseau**.

  ![Ajouter un sous-réseau de passerelle](./media/create-routebased-vpn-gateway-portal/gateway-subnet.png "Ajouter un sous-réseau de passerelle")
4. Le **nom** de votre sous-réseau est automatiquement renseigné avec la valeur requise « GatewaySubnet ». Ajustez les valeurs de **plage d’adresses** renseignées automatiquement pour qu’elles correspondent aux valeurs suivantes :

  **Plage d’adresses (bloc CIDR)** : 10.1.255.0/27

  ![Ajouter un sous-réseau de passerelle](./media/create-routebased-vpn-gateway-portal/add-gateway-subnet.png "Ajouter un sous-réseau de passerelle")
5. Pour créer le sous-réseau de passerelle, cliquez sur **OK** en bas de la page.

## <a name="gwvalues"></a>Configurer les paramètres de la passerelle

1. Sur le côté gauche de la page du portail, cliquez sur **+ Créer une ressource**, saisissez « passerelle de réseau virtuel » dans la zone de recherche, puis appuyez sur **Entrée**. Dans **Résultats**, recherchez et cliquez sur **Passerelle de réseau virtuel**.
2. En bas de la page Passerelle de réseau virtuel, cliquez sur **Créer** pour ouvrir la page **Créer une passerelle de réseau virtuel**.
3. Dans la page **Créer une passerelle réseau virtuel**, renseignez les valeurs pour votre passerelle de réseau virtuel.

  - **Nom** : Vnet1GW
  - **Type de passerelle** : VPN 
  - **Type de VPN** : Basé sur itinéraires
  - **Référence (SKU)** : VpnGw1
  - **Emplacement** : USA Est
  - **Réseau virtuel** : cliquez sur **Réseau virtuel/Choisir un réseau virtuel** pour ouvrir la page **Choisir un réseau virtuel**. Sélectionnez **VNet1**.
  - **Adresse IP publique** : ce paramètre spécifie l’objet d’adresse IP publique qui est associé à la passerelle VPN. L’adresse IP publique est attribuée dynamiquement à cet objet pendant la création de la passerelle VPN. Actuellement, la passerelle VPN prend uniquement en charge l’allocation d’adresses IP publiques *dynamiques*. Toutefois, cela ne signifie pas que l’adresse IP change après son affectation à votre passerelle VPN. L’adresse IP publique change uniquement lorsque la passerelle est supprimée, puis recréée. Elle n’est pas modifiée lors du redimensionnement, de la réinitialisation ou des autres opérations de maintenance/mise à niveau internes de votre passerelle VPN.

    - Laissez l’option **Créer** sélectionnée.
    - Dans la zone de texte, donnez un **nom** à votre adresse IP publique. Pour cet exercice, utilisez **VNet1GWIP**.<br>

    ![Configuration des paramètres de la passerelle](./media/create-routebased-vpn-gateway-portal/gw.png "Configuration des paramètres de la passerelle")

## <a name="creategw"></a>Créer la passerelle VPN

1. Vérifiez les paramètres dans la page **Créer une passerelle de réseau virtuel**. Ajustez les valeurs si nécessaire.
2. Cliquez sur **Créer** en bas de la page.

  Une fois que vous avez cliqué sur **Créer**, les paramètres sont validés et la vignette **Déploiement d’une passerelle de réseau virtuel** s’affiche sur le tableau de bord. La création d’une passerelle VPN peut prendre jusqu’à 45 minutes. Vous devrez peut-être actualiser la page du portail pour que l’état terminé apparaisse.

## <a name="viewgw"></a>Afficher la passerelle VPN

1. Une fois la passerelle créée, accédez à VNet1 dans le portail. La passerelle VPN apparaît dans la page Vue d’ensemble en tant qu’appareil connecté.

  ![Appareils connectés](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Appareils connectés")

2. Dans la liste des appareils, cliquez sur **VNet1GW** pour afficher des informations supplémentaires.

  ![Affichage de la passerelle VPN](./media/create-routebased-vpn-gateway-portal/view-gateway.png "Affichage de la passerelle VPN")

## <a name="next-steps"></a>Étapes suivantes

Une fois la création de la passerelle terminée, vous pouvez établir une connexion entre votre réseau virtuel et un autre réseau virtuel. Sinon, créez une connexion entre votre réseau virtuel et un emplacement local.

> [!div class="nextstepaction"]
> [Créer une connexion de site à site](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Créer une connexion de point à site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Créer une connexion à un autre réseau virtuel](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
