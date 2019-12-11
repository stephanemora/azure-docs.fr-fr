---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5bf93980a8be86c77240ab981eb812a738a96204
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828902"
---
Vous pouvez créer un réseau virtuel avec le modèle de déploiement Resource Manager et le portail Azure en suivant ces étapes. Pour plus d’informations sur les réseaux virtuels, consultez [Présentation du réseau virtuel](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Afin que ce réseau virtuel puisse se connecter à un emplacement local, prenez contact avec votre administrateur de réseau local pour définir une plage d’adresses IP à utiliser spécifiquement pour ce réseau virtuel. Si une plage d’adresses en double existe des deux côtés de la connexion VPN, le trafic sera acheminé de manière inattendue. En outre, si vous souhaitez connecter ce réseau virtuel à un autre réseau virtuel, l’espace d’adressage ne peut pas se chevaucher avec un autre réseau virtuel. Planifiez votre configuration réseau en conséquence.
>
>

1. Connectez-vous au [Portail Azure](https://portal.azure.com).  Dans le menu du portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**. La page **Nouveau** s’ouvre.

2. Dans **Rechercher dans la Place de marché**, entrez *réseau virtuel*, puis sélectionnez **Réseau virtuel** dans les résultats.

   ![Localiser la page de ressource Réseau virtuel](./media/vpn-gateway-basic-vnet-rm-portal-include/search-marketplace-for-virtual-network.png "Localiser la page de ressource de réseau virtuel")

   La page **Réseau virtuel** s’ouvre.

3. Dans la liste **Sélectionner un modèle de déploiement** située près du bas de la page, sélectionnez **Resource Manager**, puis **Créer**. La page **Créer un réseau virtuel** s’ouvre.

   ![Créer une page Réseau virtuel](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "Créer une page Réseau virtuel")

4. Sur la page **Créer un réseau virtuel**, configurez les paramètres du réseau virtuel. Quand vous renseignez les champs, le point d’exclamation rouge se transforme en coche verte si les caractères saisis dans le champ sont validés. Certaines valeurs sont renseignées automatiquement, et vous pouvez les remplacer par vos propres valeurs :

   - **Nom** : entrez le nom du réseau virtuel.

   - **Espace d’adressage** : entrez l’espace d’adressage. Si vous avez plusieurs espaces d’adressage à ajouter, entrez le premier espace d’adressage ici. Vous pouvez ajouter des espaces d’adressage supplémentaires plus tard, après avoir créé le réseau virtuel.

   - **Abonnement**: vérifiez que l’abonnement listé est approprié. Vous pouvez modifier des abonnements à l’aide de la liste déroulante.

   - **Groupe de ressources** : sélectionnez un groupe de ressources existant ou créez-en un nouveau en entrant un nom pour ce dernier. Si vous créez un groupe, nommez-le en fonction de vos valeurs de configuration planifiée. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).

   - **Emplacement** : sélectionnez l’emplacement du réseau virtuel. L’emplacement détermine où se trouveront les ressources que vous déployez sur ce réseau virtuel.

   - **Sous-réseau** : ajoutez le **Nom** et la **Plage d’adresses** du sous-réseau. Vous pouvez ajouter des sous-réseaux supplémentaires plus tard, après avoir créé le réseau virtuel. 
     
5. Sélectionnez **Create** (Créer).
