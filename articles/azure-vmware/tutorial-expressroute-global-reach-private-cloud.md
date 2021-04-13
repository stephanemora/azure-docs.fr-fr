---
title: 'Tutoriel : Appairer des environnements locaux avec un cloud privé'
description: Découvrez comment créer un appairage ExpressRoute Global Reach avec un cloud privé dans une solution Azure VMware Solution.
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 798b822989127ccbb00e971de2cc4147ac234259
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449560"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Tutoriel : Appairer des environnements locaux avec un cloud privé

ExpressRoute Global Reach connecte votre environnement local à votre cloud privé Azure VMware Solution. La connexion Global Reach ExpressRoute est établie entre le circuit ExpressRoute de cloud privé et une connexion ExpressRoute existante à vos environnements locaux. 

Le circuit ExpressRoute que vous utilisez au moment de [configurer un réseau pour votre cloud privé VMware dans Azure](tutorial-configure-networking.md) vous impose de créer et d’utiliser des clés d’autorisation.  Vous aurez déjà utilisé une clé d’autorisation à partir du circuit ExpressRoute et, dans ce tutoriel, vous allez créer une deuxième clé d’autorisation pour le peering avec votre circuit ExpressRoute local.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une seconde clé d’autorisation pour le circuit ExpressRoute de cloud privé (_circuit 2_)
> * Utiliser le portail Azure ou l’interface Azure CLI dans une méthode Cloud Shell dans l’abonnement du _circuit 1_ pour activer le peering d’ExpressRoute Global Reach entre circuit local et circuit de cloud privé


## <a name="before-you-begin"></a>Avant de commencer

Avant d’activer la connectivité entre deux circuits ExpressRoute à l’aide d’ExpressRoute Global Reach, consultez l’article expliquant comment [activer la connectivité dans des abonnements Azure différents](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  

## <a name="prerequisites"></a>Prérequis

- Connectivité établie vers et depuis un cloud privé Azure VMware Solution avec son circuit ExpressRoute appairé avec une passerelle ExpressRoute dans un réseau virtuel Azure, c’est-à-dire le circuit 2 dans le contexte des procédures d’appairage.
- Un circuit ExpressRoute fonctionnel distinct utilisé pour connecter des environnements locaux à Azure, c’est-à-dire le circuit 1 dans le contexte des procédures d’appairage.
- Un [bloc d’adresses réseau](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) /29 sans chevauchement pour l’appairage ExpressRoute Global Reach
- Veillez à ce que toutes les passerelles, y compris le service du fournisseur ExpressRoute, prennent en charge un numéro de système autonome (ASN) à 4 octets. Azure VMware Solution utilise des numéros de système autonome (ASN) publics à 4 octets pour publier les routes.

>[!IMPORTANT]
>Dans le cadre de ces prérequis, votre circuit ExpressRoute local est le _circuit 1_. Quant à votre circuit ExpressRoute de cloud privé, il se trouve dans un abonnement différent et est nommé _circuit 2_.

## <a name="create-an-expressroute-authorization-key-in-the-private-cloud-expressroute-circuit"></a>Créer une clé d’autorisation ExpressRoute dans le circuit ExpressRoute cloud privé

[!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]
 
## <a name="peer-private-cloud-to-on-premises-with-authorization-key"></a>Appairer un cloud privé avec un circuit local avec la clé d’autorisation
Maintenant que vous avez créé une clé d’autorisation pour le circuit ExpressRoute de cloud privé, vous pouvez l’appairer avec votre circuit ExpressRoute local. L’appairage est effectué dans le contexte du circuit ExpressRoute local soit dans le **portail Azure** soit avec l’**interface Azure CLI dans une méthode Cloud Shell**. Avec ces deux méthodes, vous devez utiliser l’ID de ressource et la clé d’autorisation de votre circuit ExpressRoute de cloud privé pour terminer le Peering.

### <a name="portal"></a>[Portail](#tab/azure-portal)
 
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant le même abonnement que le circuit ExpressRoute local.

1. Dans la page **Vue d’ensemble** du cloud privé, sous Gérer, sélectionnez **Connectivité** > **ExpressRoute Global Reach** > **Ajouter**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Capture d’écran montrant l’onglet ExpressRoute Global Reach dans le cloud privé d’Azure VMware Solution":::

1. Créez une connexion cloud locale. Effectuez l’une des opérations suivantes, puis sélectionnez **Créer** :

   - Sélectionnez le **circuit ExpressRoute** dans la liste ou
   - Si vous disposez de l'ID du circuit, collez-le dans le champ et fournissez la clé d'autorisation que vous venez de créer.

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Entrez l’ID ExpressRoute et la clé d’autorisation créés, puis sélectionnez Créer.":::   
   
   La nouvelle connexion apparaît dans la liste des connexions cloud locales.

>[!TIP]
>Vous pouvez supprimer ou déconnecter une connexion de la liste en sélectionnant **Plus**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Déconnecter ou supprimer une connexion locale":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nous avons ajouté à l’[article relatif aux commandes CLI](../expressroute/expressroute-howto-set-global-reach-cli.md) des informations et exemples spécifiques pour vous aider à configurer le peering ExpressRoute Global Reach entre des environnements locaux et un cloud privé Azure VMware Solution.

>[!TIP]
>Pour écourter la sortie de la commande Azure CLI, ces instructions peuvent utiliser un [argument `–query`](/cli/azure/query-azure-cli) pour exécuter une requête JMESPath visant à afficher uniquement les résultats nécessaires.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant le même abonnement que le circuit ExpressRoute local. 

1. Ouvrez Cloud Shell et gardez l’interpréteur de commandes en environnement bash.

   :::image type="content" source="media/expressroute-global-reach/azure-portal-cloud-shell.png" alt-text="Capture d’écran montrant Cloud Shell dans le portail Azure":::

1. Créez le peering sur le circuit 1, en passant l’ID de ressource et la clé d’autorisation du circuit 2. 

   ```azurecli-interactive
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Capture d’écran montrant la commande et les résultats d’un peering réussi entre le circuit 1 et le circuit 2":::

Vous pouvez vous connecter à votre cloud privé à partir d’environnements locaux via le peering ExpressRoute Global Reach.

>[!TIP]
>Vous pouvez supprimer le peering en suivant les instructions de la section [Désactiver la connexion entre vos réseaux locaux](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks).


---

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à activer le peering ExpressRoute Global Reach d’un environnement local à un cloud privé. 

Passez au tutoriel suivant pour apprendre comment déployer et configurer la solution VMware HCX (Hybrid Cloud Extension) pour votre cloud privé Azure VMware Solution.

> [!div class="nextstepaction"]
> [Déployer et configurer VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
