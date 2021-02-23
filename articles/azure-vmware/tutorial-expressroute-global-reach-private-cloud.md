---
title: 'Tutoriel : Appairer des environnements locaux avec un cloud privé'
description: Découvrez comment créer un appairage ExpressRoute Global Reach avec un cloud privé dans une solution Azure VMware Solution.
ms.topic: tutorial
ms.date: 01/27/2021
ms.openlocfilehash: 04be94849f1cd78ff915ca271845b283c8cfc64c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558812"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Tutoriel : Appairer des environnements locaux avec un cloud privé

ExpressRoute Global Reach connecte votre environnement local à votre cloud privé Azure VMware Solution. La connexion Global Reach ExpressRoute est établie entre le circuit ExpressRoute de cloud privé et une connexion ExpressRoute existante à vos environnements locaux. 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Utilisez le portail Azure pour activer le peering ExpressRoute Global Reach d’un environnement local à un cloud privé.


## <a name="before-you-begin"></a>Avant de commencer

Avant d’activer la connectivité entre deux circuits ExpressRoute à l’aide d’ExpressRoute Global Reach, consultez l’article expliquant comment [activer la connectivité dans des abonnements Azure différents](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Prérequis

- Un circuit ExpressRoute fonctionnel distinct utilisé pour connecter des environnements locaux à Azure.
- Vérifiez que toutes les passerelles, dont le service du fournisseur ExpressRoute, prennent en charge un numéro de système autonome (ASN) à 4 octets. Azure VMware Solution utilise des ASN publics à 4 octets pour publier les routes.


## <a name="create-an-expressroute-authorization-key-in-the-on-premises-expressroute-circuit"></a>Créer une clé d’autorisation ExpressRoute dans le circuit ExpressRoute local

1. Dans le panneau **Circuits ExpressRoute**, sous Paramètres, sélectionnez **Autorisations**.

2. Entrez le nom de la clé d’autorisation et sélectionnez **Enregistrer**.

    :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="Sélectionnez Autorisations et entrez le nom de la clé d’autorisation.":::
  
     Une fois créée, la nouvelle clé apparaît dans la liste des clés d’autorisation pour le circuit.
 
 4. Prenez note de la clé d’autorisation et de l’ID ExpressRoute. Vous les utiliserez à l’étape suivante pour effectuer l’appairage.
 
 
 ## <a name="peer-private-cloud-to-on-premises"></a>Appairer un cloud privé à un environnement local

1. Dans la page **Vue d’ensemble** du cloud privé, sous Gérer, sélectionnez **Connectivité > ExpressRoute Global Reach > Ajouter**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Dans le menu, sélectionnez Connectivité, l’onglet ExpressRoute Global Reach, puis Ajouter.":::

2. Entrez l’ID ExpressRoute et la clé d’autorisation créés dans la section précédente.

    :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Entrez l’ID ExpressRoute et la clé d’autorisation créés, puis sélectionnez Créer.":::

3. Sélectionnez **Create** (Créer). La nouvelle connexion apparaît dans la liste des connexions cloud locales.  

>[!TIP]
>Vous pouvez supprimer ou déconnecter une connexion de la liste en sélectionnant **Plus**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Déconnecter ou supprimer une connexion locale":::


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à activer le peering ExpressRoute Global Reach d’un environnement local à un cloud privé. 

Passez au tutoriel suivant pour apprendre comment déployer et configurer la solution VMware HCX (Hybrid Cloud Extension) pour votre cloud privé Azure VMware Solution.

> [!div class="nextstepaction"]
> [Déployer et configurer VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
