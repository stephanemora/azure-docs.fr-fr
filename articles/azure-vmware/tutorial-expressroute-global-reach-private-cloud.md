---
title: Appairer des environnements locaux à Azure VMware Solution
description: Découvrez comment créer un peering ExpressRoute Global Reach à un cloud privé dans Azure VMware Solution.
ms.topic: tutorial
ms.custom: contperf-fy22q1
ms.date: 06/21/2021
ms.openlocfilehash: 7e3542dbd91204688b39eddcdbdb5f374a1b35d2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464502"
---
# <a name="peer-on-premises-environments-to-azure-vmware-solution"></a>Appairer des environnements locaux à Azure VMware Solution

Dans cet étape de démarrage rapide, vous allez connecter Azure VMware Solution à votre environnement local. ExpressRoute Global Reach connecte votre environnement local à votre cloud privé Azure VMware Solution. La connexion Global Reach ExpressRoute est établie entre le circuit ExpressRoute de cloud privé et une connexion ExpressRoute existante à vos environnements locaux. 

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="Diagramme montrant la connectivité du réseau local ExpressRoute Global Reach" lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

>[!NOTE]
>Vous pouvez vous connecter par le biais d’un VPN. Cela dit, ce type de connexion n’est pas abordé dans ce guide de démarrage rapide.


## <a name="prerequisites"></a>Prérequis

- Consultez la documentation pour savoir comment [activer la connectivité dans différents abonnements Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  
- Un circuit ExpressRoute fonctionnel distinct utilisé pour connecter des environnements locaux à Azure, qui est le _circuit 1_ du peering.
- Vérifiez que toutes les passerelles, y compris le service du fournisseur ExpressRoute, prennent en charge un numéro de système autonome (ASN) à 4 octets. Azure VMware Solution utilise des numéros de système autonome (ASN) publics à 4 octets pour publier les routes.

>[!NOTE]
> Si vous publiez une route par défaut sur Azure (0.0.0.0/0), assurez-vous qu’une route plus spécifique contenant vos réseaux locaux est publiée en plus de la route par défaut pour permettre l’accès de gestion à AVS. Une route 0.0.0.0/0 seule est rejetée par le réseau de gestion d’Azure VMware Solution pour garantir le bon fonctionnement du service.

## <a name="create-an-expressroute-auth-key-in-the-on-premises-expressroute-circuit"></a>Créer une clé d’autorisation ExpressRoute dans le circuit ExpressRoute local

Le propriétaire du circuit crée une autorisation, ce qui entraîne la création d’une clé d’autorisation dont un utilisateur du circuit peut se servir pour connecter ses passerelles de réseau virtuel au circuit ExpressRoute. Une autorisation n’est valide que pour une seule connexion.

> [!NOTE]
> Chaque connexion nécessite une autorisation distincte.

1. Dans le panneau **Circuits ExpressRoute**, sous Paramètres, sélectionnez **Autorisations**.

1. Entrez le nom de la clé d’autorisation et sélectionnez **Enregistrer**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="Sélectionnez Autorisations et entrez le nom de la clé d’autorisation.":::

   Une fois créée, la nouvelle clé apparaît dans la liste des clés d’autorisation pour le circuit.

1. Copiez la clé d’autorisation et l’ID ExpressRoute. Vous les utiliserez à l’étape suivante pour effectuer l’appairage.

## <a name="peer-private-cloud-to-on-premises"></a>Appairer un cloud privé à un environnement local 
Maintenant que vous avez créé une clé d’autorisation pour le circuit ExpressRoute de cloud privé, vous pouvez l’appairer avec votre circuit ExpressRoute local. Le peering est effectué à partir du circuit ExpressRoute local dans le **portail Azure**. Vous utilisez l’ID de ressource (ID du circuit ExpressRoute) et la clé d’autorisation de votre circuit ExpressRoute de cloud privé pour terminer le peering.

1. Dans le cloud privé, sous Gérer, sélectionnez **Connectivité** > **ExpressRoute Global Reach** > **Ajouter**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Capture d’écran montrant l’onglet ExpressRoute Global Reach dans le cloud privé d’Azure VMware Solution":::

1. Entrez l’ID ExpressRoute et la clé d’autorisation créés dans la section précédente.

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Capture d’écran montrant la boîte de dialogue permettant d’entrer les informations de connexion.":::   

1. Sélectionnez **Create** (Créer). La nouvelle connexion s’affiche dans la liste des connexions cloud locales.

>[!TIP]
>Vous pouvez supprimer ou déconnecter une connexion de la liste en sélectionnant **Plus**.  
>
>:::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Capture d’écran montrant comment déconnecter ou supprimer une connexion locale dans Azure VMware Solution.":::


## <a name="verify-on-premises-network-connectivity"></a>Vérifier la connectivité réseau locale

Votre **routeur de périphérie local** doit maintenant indiquer où ExpressRoute connecte les segments de réseau NSX-T et les segments de gestion d'Azure VMware Solution.

>[!IMPORTANT]
>Chaque environnement est différent, et certains doivent autoriser ces itinéraires à se propager à nouveau sur le réseau local.  

## <a name="next-steps"></a>Étapes suivantes
Passez au tutoriel suivant pour apprendre comment déployer et configurer la solution VMware HCX (Hybrid Cloud Extension) pour votre cloud privé Azure VMware Solution.

> [!div class="nextstepaction"]
> [Déployer et configurer VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
