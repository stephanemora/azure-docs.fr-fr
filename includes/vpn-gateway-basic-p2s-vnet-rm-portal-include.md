---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5975f334eae543ea0f6ddc182170ae185ac5397a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468004"
---
Pour créer un réseau virtuel dans le modèle de déploiement Resource Manager à l’aide du portail Azure, suivez les étapes ci-dessous. Les captures d’écran sont fournies à titre d’exemple. Assurez-vous de remplacer ces valeurs par les vôtres. Pour plus d’informations sur l’utilisation des réseaux virtuels, voir [Présentation du réseau virtuel](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Si vous souhaitez que ce réseau virtuel se connecte à un emplacement local (en plus de créer une configuration P2S), vous devez prendre contact avec votre administrateur de réseau local pour définir une plage d’adresses IP à utiliser spécifiquement pour ce réseau virtuel. Si une plage d’adresses en double existe des deux côtés de la connexion VPN, le trafic ne sera pas correctement acheminé. En outre, si vous souhaitez connecter ce réseau virtuel à un autre réseau virtuel, l’espace d’adressage ne peut pas se chevaucher avec un autre réseau virtuel. Veillez à planifier votre configuration réseau en conséquence.
>
>

1. Connectez-vous au [portail Azure](https://portal.azure.com).  Dans le menu du portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**. La page **Nouveau** s’ouvre.

2. Dans **Rechercher dans la Place de marché**, entrez *réseau virtuel*, puis sélectionnez **Réseau virtuel** dans les résultats.

   ![Localiser la page de ressource Réseau virtuel](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/search-marketplace-for-virtual-network.png "Localiser la page de ressource de réseau virtuel")

3. En bas de la page Réseau virtuel, à partir de la liste **Sélectionner un modèle de déploiement**, choisissez **Gestionnaire des ressources** puis cliquez sur **Créer**.

   ![Sélectionner Resource Manager](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/resourcemanager250.png "Sélectionner Resource Manager")
4. Sur la page **Créer un réseau virtuel**, configurez les paramètres du réseau virtuel. Lorsque vous renseignez les champs, le point d’exclamation rouge se transforme en coche verte si les caractères saisis dans le champ sont valides. Il se peut que certaines valeurs soient renseignées automatiquement. Dans ce cas, remplacez ces valeurs par les vôtres. La page **Créer un réseau virtuel** ressemble à l’exemple suivant :

   ![Validation de champ](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/vnetp2s.png "Validation du champ")
5. **Name** : Entrez le nom de votre réseau virtuel.
6. **Espace d’adressage** : entrez l’espace d’adressage. Si vous avez plusieurs espaces d’adressage à ajouter, ajoutez le premier espace d’adressage. Vous pourrez ajouter des espaces d’adressage supplémentaires plus tard, après avoir créé le réseau virtuel.
7. **Abonnement**: Vérifiez que l’abonnement listé est correct. Vous pouvez modifier des abonnements à l’aide de la liste déroulante.
8. **Groupe de ressources** : Sélectionnez un groupe de ressources existant, ou créez-en un autre en tapant le nom de ce nouveau groupe de ressources. Si vous créez un groupe, nommez-le en fonction de vos valeurs de configuration planifiée. Pour plus d’informations sur les groupes de ressources, consultez [Présentation d’Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups).
9. **Emplacement** : sélectionnez l’emplacement du réseau virtuel. L’emplacement détermine où se trouveront les ressources que vous déployez sur ce réseau virtuel.
10. **Sous-réseau** : Ajoutez le nom et la plage d’adresses du sous-réseau. Vous pourrez ajouter des sous-réseaux supplémentaires plus tard, après avoir créé le réseau virtuel.
11. Sélectionnez **Épingler au tableau de bord** si vous souhaitez être en mesure de trouver votre réseau virtuel facilement sur le tableau de bord, puis cliquez sur **Créer**.

    ![Épingler au tableau de bord](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/pintodashboard150.png "Épingler au tableau de bord")
12. Après avoir cliqué sur **Créer**, vous verrez une vignette apparaître sur votre tableau de bord. Celle-ci indique la progression de votre réseau virtuel. La vignette change lorsque le réseau virtuel est créé.

    ![Vignette de création du réseau virtuel](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/deploying150.png "Mosaïque de création du réseau virtuel")
