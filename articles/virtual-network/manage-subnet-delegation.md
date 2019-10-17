---
title: Ajouter ou supprimer une délégation de sous-réseau dans un réseau virtuel Azure
titlesuffix: Azure Virtual Network
description: Découvrez comment ajouter ou supprimer un sous-réseau délégué pour un service dans Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2019
ms.author: kumud
ms.openlocfilehash: 5fa340fc3c839d74f292f551b73184ea4df1c0f1
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72175944"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Ajouter ou supprimer une délégation de sous-réseau

La délégation de sous-réseau donne des autorisations explicites au service pour créer des ressources propres au service dans le sous-réseau à l’aide d’un identificateur unique pendant le déploiement du service. Cet article explique comment ajouter un sous-réseau délégué à un service Azure et comment l’en supprimer.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-the-virtual-network"></a>Créer un réseau virtuel

Dans le cadre de cette section, vous allez créer un réseau virtuel et le sous-réseau que vous déléguerez plus tard à un service Azure.

1. Dans le coin supérieur gauche de l’écran, sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.
1. Dans **Créer un réseau virtuel**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez *MyVirtualNetwork*. |
    | Espace d’adressage | Entrez *10.0.0.0/16*. |
    | Subscription | Sélectionnez votre abonnement.|
    | Resource group | Sélectionnez **Créer nouveau**, entrez *myResourceGroup* et sélectionnez **OK**. |
    | Location | Sélectionnez **USA Est**.|
    | Sous-réseau - Nom | Entrez *mySubnet*. |
    | Plage d’adresses du sous-réseau | Entrez *10.0.0.0/24*. |
    |||
1. Conservez les autres valeurs par défaut, puis sélectionnez **Créer**.

## <a name="permissons"></a>Autorisations

Si vous n’avez pas créé le sous-réseau que vous souhaitez déléguer à un service Azure, vous devez disposer de l’autorisation suivante : `Microsoft.Network/virtualNetworks/subnets/write`.

Le rôle intégré [Contributeur réseau](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) inclut également les autorisations nécessaires.

## <a name="delegate-a-subnet-to-an-azure-service"></a>Déléguer un sous-réseau à un service Azure

Dans le cadre de cette section, vous allez déléguer le sous-réseau que vous avez créé à la section précédente à un service Azure.

1. Dans la barre de recherche du portail, entrez *myVirtualNetwork*. Quand la mention **myVirtualNetwork** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans les résultats de recherche, sélectionnez *myVirtualNetwork*.
3. Sélectionnez **Sous-réseaux** sous **PARAMÈTRES**, puis sélectionnez **mySubnet**.
4. Dans la page *mySubnet*, pour la liste **Délégation de sous-réseau**, sélectionnez l’un des services listés sous **Déléguer le sous-réseau à un service** (par exemple, **Microsoft.DBforPostgreSQL/serversv2**).  

## <a name="remove-subnet-delegation-from-an-azure-service"></a>Supprimer une délégation de sous-réseau d’un service Azure

1. Dans la barre de recherche du portail, entrez *myVirtualNetwork*. Quand la mention **myVirtualNetwork** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans les résultats de recherche, sélectionnez *myVirtualNetwork*.
3. Sélectionnez **Sous-réseaux** sous **PARAMÈTRES**, puis sélectionnez **mySubnet**.
4. Dans la page *mySubnet*, pour la liste **Délégation de sous-réseau**, sélectionnez **Aucun** dans les services listés sous **Déléguer le sous-réseau à un service**. 

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [gérer les sous-réseaux dans Azure](virtual-network-manage-subnet.md).
