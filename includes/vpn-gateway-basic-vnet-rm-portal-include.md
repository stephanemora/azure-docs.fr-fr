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
ms.openlocfilehash: 835f23f98ebe56e0b19081f07dc3302ef93b27b9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66171597"
---
Vous pouvez créer un réseau virtuel avec le modèle de déploiement Resource Manager et le portail Azure en suivant ces étapes. Pour plus d’informations sur les réseaux virtuels, consultez [Présentation du réseau virtuel](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Afin que ce réseau virtuel puisse se connecter à un emplacement local, prenez contact avec votre administrateur de réseau local pour définir une plage d’adresses IP à utiliser spécifiquement pour ce réseau virtuel. Si une plage d’adresses en double existe des deux côtés de la connexion VPN, le trafic sera acheminé de manière inattendue. En outre, si vous souhaitez connecter ce réseau virtuel à un autre réseau virtuel, l’espace d’adressage ne peut pas se chevaucher avec un autre réseau virtuel. Planifiez votre configuration réseau en conséquence.
>
>

1. Connectez-vous au [portail Azure](http://portal.azure.com) et sélectionnez **Créer une ressource**. La page **Nouveau** s’ouvre.

2. Dans le champ **Rechercher dans la Place de marché**, entrez *réseau virtuel* et sélectionnez **Réseau virtuel** dans la liste retournée. La page **Réseau virtuel** s’ouvre.

   ![Page Localiser les ressources du réseau virtuel](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Page Localiser les ressources du réseau virtuel")

3. Dans la liste **Sélectionner un modèle de déploiement** située près du bas de la page, sélectionnez **Resource Manager**, puis **Créer**. La page **Créer un réseau virtuel** s’ouvre.

   ![Page Créer un réseau virtuel](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "Page Créer un réseau virtuel")

4. Sur la page **Créer un réseau virtuel**, configurez les paramètres du réseau virtuel. Quand vous renseignez les champs, le point d’exclamation rouge se transforme en coche verte si les caractères saisis dans le champ sont validés. Certaines valeurs sont renseignées automatiquement, et vous pouvez les remplacer par vos propres valeurs :

   - **Nom** : entrez le nom du réseau virtuel.

   - **Espace d’adressage** : entrez l’espace d’adressage. Si vous avez plusieurs espaces d’adressage à ajouter, entrez le premier espace d’adressage ici. Vous pouvez ajouter des espaces d’adressage supplémentaires plus tard, après avoir créé le réseau virtuel.

   - **Abonnement**: vérifiez que l’abonnement listé est approprié. Vous pouvez modifier des abonnements à l’aide de la liste déroulante.

   - **Groupe de ressources** : sélectionnez un groupe de ressources existant ou créez-en un nouveau en entrant un nom pour ce dernier. Si vous créez un groupe, nommez-le en fonction de vos valeurs de configuration planifiée. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).

   - **Emplacement** : sélectionnez l’emplacement du réseau virtuel. L’emplacement détermine où se trouveront les ressources que vous déployez sur ce réseau virtuel.

   - **Sous-réseau** : ajoutez le **Nom** et la **Plage d’adresses** du sous-réseau. Vous pouvez ajouter des sous-réseaux supplémentaires plus tard, après avoir créé le réseau virtuel. 
     
5. Sélectionnez **Créer**.
