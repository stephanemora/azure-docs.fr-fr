---
title: Démarrage rapide - Créer un service Private Link avec le portail Azure
titlesuffix: Azure Private Link
description: Découvrez dans ce guide de démarrage rapide comment créer un service Private Link en utilisant le portail Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 08/18/2021
ms.author: allensu
ms.openlocfilehash: de7d106ae02e1150d9765e60fbfdf0dc0cb9bc74
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124810316"
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

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. En haut à gauche de l’écran, sélectionnez **Créer une ressource > Réseau > Réseau virtuel**, ou recherchez **Réseau virtuel** à partir de la zone de recherche.

3. Sélectionnez **Create** (Créer). 

4. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Sélectionner votre abonnement Azure                                  |
    | Groupe de ressources   | Sélectionnez **Créer nouveau**. Enter **CreatePrivLinkService-rg**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |                                                                 |
    | Nom             | Entrez **myVNet**                                    |
    | Région           | Sélectionnez **(États-Unis) USA Est 2** |

5. Sélectionnez l’onglet **Adresses IP**, ou sélectionnez le bouton **Suivant : Adresses IP** au bas de la page.

6. Sous l’onglet **Adresses IP**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Espace d’adressage IPv4 | Entrez **10.1.0.0/16** |

7. Sous **Nom de sous-réseau**, sélectionnez le mot **par défaut**.

8. Dans **Modifier le sous-réseau**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Nom du sous-réseau | Entrez **myBackendSubnet** |
    | Plage d’adresses de sous-réseau | Entrez **10.1.0.0/24** |

9. Sélectionnez **Enregistrer**.

10. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton **Vérifier + créer**.

11. Sélectionnez **Create** (Créer).

### <a name="create-nat-gateway"></a>Créer une passerelle NAT

Dans cette section, vous allez créer une passerelle NAT et l’affecter au sous-réseau dans le réseau virtuel que vous avez créé précédemment.

1. En haut à gauche de l’écran, sélectionnez **Créer une ressource > Réseau > Passerelle NAT**, ou recherchez **Passerelle NAT** dans la zone de recherche.

2. Sélectionnez **Create** (Créer). 

3. Dans **Créer une passerelle NAT (traduction d’adresses réseau)** , entrez ou sélectionnez les informations suivantes sous l’onglet **Informations de base** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Sélectionnez votre abonnement Azure.                                  |
    | Groupe de ressources   | Sélectionnez **CreatePrivLinkService-rg**. |
    | **Détails de l’instance** |                                                                 |
    | Nom             | Entrez **myNATGateway**                                    |
    | Région           | Sélectionnez **(États-Unis) USA Est 2**  |
    | Zone de disponibilité | Sélectionnez **Aucun**. |
    | Délai d’inactivité (minutes) | Entrez **10**. |

4. Sélectionnez l’onglet **IP sortante**, ou sélectionnez le bouton **Suivant : IP sortante** situé au bas de la page.

5. Sous l’onglet **IP sortante**, entrez ou sélectionnez les informations suivantes :

    | **Paramètre** | **Valeur** |
    | ----------- | --------- |
    | Adresses IP publiques | Sélectionnez **Créer une adresse IP publique**. </br> Dans **Nom**, entrez **myNATgatewayIP**. </br> Sélectionnez **OK**. |

6. Sélectionnez l’onglet **Sous-réseau**, ou sélectionnez le bouton **Suivant : Sous-réseau** situé au bas de la page.

7. Sous l’onglet **Sous-réseau**, sélectionnez **myVNet** dans la zone de liste déroulante **Réseau virtuel**.

8. Cochez la case en regard de **myBackendSubnet**.

9. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.

10. Sélectionnez **Create** (Créer).

### <a name="create-load-balancer"></a>Créer un équilibreur de charge

Dans cette section, vous créez un équilibreur de charge qui équilibre la charge des machines virtuelles.

Lors de la création de l’équilibreur de charge, vous allez configurer les éléments suivants :

* Adresse IP du serveur frontal
* Pool de back-ends
* Règles d’équilibrage des charges entrantes

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

2. Dans la page **Équilibreur de charge**, sélectionnez **Créer**.

3. Dans l’onglet **Fonctions de base** de la page **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | **Détails du projet** |   |
    | Abonnement               | Sélectionnez votre abonnement.    |    
    | Resource group         | Sélectionnez **CreatePrivLinkService-rg**. |
    | **Détails de l’instance** |   |
    | Nom                   | Entrez **myLoadBalancer**                                   |
    | Région         | Sélectionnez **(États-Unis) USA Est 2**.                                        |
    | Type          | sélectionnez **Interne**.                                        |
    | SKU           | Conservez la valeur par défaut **Standard**. |

4. Sélectionnez **Suivant : configuration de l’adresse IP front-end** au bas de la page.

5. Dans **Configuration de l’adresse IP front-end**, sélectionnez **+ Ajouter une adresse IP front-end**.

6. Entrez **LoadBalancerFrontend** dans **Nom**.

7. Sélectionnez **myBackendSubnet** dans **Sous-réseau**.

8. Sélectionnez **Dynamique** comme **Attribution**.

9. Sélectionnez **Zone-redondante** dans la **Zone de disponibilité**.

    > [!NOTE]
    > Dans les régions avec des [zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), vous avez la possibilité de sélectionner aucune zone (option par défaut), une zone spécifique ou redondant dans une zone. Le choix dépendra de vos exigences spécifiques en matière de défaillance de domaine. Dans les régions sans Zones de disponibilité, ce champ n’apparaît pas. </br> Pour plus d’informations sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](../availability-zones/az-overview.md).

10. Sélectionnez **Ajouter**.

11. Sélectionnez **Suivant : Pools de back-end** au bas de la page.

12. Sous l’onglet **Pools de back-end**, sélectionnez **+ Ajouter un pool de back-end**.

13. Entrez **myBackendPool** comme **Nom** dans **Ajouter un pool de back-end**.

14. Sélectionnez **Carte d’interface réseau** ou **Adresse IP** pour la **Configuration du pool de back-end**.

15. Sélectionnez **IPv4** ou **IPv6** pour la **Version IP**.

16. Sélectionnez **Ajouter**.

17. Sélectionnez le bouton **Suivant : Règles de trafic entrant** au bas de la page.

18. Dans **Règle d’équilibrage de la charge** sous l’onglet **Règles de trafic entrant**, sélectionnez **+ Ajouter une règle d’équilibrage de charge**.

19. Dans **Ajouter une règle d’équilibrage de charge**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **MyHTTPRule** |
    | Version de l’adresse IP | Sélectionnez **IPv4** ou **IPv6** en fonction de vos besoins. |
    | Adresse IP du serveur frontal | Sélectionnez **LoadBalancerFrontend**. |
    | Protocol | Sélectionnez **TCP**. |
    | Port | Entrez **80**. |
    | Port principal | Entrez **80**. |
    | Pool principal | Sélectionnez **MyBackendPool**. |
    | Sonde d’intégrité | Sélectionnez **Créer nouveau**. </br> Dans **Nom**, entrez **myHealthProbe**. </br> Sélectionnez **HTTP** dans **Protocole**. </br> Laissez les autres valeurs par défaut et sélectionnez **OK**. |
    | Persistance de session | Sélectionnez **Aucun**. |
    | Délai d’inactivité (minutes) | Entrez ou sélectionnez **15**. |
    | Réinitialisation du protocole TCP | Sélectionnez **Enabled**. |
    | IP flottante | Sélectionnez **Désactivé**. |

20. Sélectionnez **Ajouter**.

21. Sélectionnez le bouton bleu **Vérifier + créer** au bas de la page.

22. Sélectionnez **Create** (Créer).

## <a name="create-a-private-link-service"></a>Créer un service Private Link

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
    | Région | Sélectionnez **(États-Unis) USA Est 2**. |

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

Votre service de liaison privée est créé et peut recevoir du trafic. Si vous voulez voir les flux de trafic, configurez votre application derrière votre équilibreur de charge standard.

## <a name="create-private-endpoint"></a>Créer un point de terminaison privé

Dans cette section, vous allez mapper le service de liaison privée sur un point de terminaison privé. Un réseau virtuel contient le point de terminaison privé pour le service de liaison privée. Ce réseau virtuel contient les ressources qui auront accès à votre service de liaison privée.

### <a name="create-private-endpoint-virtual-network"></a>Créer un réseau virtuel de point de terminaison privé

1. En haut à gauche de l’écran, sélectionnez **Créer une ressource > Réseau > Réseau virtuel**, ou recherchez **Réseau virtuel** à partir de la zone de recherche.

2. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Sélectionner votre abonnement Azure                                  |
    | Groupe de ressources   | Sélectionnez **CreatePrivLinkService-rg** |
    | **Détails de l’instance** |                                                                 |
    | Nom             | Entrez **myVNetPE**.                                    |
    | Région           | Sélectionnez **(États-Unis) USA Est 2** |

3. Sélectionnez l’onglet **Adresses IP**, ou sélectionnez le bouton **Suivant : Adresses IP** au bas de la page.

4. Sous l’onglet **Adresses IP**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Espace d’adressage IPv4 | Entrez **11.1.0.0/16**. |

5. Sous **Nom de sous-réseau**, sélectionnez le mot **par défaut**.

6. Dans **Modifier le sous-réseau**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Nom du sous-réseau | Entrez **mySubnetPE**. |
    | Plage d’adresses de sous-réseau | Entrez **11.1.0.0/24**. |

7. Sélectionnez **Enregistrer**.

8. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton **Vérifier + créer**.

9. Sélectionnez **Create** (Créer).

### <a name="create-private-endpoint"></a>Créer un point de terminaison privé

1. En haut à gauche de l’écran du portail, sélectionnez **Créer une ressource** > **Mise en réseau** > **Liaison privée** ou entrez **Liaison privée** dans la zone de recherche.

2. Sélectionnez **Create** (Créer).

3. Dans **Centre de liaisons privées**, sélectionnez **Points de terminaison privés** dans le menu de gauche.

4. Dans **Points de terminaison privés**, sélectionnez **+ Ajouter**.

5. Sous l’onglet **Général** de **Créer un point de terminaison privé**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** | |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **CreatePrivLinkService-rg**. Vous avez créé ce groupe de ressources dans la section précédente.|
    | **Détails de l’instance** |  |
    | Nom  | Entrez **myPrivateEndpoint**. |
    | Région | Sélectionnez **(États-Unis) USA Est 2**. |

6. Sélectionnez l’onglet **Ressource** ou le bouton **Suivant : Ressource** en bas de la page.
    
7. Dans **Ressource**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Méthode de connexion | Sélectionnez **Se connecter à une ressource Azure dans mon répertoire**. |
    | Abonnement | Sélectionnez votre abonnement. |
    | Type de ressource | Sélectionnez **Microsoft.Network/privateLinkServices**. |
    | Ressource | Sélectionnez **myPrivateLinkService**. |

8. Sélectionnez l’onglet **Configuration** ou le bouton **Suivant : Configuration** en bas de l’écran.

9. Dans **Configuration**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Mise en réseau** |  |
    | Réseau virtuel | Sélectionnez **myVNetPE**. |
    | Subnet | Sélectionnez **mySubnetPE**. |

10. Sélectionnez l’onglet **Vérifier + créer** ou sélectionnez le bouton **Vérifier + créer** situé au bas de l’écran.

11. Sélectionnez **Create** (Créer).

### <a name="ip-address-of-private-endpoint"></a>Adresse IP du point de terminaison privé

Dans cette section, vous trouverez l’adresse IP du point de terminaison privé qui correspond à l’équilibreur de charge et au service de liaison privée.

1. Dans la colonne de gauche du portail Azure, sélectionnez **Groupes de ressources**.

2. Sélectionnez le groupe de ressources **CreatePrivLinkService-rg**.

3. Dans le groupe de ressources **CreatePrivLinkService-rg**, sélectionnez **myPrivateEndpoint**.

4. Dans la page **Vue d’ensemble** de **myPrivateEndpoint**, sélectionnez le nom de l’interface réseau qui est associée au point de terminaison privé.  Le nom de l’interface réseau commence par **myPrivateEndpoint.nic**.

5. Dans la page **Vue d’ensemble** de la carte réseau du point de terminaison privé, l’adresse IP du point de terminaison s’affiche dans **Adresse IP privée**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous avez terminé d’utiliser le service de liaison privée, supprimez le groupe de ressources afin de nettoyer les ressources utilisées dans ce guide de démarrage rapide.

1. Entrez **CreatePrivLinkService-rg** dans la zone de recherche en haut du portail, puis sélectionnez **CreatePrivLinkService-rg** dans les résultats de la recherche.

2. Sélectionnez **Supprimer le groupe de ressources**.

3. Dans **ENTREZ LE NOM DU GROUPE DE RESSOURCES**, tapez **CreatePrivLinkService-rg**.

4. Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous :

* Avez créé un réseau virtuel et un Azure Load Balancer interne.
* Avez créé un service de liaison privée.
* Avez créé un réseau virtuel et un point de terminaison privé pour le service de liaison privée.

Pour en savoir plus sur le point de terminaison privé Azure, passez à :
> [!div class="nextstepaction"]
> [Démarrage rapide : Créer un point de terminaison privé en utilisant le portail Azure](create-private-endpoint-portal.md)
