---
title: 'Tutoriel : Appairer des environnements locaux avec un cloud privé'
description: Découvrez comment créer un appairage ExpressRoute Global Reach avec un cloud privé dans une solution Azure VMware Solution.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 9de6cbe177ac8d2ca4957e80c7ca1072a0d7985e
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948304"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Tutoriel : Appairer des environnements locaux avec un cloud privé

ExpressRoute Global Reach connecte votre environnement local à votre cloud privé Azure VMware Solution. La connexion Global Reach ExpressRoute est établie entre le circuit ExpressRoute de cloud privé et une connexion ExpressRoute existante à vos environnements locaux. 

Le circuit ExpressRoute que vous utilisez quand vous [configurez un réseau Azure-à-cloud privé](tutorial-configure-networking.md) implique la création et l’utilisation de clés d’autorisation pour l’appairage avec des passerelles ExpressRoute ou d’autres circuits ExpressRoute à l’aide de Global Reach. Vous aurez déjà utilisé une clé d’autorisation avec le circuit ExpressRoute et, dans ce tutoriel, vous en créerez une seconde pour l’appairage avec votre circuit ExpressRoute local.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une seconde clé d’autorisation pour le circuit ExpressRoute de cloud privé (_circuit 2_)
> * Utiliser le [portail Azure](#azure-portal-method) ou l’[interface Azure CLI dans une méthode Cloud Shell](#azure-cli-in-a-cloud-shell-method) dans l’abonnement du _circuit 1_ pour activer l’appairage ExpressRoute Global Reach entre circuit local et circuit de cloud privé


## <a name="before-you-begin"></a>Avant de commencer

Avant d’activer la connectivité entre deux circuits ExpressRoute à l’aide d’ExpressRoute Global Reach, consultez l’article expliquant comment [activer la connectivité dans des abonnements Azure différents](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Prérequis

1. Connectivité établie vers et depuis un cloud privé Azure VMware Solution avec son circuit ExpressRoute appairé avec une passerelle ExpressRoute dans un réseau virtuel Azure, c’est-à-dire le _circuit 2_ dans le contexte des procédures d’appairage.  
1. Un circuit ExpressRoute fonctionnel distinct utilisé pour connecter des environnements locaux à Azure, c’est-à-dire le _circuit 1_ dans le contexte des procédures d’appairage.
1. Un [bloc d’adresses réseau](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) /29 sans chevauchement pour l’appairage ExpressRoute Global Reach

> [!TIP]
> Dans le cadre de ces prérequis, votre circuit ExpressRoute local est le _circuit 1_. Quant à votre circuit ExpressRoute de cloud privé, il se trouve dans un abonnement différent et est nommé _circuit 2_. 


## <a name="create-an-expressroute-authorization-key-in-the-private-cloud"></a>Créer une clé d’autorisation ExpressRoute dans le cloud privé

1. Dans la page **Vue d’ensemble** du cloud privé, sous Gérer, sélectionnez **Connectivité > ExpressRoute > Demander une clé d’autorisation**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Sélectionnez Connectivité > ExpressRoute > Demander une clé d’autorisation pour lancer une nouvelle demande.":::

2. Entrez le nom de la clé d’autorisation, puis sélectionnez **Créer**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Sélectionnez Connectivité > ExpressRoute > Demander une clé d’autorisation pour lancer une nouvelle demande.":::

   La clé créée apparaît dans la liste des clés d’autorisation pour le cloud privé. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Sélectionnez Connectivité > ExpressRoute > Demander une clé d’autorisation pour lancer une nouvelle demande.":::

3. Prenez note de la clé d’autorisation et de l’ID ExpressRoute avec le bloc d’adresses /29. Vous les utiliserez à l’étape suivante pour effectuer l’appairage. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Appairer un cloud privé avec un circuit local à l’aide de la clé d’autorisation

Vous avez créé une clé d’autorisation pour le circuit ExpressRoute de cloud privé. À présent, vous pouvez l’appairer avec votre circuit ExpressRoute local.  L’appairage est effectué dans le contexte du circuit ExpressRoute local soit dans le [portail Azure](#azure-portal-method) soit avec l’[interface Azure CLI dans une méthode Cloud Shell](#azure-cli-in-a-cloud-shell-method). Quelle que soit la méthode utilisée, vous allez utiliser l’ID de ressource et la clé d’autorisation de votre circuit ExpressRoute de cloud privé, que vous avez créé au cours des étapes précédentes, pour terminer l’appairage.

### <a name="azure-portal-method"></a>Méthode du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant le même abonnement que le circuit ExpressRoute local.

1. Dans la page **Vue d’ensemble** du cloud privé, sous Gérer, sélectionnez **Connectivité > ExpressRoute Global Reach > Ajouter**.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Sélectionnez Connectivité > ExpressRoute > Demander une clé d’autorisation pour lancer une nouvelle demande.":::

1. Vous pouvez créer une connexion cloud locale en procédant de l’une des manières suivantes :

   - Sélectionnez le circuit ExpressRoute dans la liste.
   - Si vous avez un ID de circuit, vous pouvez le copier-coller.

1. Sélectionnez **Connecter**. La nouvelle connexion apparaît dans la liste des connexions cloud locales.  

>[!TIP]
>Vous pouvez supprimer ou déconnecter une connexion de la liste en sélectionnant **Plus**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Sélectionnez Connectivité > ExpressRoute > Demander une clé d’autorisation pour lancer une nouvelle demande.":::

### <a name="azure-cli-in-a-cloud-shell-method"></a>Azure CLI dans une méthode Cloud Shell

Nous avons ajouté à l’[article relatif aux commandes CLI](../expressroute/expressroute-howto-set-global-reach-cli.md) des informations et exemples spécifiques pour vous aider à configurer l’appairage ExpressRoute Global Reach entre des environnements locaux et un cloud privé Azure VMware Solution.  

> [!TIP]  
> Par souci de concision dans la sortie de la commande Azure CLI, ces instructions peuvent utiliser un [argument `–query` pour exécuter une requête JMESPath afin d’afficher uniquement les résultats nécessaires](/cli/azure/query-azure-cli).


1. Connectez-vous au portail Azure en utilisant le même abonnement que le circuit ExpressRoute local et ouvrez Cloud Shell. Gardez l’interpréteur de commandes en environnement Bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Sélectionnez Connectivité > ExpressRoute > Demander une clé d’autorisation pour lancer une nouvelle demande.":::
 
2. Dans la ligne de commande, entrez la commande Azure CLI permettant de créer l’appairage à l’aide de vos propres informations, de votre ID de ressource, de votre clé d’autorisation et du bloc réseau CIDR /29. 

   Voici un exemple de la commande que vous allez utiliser et la sortie indiquant la réussite de l’appairage. L’exemple de commande est basé sur la commande utilisée à l’[étape 3 de la section « Activer la connectivité entre des circuits ExpressRoute dans des abonnements Azure différents »](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Sélectionnez Connectivité > ExpressRoute > Demander une clé d’autorisation pour lancer une nouvelle demande.":::
 
   Vous devez maintenant être en mesure de vous connecter à partir d’environnements locaux à votre cloud privé par le biais de l’appairage ExpressRoute Global Reach.

> [!TIP]
> Vous pouvez supprimer l’appairage que vous venez de créer en suivant les instructions de la section [Désactiver la connexion entre vos réseaux locaux](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks).


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer une seconde clé d’autorisation pour le circuit ExpressRoute de cloud privé et activé l’appairage ExpressRoute Global Reach entre circuit local et circuit de cloud privé. 

Passez au tutoriel suivant pour apprendre comment déployer et configurer la solution VMware HCX (Hybrid Cloud Extension) pour votre cloud privé Azure VMware Solution.

> [!div class="nextstepaction"]
> [Déployer et configurer VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->