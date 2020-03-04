---
title: Fichier include
description: Fichier include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 872ba86c9e43b1f6642331908eb829605f6c19bd
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619724"
---
Vous pouvez créer un réseau virtuel avec le modèle de déploiement Resource Manager et le portail Azure en suivant ces étapes. Pour plus d’informations sur les réseaux virtuels, consultez [Présentation du réseau virtuel](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Afin que ce réseau virtuel puisse se connecter à un emplacement local, prenez contact avec votre administrateur de réseau local pour définir une plage d’adresses IP à utiliser spécifiquement pour ce réseau virtuel. Si une plage d’adresses en double existe des deux côtés de la connexion VPN, le trafic sera acheminé de manière inattendue. En outre, si vous souhaitez connecter ce réseau virtuel à un autre réseau virtuel, l’espace d’adressage ne peut pas se chevaucher avec un autre réseau virtuel. Planifiez votre configuration réseau en conséquence.
>
>

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans **Rechercher dans les ressources, services et documents (G+/)** , tapez *réseau virtuel*.

   ![Localiser la page de ressource Réseau virtuel](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Localiser la page de ressource de réseau virtuel")
1. Sélectionnez **Réseau virtuel** dans les résultats sous **Place de marché**.

   ![Sélectionner un réseau virtuel](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Localiser la page de ressource de réseau virtuel")
1. Dans la page **Réseau virtuel**, cliquez sur **Créer**.

   ![Page Réseau virtuel](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Cliquez sur Create.")
1. Une fois que vous avez cliqué sur Créer, la page **Créer un réseau virtuel** s’ouvre.

   ![Créer une page Réseau virtuel](./media/vpn-gateway-basic-vnet-rm-portal-include/create-virtual-network-page.png "Créer une page Réseau virtuel")
1. Sur la page **Créer un réseau virtuel**, configurez les paramètres du réseau virtuel. Quand vous renseignez les champs, le point d’exclamation rouge se transforme en coche verte si les caractères saisis dans le champ sont validés. Certaines valeurs sont renseignées automatiquement, et vous pouvez les remplacer par vos propres valeurs :

   - **Name** : entrez le nom du réseau virtuel.
   - **Espace d’adressage** : entrez l’espace d’adressage. Si vous avez plusieurs espaces d’adressage à ajouter, entrez le premier espace d’adressage ici. Vous pouvez ajouter des espaces d’adressage supplémentaires plus tard, après avoir créé le réseau virtuel. Si votre configuration nécessite un espace d’adressage IPv6, cochez la case pour entrer cette information.
   - **Abonnement**: vérifiez que l’abonnement listé est approprié. Vous pouvez modifier des abonnements à l’aide de la liste déroulante.
   - **Groupe de ressources** : sélectionnez un groupe de ressources existant ou créez-en un nouveau en entrant un nom pour ce dernier. Si vous créez un groupe, nommez-le en fonction de vos valeurs de configuration planifiée. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Emplacement** : sélectionnez l’emplacement du réseau virtuel. L’emplacement détermine où se trouveront les ressources que vous déployez sur ce réseau virtuel.
   - **Sous-réseau** : ajoutez le **Nom** et la **Plage d’adresses** du sous-réseau. Vous pouvez ajouter des sous-réseaux supplémentaires plus tard, après avoir créé le réseau virtuel.
   - **Protection DDoS** : sélectionnez **De base**, sauf si vous souhaitez utiliser le service Standard.
   - **Points de terminaison de service** : vous pouvez conserver la valeur **Désactivé** pour ce paramètre, sauf si votre configuration spécifie ce paramètre.
   - **Pare-feu** : vous pouvez conserver la valeur **Désactivé** pour ce paramètre, sauf si votre configuration spécifie ce paramètre.
1. Cliquez sur **Créer** pour commencer le déploiement du réseau virtuel.
