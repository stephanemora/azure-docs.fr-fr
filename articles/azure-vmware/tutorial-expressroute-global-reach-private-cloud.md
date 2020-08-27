---
title: Appairer des environnements locaux avec un cloud privé
description: Dans le cadre de ce tutoriel Azure VMware Solution, vous allez créer un peering ExpressRoute Global Reach avec un cloud privé dans une solution Azure VMware Solution.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: db3f5988cb8c07d9b6e80f500ac6aff8f96dfded
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750453"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Tutoriel : Appairer des environnements locaux avec un cloud privé

ExpressRoute Global Reach connecte votre environnement local à vos clouds privés. La connexion Global Reach ExpressRoute est établie entre un circuit ExpressRoute de cloud privé et une connexion ExpressRoute existante à vos environnements locaux.  Des instructions sur la configuration d’ExpressRoute Global Reach avec Azure CLI et PowerShell sont disponibles. Par ailleurs, nous avons ajouté à l’[article relatif aux commandes CLI](../expressroute/expressroute-howto-set-global-reach-cli.md) des informations et exemples spécifiques pour vous aider à configurer le peering ExpressRoute Global Reach entre des environnements locaux et un cloud privé Azure VMware Solution.   

Avant d’activer la connectivité entre deux circuits ExpressRoute à l’aide d’ExpressRoute Global Reach, consultez l’article expliquant comment [activer la connectivité dans des abonnements Azure différents](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  Le circuit ExpressRoute que vous utilisez quand vous [configurez un réseau Azure-à-cloud privé](tutorial-configure-networking.md) implique la création et l’utilisation de clés d’autorisation pour l’appairage avec des passerelles ExpressRoute ou d’autres circuits ExpressRoute à l’aide de Global Reach. Vous aurez déjà utilisé une clé d’autorisation avec le circuit ExpressRoute et vous en créerez une seconde pour l’appairage avec votre circuit ExpressRoute local.

> [!TIP]
> Dans le cadre de ces instructions, votre circuit ExpressRoute local est le _circuit 1_. Quant à votre circuit ExpressRoute de cloud privé, il se trouve dans un abonnement différent et est nommé _circuit 2_. 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Utiliser les instructions existantes pour gérer les circuits ExpressRoute et les appairages ExpressRoute Global Reach
> * Créer une clé d’autorisation pour le circuit ExpressRoute de cloud privé (_circuit 2_)
> * Utiliser l’interface Azure CLI dans Cloud Shell dans l’abonnement du _circuit 1_ pour activer l’appairage ExpressRoute Global Reach entre circuit local et circuit de cloud privé

## <a name="prerequisites"></a>Prérequis

Les prérequis pour ce tutoriel sont les suivants :
- Un cloud privé avec circuit ExpressRoute appairé avec une passerelle ExpressRoute dans un réseau virtuel Azure, c’est-à-dire le _circuit 2_ dans le contexte des procédures d’appairage
- Un circuit ExpressRoute fonctionnel distinct utilisé pour connecter des environnements locaux à Azure, c’est-à-dire le _circuit 1_ dans le contexte des procédures d’appairage
- Un [bloc d’adresses réseau](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) /29 sans chevauchement pour l’appairage ExpressRoute Global Reach

## <a name="create-an-expressroute-authorization-key-in-the-azure-vmware-solution-private-cloud"></a>Créer une clé d’autorisation ExpressRoute dans le cloud privé Azure VMware Solution

1. Dans la page **Vue d’ensemble** du cloud privé, sous Gérer, sélectionnez **Connectivité > ExpressRoute > Demander une clé d’autorisation**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Sélectionnez Connectivité > ExpressRoute > Demander une clé d’autorisation pour lancer une nouvelle demande.":::

2. Entrez le nom de la clé d’autorisation, puis sélectionnez **Créer**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Cliquez sur Créer pour créer une clé d’autorisation.":::

   La clé créée apparaît dans la liste des clés d’autorisation pour le cloud privé. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Vérifiez que la nouvelle clé d’autorisation apparaît dans la liste des clés pour le cloud privé.":::

3. Prenez note de la clé d’autorisation et de l’ID ExpressRoute avec le bloc d’adresses /29. Vous les utiliserez à l’étape suivante pour effectuer l’appairage. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Appairer un cloud privé avec un circuit local à l’aide de la clé d’autorisation

Vous avez créé une clé d’autorisation pour le circuit ExpressRoute de cloud privé. À présent, vous pouvez l’appairer avec votre circuit ExpressRoute local.  L’appairage s’effectue du point de vue du circuit ExpressRoute local à l’aide de l’interface Azure CLI dans Cloud Shell, avec l’ID de ressource et la clé d’autorisation de votre circuit ExpressRoute de cloud privé, créés précédemment.

> [!TIP]  
> Par souci de concision dans la sortie de la commande Azure CLI, ces instructions peuvent utiliser un [argument `–query` pour exécuter une requête JMESPath afin d’afficher uniquement les résultats nécessaires](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).


1. Connectez-vous au portail Azure en utilisant le même abonnement que le circuit ExpressRoute local et ouvrez Cloud Shell. Gardez l’interpréteur de commandes en environnement Bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Connectez-vous au portail Azure et ouvrez Cloud Shell.":::
 
2. Dans la ligne de commande, entrez la commande Azure CLI permettant de créer l’appairage à l’aide de vos propres informations, de votre ID de ressource, de votre clé d’autorisation et du bloc réseau CIDR /29. 

   Voici un exemple de la commande que vous allez utiliser et la sortie indiquant la réussite de l’appairage. L’exemple de commande est basé sur la commande utilisée à l’[étape 3 de la section « Activer la connectivité entre des circuits ExpressRoute dans des abonnements Azure différents »](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Créez un appairage ExpressRoute Global Reach avec la commande Azure CLI dans Cloud Shell.":::
 
   Vous devez maintenant être en mesure de vous connecter à partir d’environnements locaux à votre cloud privé par le biais de l’appairage ExpressRoute Global Reach.

> [!TIP]
> Vous pouvez supprimer l’appairage que vous venez de créer en suivant les instructions de la section [Désactiver la connexion entre vos réseaux locaux](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks).


## <a name="next-steps"></a>Étapes suivantes

Si vous envisagez d’utiliser HCX (Hybrid Cloud Extension) pour migrer des charges de travail de machine virtuelle vers votre cloud privé, utilisez la procédure [Installer HCX pour Azure VMware Solution](hybrid-cloud-extension-installation.md).


<!-- LINKS - external-->

<!-- LINKS - internal -->
