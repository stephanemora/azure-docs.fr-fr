---
title: Déployer Traffic Manager pour équilibrer les charges de travail Azure VMware Solution
description: Découvrez comment intégrer Traffic Manager avec Azure VMware Solution pour équilibrer les charges de travail d’application sur plusieurs points de terminaison dans différentes régions.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 46570c5a61fc0a641d83126fd0f8ef35b3dc42cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99988602"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Déployer Traffic Manager pour équilibrer les charges de travail Azure VMware Solution

Cet article décrit les étapes de l’intégration d’[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) à Azure VMware Solution. L’intégration équilibre les charges de travail d’application sur plusieurs points de terminaison. Cet article présente également les étapes de configuration de Traffic Manager pour diriger le trafic entre trois passerelles [Azure Application Gateway](../application-gateway/overview.md) couvrant plusieurs régions Azure VMware Solution. 

Les passerelles disposent de machines virtuelles Azure VMware Solution configurées en tant que membres du pool principal pour équilibrer la charge des demandes de couche 7 entrantes. Pour en savoir plus, consultez la rubrique [Utiliser Azure Application Gateway pour protéger vos applications web sur Azure VMware Solution](protect-azure-vmware-solution-with-application-gateway.md)

Le diagramme montre comment Traffic Manager fournit un équilibrage de charge pour les applications au niveau du DNS entre les points de terminaison régionaux. Les passerelles ont des membres du pool principal configurés en tant que serveurs IIS et référencés en tant que points de terminaison externes Azure VMware Solution. La connexion sur le réseau virtuel entre deux régions de cloud privé utilise une passerelle ExpressRoute.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Diagramme de l’architecture de l’intégration de Traffic Manager à Azure VMware Solution" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

Avant toute chose, examinez les [prérequis](#prerequisites). Ensuite, nous allons parcourir les procédures pour :

> [!div class="checklist"]
> * vérifier la configuration de vos passerelles d’application et du segment NSX-T ;
> * créer votre profil Traffic Manager ;
> * ajouter des points de terminaison externes à votre profil Traffic Manager.

## <a name="prerequisites"></a>Conditions préalables requises

- Trois machines virtuelles configurées en tant que serveurs Microsoft IIS s’exécutant dans différentes régions Azure VMware Solution : 
   - USA Ouest
   - Europe Ouest
   - USA Est (en local) 

- Une passerelle d’application avec des points de terminaison externes dans les régions Azure VMware Solution mentionnées ci-dessus.

- Hôte avec connectivité Internet pour la vérification. 

- Un [segment réseau NSX-T créé dans Azure VMware Solution](tutorial-nsx-t-network-segment.md).

## <a name="verify-your-application-gateways-configuration"></a>Vérifier la configuration de vos passerelles d’application

Les étapes suivantes permettent de vérifier la configuration de vos passerelles d’application.

1. Dans le portail Azure, sélectionnez **Passerelles d’application** pour afficher la liste de vos passerelles d’application actuelles :

   - AVS-GW-WUS
   - AVS-GW-EUS (locale)
   - AVS-GW-UEO

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Capture d’écran de la page de la passerelle applicative montrant la liste des passerelles applicatives configurées." lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. Sélectionnez l’une des passerelles d’application précédemment déployées. 

   Une fenêtre s’ouvre, qui affiche diverses informations sur la passerelle applicative. 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Capture d’écran de la page de passerelle applicative montrant les détails de la passerelle applicative sélectionnée." lightbox="media/traffic-manager/backend-pool-config.png":::

1. Sélectionnez **Pools principaux** pour vérifier la configuration de l’un des pools principaux. Vous voyez un membre du pool principal de machines virtuelles configuré en tant que serveur web avec l’adresse IP 172.29.1.10.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Capture d’écran de la page Modifier le pool principal avec l’adresse IP cible mise en surbrillance.":::

1. Vérifiez la configuration des autres passerelles d’application et membres du pool principal. 

## <a name="verify-the-nsx-t-segment-configuration"></a>Vérifier la configuration du segment NSX-T

Les étapes suivantes permettent de vérifier la configuration du segment NSX-T dans l’environnement Azure VMware Solution.

1. Sélectionnez **Segments** pour afficher vos segments configurés.  Vous voyez le segment Contoso-segment1 connecté à la passerelle Contoso-T01, un routeur flexible de niveau 1.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Capture d’écran montrant des profils de segment dans NSX-T Manager." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. Sélectionnez **Passerelles de niveau 1** pour afficher la liste des vos passerelles de niveau 1 avec le nombre de segments liés. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Capture d’écran montrant l’adresse de la passerelle du segment sélectionné.":::    

1. Sélectionnez le segment lié à Contoso-T01. Une fenêtre s’ouvre, qui affiche l’interface logique configurée sur le routeur de niveau 01. Celui-ci sert de passerelle vers la machine virtuelle du membre du pool principal connecté au segment.

1. Dans le client vSphere, sélectionnez la machine virtuelle pour afficher ses détails. 

   >[!NOTE]
   >Son adresse IP correspond au membre du pool principal de machines virtuelles configuré comme serveur web dans la section précédente : 172.29.1.10.

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Capture d’écran montrant les détails de la machine virtuelle dans le client VSphere." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Sélectionnez la machine virtuelle, puis cliquez sur **ACTIONS > Modifier les paramètres** pour vérifier la connexion au segment NSX-T.

## <a name="create-your-traffic-manager-profile"></a>Créer votez profil Traffic Manager

1. Connectez-vous au [portail Azure](https://rc.portal.azure.com/#home). Sous **Services Azure > Mise en réseau**, sélectionnez **Profils Traffic Manager**.

2. Sélectionnez **+ Ajouter** pour créer un profil Traffic Manager.
 
3. Fournissez les informations suivantes, puis sélectionnez **Créer** :

   - Nom de profil
   - Méthode de routage (utiliser [pondérée](../traffic-manager/traffic-manager-routing-methods.md)
   - Abonnement
   - Resource group

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Ajouter des points de terminaison externes au profil Traffic Manager

1. Sélectionnez le profil Traffic Manager dans le volet des résultats de la recherche, sélectionnez **Points de terminaison**, puis **+ Ajouter**.

1. Pour chacun des points de terminaison externes dans les différentes régions, entrez les informations requises, puis sélectionnez **Ajouter** : 
   - Type
   - Nom
   - Nom de domaine complet (FQDN) ou IP
   - Poids (affectez un poids de 1 à chaque point de terminaison). 

   Une fois créés, les trois points de terminaison apparaissent dans le profil Traffic Manager. L’état de l’analyse doit être **En ligne** pour tous.

3. Sélectionnez **Vue d’ensemble** et copiez l’URL sous **Nom DNS**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Capture d’écran montrant une vue d’ensemble de point de terminaison Traffic Manager avec le nom DNS mis en surbrillance." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Collez l’URL du nom DNS dans un navigateur. La capture d’écran montre le trafic dirigé vers la région Europe Ouest.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Capture d’écran de la fenêtre du navigateur montrant le trafic acheminé vers la région Europe Ouest."::: 

5. Actualisez votre navigateur. La capture d’écran présente le trafic dirigé vers un autre ensemble de membres du pool principal dans la région USA Ouest.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Capture d’écran de la fenêtre du navigateur montrant le trafic acheminé vers la région USA Ouest."::: 

6. Réactualisez votre navigateur. La capture d’écran présente le trafic dirigé vers l’ensemble final de membres du pool principal local.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Capture d’écran de la fenêtre du navigateur montrant le trafic acheminé vers une région locale.":::

## <a name="next-steps"></a>Étapes suivantes

À présent que vous savez comment intégrer Azure Traffic Manager avec Azure VMware Solution, vous pouvez découvrir ce qui suit :

- [Utilisation d’Azure Application Gateway sur Azure VMware Solution](protect-azure-vmware-solution-with-application-gateway.md).
- [Méthodes de routage de Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).
- [Combinaison de services d’équilibrage de charge dans Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- [Mesure des performances de Traffic Manager](../traffic-manager/traffic-manager-performance-considerations.md).
