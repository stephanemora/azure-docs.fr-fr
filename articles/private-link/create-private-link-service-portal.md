---
title: Démarrage rapide - Créer un service Private Link avec le portail Azure
titlesuffix: Azure Private Link
description: Découvrez dans ce guide de démarrage rapide comment créer un service Private Link en utilisant le portail Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/18/2021
ms.author: allensu
ms.openlocfilehash: 3e9ade329d2b26d36763db579b0fcec03e938aad
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555455"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Démarrage rapide : Créer un service Private Link en utilisant le portail Azure

Commencez à créer un service Private Link qui fait référence à votre service.  Accordez à Private Link un accès à votre service ou ressource déployé(e) derrière Azure Standard Load Balancer.  Les utilisateurs de votre service ont un accès privé à partir de leur réseau virtuel.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Créer un équilibrage de charge interne

Dans cette section, vous allez créer un réseau virtuel et un Azure Load Balancer interne.

### <a name="virtual-network"></a>Réseau virtuel

Dans cette section, vous allez créer un réseau virtuel et un sous-réseau pour héberger l’équilibreur de charge qui accède à votre service Private Link.

1. En haut à gauche de l’écran, sélectionnez **Créer une ressource > Réseau > Réseau virtuel**, ou recherchez **Réseau virtuel** à partir de la zone de recherche.

2. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Sélectionner votre abonnement Azure                                  |
    | Groupe de ressources   | Sélectionnez **CreatePrivLinkService-rg** |
    | **Détails de l’instance** |                                                                 |
    | Nom             | Entrez **myVNet**                                    |
    | Région           | Sélectionnez **USA Est 2** |

3. Sélectionnez l’onglet **Adresses IP**, ou sélectionnez le bouton **Suivant : Adresses IP** au bas de la page.

4. Sous l’onglet **Adresses IP**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Espace d’adressage IPv4 | Entrez **10.1.0.0/16** |

5. Sous **Nom de sous-réseau**, sélectionnez le mot **par défaut**.

6. Dans **Modifier le sous-réseau**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Nom du sous-réseau | Entrez **mySubnet** |
    | Plage d’adresses de sous-réseau | Entrez **10.1.0.0/24** |

7. Sélectionnez **Enregistrer**.

8. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton **Vérifier + créer**.

9. Sélectionnez **Create** (Créer).

### <a name="create-a-standard-load-balancer"></a>Créer un équilibreur de charge standard

Utilisez le portail pour créer un équilibreur de charge interne standard. 

1. Dans l’angle supérieur gauche de l’écran, cliquez sur **Créer une ressource** > **Mise en réseau** > **Load Balancer**.

2. Dans l’onglet **Fonctions de base** de la page **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | Abonnement               | Sélectionnez votre abonnement.    |    
    | Resource group         | Sélectionnez **CreatePrivLinkService-rg** créé à l’étape précédente.|
    | Nom                   | Entrez **myLoadBalancer**                                   |
    | Région         | Sélectionnez **USA Est**.                                        |
    | Type          | sélectionnez **Interne**.                                        |
    | SKU           | sélectionnez **Standard**. |
    | Réseau virtuel | Sélectionnez **myVNet** créé à l’étape précédente. |
    | Subnet  | Sélectionnez **mySubnet** créé à l’étape précédente. |
    | Affectation d’adresses IP | Sélectionnez **Dynamique**. |
    | Zone de disponibilité | Sélectionnez **Redondant dans une zone**. |

3. Acceptez les valeurs par défaut pour les paramètres restants, puis sélectionnez **Vérifier + créer**.

4. Sous l’onglet **Review + create (Vérifier + créer)** , sélectionnez **Créer**.   

## <a name="create-load-balancer-resources"></a>Créer les ressources d’équilibreur de charge

Dans cette section, vous configurez :

* Les paramètres d’équilibreur de charge d’un pool d’adresses de back-ends
* Une sonde d’intégrité
* Une règle d’équilibreur de charge

### <a name="create-a-backend-pool"></a>Créer un pool principal

Un pool d’adresses de back-ends contient les adresses IP des cartes d’interface réseau virtuelles connectées à l’équilibreur de charge. 

Créez le pool d’adresses principal **myBackendPool** afin d’inclure des machines virtuelles pour l’équilibrage de charge du trafic Internet.

1. Sélectionnez **Tous les services** dans le menu de gauche, **Toutes les ressources**, puis **myLoadBalancer** dans la liste des ressources.

2. Sous **Paramètres**, sélectionnez **Pools principaux**, puis **Ajouter**.

3. Dans la page **Ajouter un pool de backends**, entrez **myBackendPool** comme nom de votre pool principal, puis sélectionnez **Ajouter**.

### <a name="create-a-health-probe"></a>Créer une sonde d’intégrité

L’équilibreur de charge supervise l’état de votre application avec une sonde d’intégrité. 

La sonde d’intégrité ajoute ou supprime des machines virtuelles dans l’équilibreur de charge en fonction de leur réponse aux contrôles d’intégrité. 

Créez une sonde d’intégrité nommée **myHealthProbe** pour surveiller l’intégrité des machines virtuelles.

1. Sélectionnez **Tous les services** dans le menu de gauche, **Toutes les ressources**, puis **myLoadBalancer** dans la liste des ressources.

2. Sous **Paramètres**, sélectionnez **Sondes d’intégrité**, puis **Ajouter**.
    
    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **MyHealthProbe**. |
    | Protocol | Sélectionnez **TCP**. |
    | Port | Entrez **80**.|
    | Intervalle | Entrez **15** pour **l’intervalle** en secondes entre les tentatives de la sonde. |
    | Seuil de défaillance sur le plan de l’intégrité | Sélectionnez **2** pour le **Seuil de défaillance sur le plan de l’intégrité**, soit le nombre d’échecs de sonde consécutifs qui peuvent se produire avant qu’une machine virtuelle soit considérée comme non saine.|
    | | |

3. Laissez les autres valeurs par défaut et sélectionnez **OK**.

### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge

Une règle d’équilibrage de charge est utilisée pour définir la distribution du trafic vers les machines virtuelles. Vous définissez la configuration IP front-end pour le trafic entrant et le pool d’adresses IP de back-ends pour la réception du trafic. Les ports source et de destination sont définis dans la règle. 

Dans cette section, vous allez créer une règle d’équilibreur de charge :

* A pour nom **myHTTPRule**.
* Se trouve dans le front-end nommé **LoadBalancerFrontEnd**.
* Écoute sur le **Port 80**.
* Dirige le trafic à charge équilibrée vers le back-end nommé **myBackendPool** sur le **Port 80**.

1. Sélectionnez **Tous les services** dans le menu de gauche, **Toutes les ressources**, puis **myLoadBalancer** dans la liste des ressources.

2. Sous **Paramètres**, sélectionnez **Règles d’équilibrage de charge**, puis **Ajouter**.

3. Pour configurer la règle d’équilibrage de charge, utilisez les valeurs suivantes :
    
    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **MyHTTPRule**. |
    | Version de l’adresse IP | Sélectionnez **IPv4** |
    | Adresse IP du serveur frontal | Sélectionnez **LoadBalancerFrontEnd** |
    | Protocol | Sélectionnez **TCP**. |
    | Port | Entrez **80**.|
    | Port principal | Entrez **80**. |
    | Pool principal | Sélectionnez **MyBackendPool**.|
    | Sonde d’intégrité | Sélectionnez **myHealthProbe**. |
    | Délai d’inactivité (minutes) | Déplacez le curseur sur **15 minutes**. |
    | Réinitialisation du protocole TCP | Sélectionnez **Enabled**. |

4. Laissez les autres valeurs par défaut, puis sélectionnez **OK**.

## <a name="create-a-private-link-service"></a>Créer un service Liaison privée

Dans cette section, vous allez créer un service Private Link derrière un équilibreur de charge standard.

1. En haut à gauche de la page dans le portail Azure, sélectionnez **Créer une ressource**.

2. Recherchez **Private Link** dans la zone **Rechercher dans la Place de marché**.

3. Sélectionnez **Create** (Créer).

4. Dans **Vue d’ensemble** sous **Centre Private Link**, sélectionnez le bouton bleu **Créer un service de liaison privée**.

5. Sous l’onglet **De base** sous **Créer un service de liaison privée**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |  |
    | Abonnement | Sélectionnez votre abonnement. |
    | Groupe de ressources | Sélectionnez **CreatePrivLinkService-rg**. |
    | **Détails de l’instance** |  |
    | Nom | Entrez **myPrivateLinkService**. |
    | Région | Sélectionnez **USA Est**. |

6. Sélectionnez l’onglet **Paramètres sortants** ou sélectionnez **Suivant : Paramètres sortants** en bas de la page.

7. Sous l’onglet **Paramètres sortants**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Équilibrage de charge | Sélectionnez **myLoadBalancer**. |
    | Adresse IP front-end de l’équilibreur de charge | Sélectionnez **LoadBalancerFrontEnd (10.1.0.4)** . |
    | Sous-réseau NAT source | Sélectionnez **mySubnet (10.1.0.0/24)** . |
    | Activer le proxy TCP V2 | Conservez la valeur par défaut **Non**. </br> Si votre application attend un en-tête de proxy TCP v2, sélectionnez **Oui**. |
    | **Paramètres d’adresse IP privée** |  |
    | Conservez les paramètres par défaut. |  |

8. Sélectionnez l’onglet **Sécurité d’accès** ou sélectionnez **Suivant : Sécurité d’accès** en bas de la page.

9. Conservez la valeur par défaut **Contrôle d’accès en fonction du rôle uniquement** sous l’onglet **Sécurité d’accès**.

10. Sélectionnez l’onglet **Étiquettes** ou sélectionnez **Suivant : Étiquettes** au bas de la page.

11. Sélectionnez l’onglet **Vérifier + créer** ou sélectionnez **Suivant : Vérifier + créer** en bas de la page.

12. Sélectionnez **Créer** sous l’onglet **Vérifier + créer**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous avez terminé d’utiliser le service Private Link, supprimez le groupe de ressources pour nettoyer les ressources utilisées dans ce guide de démarrage rapide.

1. Entrez **CreatePrivLinkService-rg** dans la zone de recherche en haut du portail, puis sélectionnez **CreatePrivLinkService-rg** dans les résultats de la recherche.
1. Sélectionnez **Supprimer le groupe de ressources**.
1. Dans **ENTREZ LE NOM DU GROUPE DE RESSOURCES**, tapez **CreatePrivLinkService-rg**.
1. Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous :

* Avez créé un réseau virtuel et un Azure Load Balancer interne.
* Avez créé un service de liaison privée.

Pour en savoir plus sur le point de terminaison privé Azure, passez à :
> [!div class="nextstepaction"]
> [Démarrage rapide : Créer un point de terminaison privé en utilisant le portail Azure](create-private-endpoint-portal.md)