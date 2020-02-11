---
title: Démarrage rapide - Créer un service Private Link avec le portail Azure
titlesuffix: Azure Private Link
description: Découvrez dans ce guide de démarrage rapide comment créer un service Private Link avec le portail Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: 4dd27d3fb4321c2eecf222136376bfd4b49790bb
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026202"
---
# <a name="quickstart-create-a-private-link-service-using-the-azure-portal"></a>Démarrage rapide : Créer un service Private Link avec le portail Azure

Le service Azure Private Link est la référence à votre propre service Azure Private Link. Le service ou la ressource qui fonctionne derrière Azure Load Balancer peut être activé pour l’accès Private Link. Les consommateurs de votre service peuvent accéder au service de façon privée à partir de leurs propres réseaux virtuels. Dans ce guide de démarrage rapide, vous découvrez comment créer un service Private Link avec le portail Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.


## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Créer un équilibreur de charge interne

Tout d’abord, créez un réseau virtuel, puis un équilibreur de charge interne à utiliser avec le service Azure Private Link.

### <a name="create-the-virtual-network"></a>Créer un réseau virtuel

Dans cette section, vous allez créer un réseau virtuel et le sous-réseau pour héberger la machine virtuelle qui est utilisée pour accéder à votre ressource Private Link.


1. En haut à gauche du portail, sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.
   
1. Dans le volet **Créer un réseau virtuel**, tapez ou sélectionnez les valeurs suivantes :
   
   - **Name** : Tapez **MyVNet**.
   - **Groupe de ressources** : Sélectionnez **Créer**, entrez **MyResourceGroupLB**, puis sélectionnez **OK**. 
   - **Sous-réseau** > **Nom** : Entrez **MyBackendSubnet**.
   
1. Sélectionnez **Create** (Créer).

   ![Créez un réseau virtuel](../load-balancer/media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-a-standard-load-balancer"></a>Créer un équilibreur de charge standard

Créez un équilibreur de charge interne standard à l’aide du portail. Le nom et l’adresse IP que vous créez sont automatiquement configurés comme front-end de l’équilibreur de charge.

1. En haut à gauche du portail, sélectionnez **Créer une ressource** > **Mise en réseau** > **Équilibreur de charge**.
   
2. Sous l’onglet **De base** de la page **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis choisissez **Vérifier + créer** :

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | Subscription               | Sélectionnez votre abonnement.    |    
    | Resource group         | Sélectionnez *MyResourceGroupLB* dans la zone de liste déroulante.|
    | Name                   | *myLoadBalancer*                                   |
    | Région         | Sélectionnez **USA Est**.                                        |
    | Type          | sélectionnez **Interne**.                                        |
    | SKU           | Sélectionnez **Standard**.                          |
    | Réseau virtuel           | Sélectionnez *MyVNet*.                          |    
    | Affectation d’adresses IP              | Sélectionnez **Statique**.   |
    | Adresse IP privée|entrez une adresse qui se trouve dans l'espace d'adressage de votre réseau virtuel et de votre sous-réseau, par exemple *10.3.0.7*.  |

3. Sous l’onglet **Vérifier + créer**, cliquez sur **Créer**. 
   

### <a name="create-standard-load-balancer-resources"></a>Créer des ressources d’équilibreur de charge Standard

Dans cette section, vous configurez les paramètres de l’équilibreur de charge pour un pool d’adresses back-end et une sonde d’intégrité, puis spécifiez les règles de l’équilibreur de charge.

#### <a name="create-a-backend-pool"></a>Créer un pool back-end

Pour distribuer le trafic à vos ressources, un pool d’adresses de back-end contient les adresses IP des cartes d’interface réseau virtuelles connectées à l’équilibreur de charge. Créez le pool d’adresses de back-end *myBackendPool* afin d’inclure des ressources pour l’équilibrage de charge du trafic.

1. Sélectionnez **Tous les services** dans le menu de gauche, **Toutes les ressources**, puis **myLoadBalancer** dans la liste des ressources.
2. Sous **Paramètres**, sélectionnez **Pools principaux**, puis **Ajouter**.
3. Dans la page **Ajouter un pool de backends**, entrez *myBackendPool* comme nom de votre pool principal, puis sélectionnez **Ajouter**.



#### <a name="create-a-health-probe"></a>Créer une sonde d’intégrité

Pour permettre à l’équilibreur de charge de superviser l’état des ressources, vous utilisez une sonde d’intégrité. La sonde d’intégrité ajoute ou supprime dynamiquement des ressources dans la rotation de l’équilibreur de charge en fonction de leur réponse aux contrôles d’intégrité. 

**Pour créer une sonde d’intégrité en vue de superviser l’intégrité des ressources :**

1. Sélectionnez **Toutes les ressources** dans le menu de gauche, puis sélectionnez **MyLoadBalancer** dans la liste de ressources.
   
1. Sous **Paramètres**, sélectionnez **Health probes** (Sondes d’intégrité), puis **Ajouter**.
   
1. Dans la page **Ajouter une sonde d’intégrité**, tapez ou sélectionnez les valeurs suivantes :
   
   - **Name** : Entrez **MyHealthProbe**.
   - **Protocole** : Faites défiler et sélectionnez **TCP**. 
   - **Port** : Entrez **80**. 
   - **Intervalle** : Entrez **15**. L’intervalle est le nombre de secondes entre les tentatives de la sonde.
   - **Seuil de défaillance sur le plan de l’intégrité** : Entrez **2**. Cette valeur est le nombre d’échecs de sonde consécutifs qui se produisent avant qu’une machine virtuelle soit considérée comme défaillante.
   
1. Sélectionnez **OK**.

#### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge

Une règle d’équilibreur de charge définit comment le trafic est distribué aux ressources. La règle définit la configuration IP front-end pour le trafic entrant, le pool d’adresses IP front-end pour recevoir le trafic ainsi que les ports source et de destination nécessaires. 

La règle d’équilibreur de charge nommée **MyLoadBalancerRule** écoute sur le port 80 dans le front-end **LoadBalancerFrontEnd**. La règle envoie le trafic réseau au pool d’adresses front-end **MyBackEndPool**, ainsi que sur le port 80. 

**Pour créer la règle d’équilibreur de charge :**

1. Sélectionnez **Toutes les ressources** dans le menu de gauche, puis sélectionnez **MyLoadBalancer** dans la liste de ressources.
   
1. Sous **Paramètres**, sélectionnez **Règles d’équilibrage de charge**, puis **Ajouter**.
   
1. Dans la page **Ajouter une règle d’équilibrage de charge**, tapez ou sélectionnez les valeurs suivantes, si elles ne sont pas déjà présentes :
   
   - **Name** : Entrez **MyLoadBalancerRule**.
   - **Adresse IP du serveur frontal :** entrez **LoadBalancerFrontEnd** si absent.
   - **Protocole** : Sélectionnez **TCP**.
   - **Port** : Entrez **80**.
   - **Port principal** : Entrez **80**.
   - **Pool principal** : Sélectionnez **MyBackendPool**.
   - **Sonde d’intégrité** : Sélectionnez **MyHealthProbe**. 
   
1. Sélectionnez **OK**.
   
## <a name="create-a-private-link-service"></a>Créer un service Private Link

Dans cette section, vous allez créer un service Private Link derrière un équilibreur de charge standard.

1. En haut à gauche de l’écran du portail Azure, sélectionnez **Créer une ressource** > **Mise en réseau** > **Centre de liaisons privées (préversion)** . Vous pouvez également rechercher Private Link via la recherche dans le portail.

2. Dans **Centre de liaisons privées - Vue d’ensemble**, dans l’option permettant de **« Exposer votre propre service pour permettre à d’autres utilisateurs de se connecter »** , sélectionnez Démarrer.

3. Dans **Créer un service Private Link - Concepts de base**, entrez ou sélectionnez les informations suivantes :

    | Paramètre           | Valeur                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | **Détails du projet :**  |                                                                              |
    | Subscription      | Sélectionnez votre abonnement                                                     |
    | Groupe de ressources    | Sélectionnez *MyResourceGroupLB*                                                     |
    | **DÉTAILS DE L’INSTANCE :** |                                                                              |
    | Name              | Entrez *myPrivateLinkService* |
    | Région            | Sélectionnez *USA Est 2*                                                         |

4. Sélectionnez **Suivant : Paramètres sortants**.

5. Dans **Créer un service Private Link - Paramètres sortants**, entrez ou sélectionnez les informations suivantes :


    | Paramètre                           | Valeur                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | Load Balancer                     | Sélectionnez *MyLoadBalancer*                                                           |
    | Adresse IP front-end de l’équilibreur de charge | Sélectionnez l’adresse IP front-end de MyLoadBalancer                                |
    | Réseau virtuel NAT source        | Sélectionnez *myVNET*                                                                   |
    | Sous-réseau NAT source                 | Sélectionnez *myBackendSubnet*                                                          |
    | Activer le proxy TCP v2               | Sélectionnez OUI/NON selon que votre application attend ou non un en-tête de proxy TCP v2 |
    | Paramètres d’adresse IP privée       | Configurer la méthode d’allocation et l’adresse IP pour chaque adresse IP NAT                  |

6. Sélectionnez **Suivant : Sécurité des accès**.

7. Dans **Créer un service Private Link - Sécurité des accès**, entrez ou sélectionnez les informations suivantes :

    | Paramètre                                     | Valeur                                                             |
    |---------------------------------------------|-------------------------------------------------------------------|
    | Visibilité                                  | Choisissez *Contrôle d’accès en fonction du rôle (RBAC) uniquement*         |
  
8. Sélectionnez **Suivant : Balises**, puis **Vérifier + créer** ou choisissez l’**onglet Vérifier + créer** en haut de la page.

9. Passez en revue vos informations, puis sélectionnez **Créer**.

## <a name="clean-up-resources"></a>Nettoyer les ressources 
Quand vous avez terminé d’utiliser le service Private Link, supprimez le groupe de ressources pour nettoyer les ressources utilisées dans ce guide de démarrage rapide :

1. Entrez *myResourceGroupLB* dans la zone **Rechercher** en haut du portail, puis sélectionnez *myResourceGroupLB* dans les résultats de la recherche. 
2. Sélectionnez **Supprimer le groupe de ressources**. 
3. Entrez myResourceGroup dans **TAPER LE NOM DU GROUPE DE RESSOURCES**, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un équilibreur de charge Azure interne et un service Private Link. Pour plus d’informations sur la création de points de terminaison privés, consultez [Créer des points de terminaison privés avec le portail Azure](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).
