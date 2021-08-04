---
title: Fichier include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/30/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 82db4939b6f980e1d9fb9a82a6cd255bb10c04cb
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113126347"
---
1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans **Rechercher dans les ressources, services et documents (G+/)** , tapez *réseau virtuel*.

   :::image type="content" source="./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png" alt-text="Capture d’écran montrant la barre de recherche du portail Azure." border="false":::
1. Sélectionnez **Réseau virtuel** dans les résultats sous **Place de marché**.

   :::image type="content" source="./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png" alt-text="Capture d’écran montrant les résultats de la barre de recherche du portail Azure et la sélection de Réseau virtuel à partir de la Place de marché." border="false":::
1. Dans la page **Réseau virtuel**, sélectionnez **Créer**.

   :::image type="content" source="./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png" alt-text="Capture d’écran montrant la page Réseau virtuel et la sélection du bouton Créer." border="false":::
1. Quand vous sélectionnez **Créer**, la page **Créer un réseau virtuel** s’ouvre.
1. Sous l’onglet **Informations de base**, configurez les paramètres de réseau virtuel **Détails du projet** et **Détails de l’instance**.

   :::image type="content" source="./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png" alt-text="Capture d’écran montrant l’onglet Informations de base." border="false":::

   Quand vous renseignez les champs, une coche verte indique que les caractères entrés sont validés. Certaines valeurs sont renseignées automatiquement, et vous pouvez les remplacer par vos propres valeurs :

   - **Abonnement**: vérifiez que l’abonnement listé est approprié. Vous pouvez modifier des abonnements à l’aide de la liste déroulante.
   - **Groupe de ressources** : sélectionnez un groupe de ressources existant ou cliquez sur **Créer** pour en créer un. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Name** : entrez le nom du réseau virtuel.
   - **Région** : sélectionnez l’emplacement du réseau virtuel. L’emplacement détermine où se trouveront les ressources que vous déployez sur ce réseau virtuel.

1. Configurez les valeurs de l’onglet **Adresses IP**. Les valeurs indiquées dans les exemples ci-dessous sont présentées à des fins de démonstration. Définissez ces valeurs selon les paramètres dont vous avez besoin.

   :::image type="content" source="./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png" alt-text="Capture d’écran montrant l’onglet Adresses IP." border="false"::: 
   - **Espace d’adressage IPv4** : Un espace d’adressage est créé automatiquement par défaut. Vous pouvez cliquer sur l’espace d’adressage pour le définir selon vos propres valeurs. Vous pouvez également ajouter des espaces d’adressage.
   - **Sous-réseau** : Si vous utilisez l’espace d’adressage par défaut, un sous-réseau par défaut est créé automatiquement. Si vous modifiez l’espace d’adressage, vous devez ajouter un sous-réseau. Sélectionnez **+ Ajouter un sous-réseau** pour ouvrir la fenêtre **Ajouter un sous-réseau**. Configurez les paramètres suivants, puis sélectionnez **Ajouter** pour ajouter les valeurs :
      - **Nom du sous-réseau** : dans cet exemple, nous avons nommé le sous-réseau « FrontEnd ».
      - **Plage d’adresses de sous-réseau** : plage d’adresses de ce sous-réseau.

1. Dans l’onglet **Sécurité**, conservez les valeurs par défaut pour le moment :

   - **Protection DDoS** : Désactivée
   - **Pare-feu** : Désactivé
1. Sélectionnez **Vérifier + créer** pour vérifier les paramètres de réseau virtuel.
1. Une fois les paramètres vérifiés, sélectionnez **Créer**.
