---
title: 'Démarrage rapide : Créer un équilibreur de charge interne - Portail Azure'
titleSuffix: Azure Load Balancer
description: Ce guide de démarrage rapide montre comment créer un équilibreur de charge interne en utilisant le portail Azure.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.date: 08/09/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 9c8f46bf615b0d0c9fe8289057a8244a1d57185d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732798"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Démarrage rapide : Créer un équilibreur de charge interne pour équilibrer la charge de machines virtuelles en utilisant le portail Azure

Découvrez comment démarrer avec Azure Load Balancer en utilisant le portail Azure pour créer un équilibreur de charge interne et trois machines virtuelles.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

---

# <a name="standard-sku"></a>[**Référence Standard**](#tab/option-1-create-internal-load-balancer-standard)

>[!NOTE]
>Il est recommandé de disposer d’un équilibreur de charge de référence SKU Standard pour les charges de travail de production.  Pour plus d’informations sur les références (SKU), consultez **[Références SKU Azure Load Balancer](skus.md)** .

Quand vous créez un équilibreur de charge interne, un réseau virtuel est configuré comme réseau pour l’équilibreur de charge. 

Une adresse IP privée dans le réseau virtuel est configurée comme front-end (nommé **LoadBalancerFrontend** par défaut) pour l’équilibreur de charge. 

L’adresse IP du front-end peut être **statique** ou **dynamique**.

## <a name="create-the-virtual-network"></a>Créer un réseau virtuel

Dans cette section, vous allez créer un réseau virtuel et un sous-réseau.

1. Dans la zone de recherche située en haut du portail, entrez **Réseau virtuel**. Sélectionnez **Réseaux virtuels** dans les résultats de la recherche.

2. Dans **Réseaux virtuels**, sélectionnez **+ Créer**.

3. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Sélectionner votre abonnement Azure                                  |
    | Groupe de ressources   | Sélectionnez **Créer nouveau**. </br> Dans **Nom**, entrez **CreatePubLBQS-rg**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |                                                                 |
    | Nom             | Entrez **myVNet**                                    |
    | Région           | Sélectionnez **(Europe) Europe Ouest** |

4. Sélectionnez l’onglet **Adresses IP**, ou sélectionnez le bouton **Suivant : Adresses IP** au bas de la page.

5. Sous l’onglet **Adresses IP**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Espace d’adressage IPv4 | Entrez **10.1.0.0/16** |

6. Sous **Nom de sous-réseau**, sélectionnez le mot **par défaut**.

7. Dans **Modifier le sous-réseau**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Nom du sous-réseau | Entrez **myBackendSubnet** |
    | Plage d’adresses de sous-réseau | Entrez **10.1.0.0/24** |

8. Sélectionnez **Enregistrer**.

9. Sélectionnez l'onglet **Sécurité** .

10. Sous **BastionHost**, sélectionnez **Activer**. Entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Nom du bastion | Entrez **myBastionHost** |
    | Espace d’adressage AzureBastionSubnet | Entrer **10.1.1.0/27** |
    | Adresse IP publique | Sélectionnez **Créer nouveau**. </br> Pour **Nom**, entrez **myBastionIP**. </br> Sélectionnez **OK**. |

11. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton **Vérifier + créer**.

12. Sélectionnez **Create** (Créer).

## <a name="create-nat-gateway"></a>Créer une passerelle NAT

Dans cette section, vous allez créer une passerelle NAT pour l’accès Internet sortant des ressources dans le réseau virtuel. 

1. Dans la zone de recherche située en haut du portail, entrez **Passerelle NAT**. Sélectionnez **Passerelle NAT** dans les résultats de la recherche.

2. Dans **Passerelles NAT**, sélectionnez **+ Créer**.

3. Dans **Créer une passerelle NAT (traduction d’adresses réseau)** , entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **CreateIntLBQS-rg**. |
    | **Détails de l’instance** |    |
    | Nom de la passerelle NAT | Entrez **myNATgateway**. |
    | Zone de disponibilité | Sélectionnez **Aucun**. |
    | Délai d’inactivité (minutes) | Entrez **15**. |

4. Sélectionnez l’onglet **IP sortante** ou le bouton **Suivant : IP sortante** situé en bas de la page.

5. Dans **IP sortante**, sélectionnez **Créer une nouvelle adresse IP publique** à côté d’**Adresses IP publiques**.

6. Entrez **myNATgatewayIP** pour **Nom** dans **Ajouter une adresse IP publique**.

7. Sélectionnez **OK**.

8. Sélectionnez l’onglet **Sous-réseau** ou le bouton **Suivant : Sous-réseau** situé en bas de la page.

9. Dans **Réseau virtuel**, dans l’onglet **Sous-réseau**, sélectionnez **myVNet**.

10. Sélectionnez **myBackendSubnet** sous **Nom du sous-réseau**.

11. Sélectionnez le bouton bleu **Vérifier + créer** en bas de la page, ou l’onglet **Vérifier + créer**.

12. Sélectionnez **Create** (Créer).

## <a name="create-load-balancer"></a><a name="create-load-balancer-resources"></a> Créer un équilibreur de charge

Dans cette section, vous créez un équilibreur de charge qui équilibre la charge des machines virtuelles.

Lors de la création de l’équilibreur de charge, vous allez configurer les éléments suivants :

* Adresse IP du serveur frontal
* Pool de back-ends
* Règles d’équilibrage de charge du trafic entrant

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

2. Dans la page **Équilibreur de charge**, sélectionnez **Créer**.

3. Dans l’onglet **Fonctions de base** de la page **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | **Détails du projet** |   |
    | Abonnement               | Sélectionnez votre abonnement.    |    
    | Resource group         | Sélectionnez **CreateIntLBQS-rg**. |
    | **Détails de l’instance** |   |
    | Nom                   | Entrez **myLoadBalancer**                                   |
    | Région         | Sélectionnez **(Europe) Europe Ouest**.                                        |
    | Type          | sélectionnez **Interne**.                                        |
    | SKU           | Conservez la valeur par défaut **Standard**. |

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-standard-internal-load-balancer.png" alt-text="Capture d’écran de l’onglet Créer des informations de base de l’équilibreur de charge standard." border="true":::

4. Sélectionnez **Suivant : configuration de l’adresse IP front-end** au bas de la page.

5. Dans **Configuration de l’adresse IP front-end**, sélectionnez **+ Ajouter une adresse IP front-end**.

6. Entrez **LoadBalancerFrontend** dans **Nom**.

7. Sélectionnez **myBackendSubnet** dans **Sous-réseau**.

8. Sélectionnez **Dynamique** comme **Attribution**.

9. Sélectionnez **Redondant interzone** dans la **Zone de disponibilité**.

    > [!NOTE]
    > Dans les régions avec [Zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), vous avez la possibilité de sélectionner aucune zone (option par défaut), une zone spécifique ou redondant interzone. Le choix dépendra de vos exigences spécifiques en matière de défaillance de domaine. Dans les régions sans Zones de disponibilité, ce champ n’apparaît pas. </br> Pour plus d’informations sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](../availability-zones/az-overview.md).

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
    | Nom | Entrez **myHTTPRule** |
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

    > [!NOTE]
    > Dans cet exemple, vous avez créé une passerelle NAT pour fournir un accès Internet sortant. L’onglet Règles de trafic sortant dans la configuration est ignoré et n’est pas nécessaire avec la passerelle NAT. Pour plus d’informations sur la passerelle NAT Azure, consultez [Présentation de NAT de réseau virtuel Azure ?](../virtual-network/nat-gateway/nat-overview.md)
    > Pour plus d’informations sur les connexions sortantes dans Azure, consultez [SNAT (Traduction d'adresses réseau source) pour les connexions sortantes](../load-balancer/load-balancer-outbound-connections.md)

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Dans cette section, vous allez créer trois machines virtuelles (**myVM1**, **myVM2** et **myVM3**) dans trois zones différentes (**Zone 1**, **Zone 2** et **Zone 3**). 

Ces machines virtuelles sont ajoutées au pool de back-ends de l’équilibreur de charge créé auparavant.

1. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

2. Dans **Machines virtuelles**, sélectionnez **+ Créer** > **Machine virtuelle**.
   
3. Dans **Créer une machine virtuelle**, entrez ou sélectionnez les valeurs sous l’onglet **Informations de base** :

    | Paramètre | Valeur                                          |
    |-----------------------|----------------------------------|
    | **Détails du projet** |  |
    | Abonnement | Sélectionner votre abonnement Azure |
    | Groupe de ressources | Sélectionnez **CreateIntLBQS-rg**. |
    | **Détails de l’instance** |  |
    | Nom de la machine virtuelle | Entrez **myVM1** |
    | Région | Sélectionnez **(Europe) Europe Ouest** |
    | Options de disponibilité | Sélectionnez **Zones de disponibilité** |
    | Zone de disponibilité | Sélectionnez **1** |
    | Image | Sélectionnez **Windows Server 2019 Datacenter - Gen1** |
    | Instance Azure Spot | Conservez la valeur par défaut (case non cochée). |
    | Taille | Choisissez la taille de la machine virtuelle ou acceptez le paramètre par défaut |
    | **Compte administrateur** |  |
    | Nom d’utilisateur | Entrez un nom d’utilisateur |
    | Mot de passe | Entrez un mot de passe |
    | Confirmer le mot de passe | Entrez à nouveau le mot de passe |
    | **Règles des ports d’entrée** |  |
    | Aucun port d’entrée public | Sélectionnez **Aucun** |

4. Sélectionnez l'onglet **Mise en réseau** ou choisissez **Suivant : Disques**, puis **Suivant : Mise en réseau**.
  
5. Sous l’onglet Réseau, sélectionnez ou entrez :

    | Paramètre | Valeur |
    |-|-|
    | **Interface réseau** |  |
    | Réseau virtuel | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Adresse IP publique | Sélectionnez **Aucun**. |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **Avancé**|
    | Configurer un groupe de sécurité réseau | Sélectionnez **Créer nouveau**. </br> Dans **Créer un groupe de sécurité réseau**, entrez **myNSG** dans **Nom**. </br> Sous **Règles de trafic entrant**, sélectionnez **+Ajouter une règle de trafic entrant**. </br> Sous **Plages de ports de destination**, entrez **80**. </br> Sous **Priorité**, entrez **100**. </br> Dans **Nom**, entrez **myNSGRule** </br> Sélectionnez **Ajouter** </br> Sélectionnez **OK**. |
    | **Équilibrage de charge**  |
    | Placer cette machine virtuelle derrière une solution d’équilibrage de charge existante ? | Sélectionnez la zone. |
    | **Paramètres d’équilibrage de charge** |
    | Options d’équilibrage de charge | Sélectionnez **Équilibrage de charge Azure** |
    | Sélectionnez un équilibreur de charge | Sélectionnez **myLoadBalancer**  |
    | Sélectionnez un pool principal | Sélectionnez **myBackendPool** |
   
6. Sélectionnez **Revoir + créer**. 
  
7. Passez en revue les paramètres, puis sélectionnez **Créer**.

8. Suivez les étapes 1 à 7 pour créer deux machines virtuelles supplémentaires avec les valeurs suivantes et tous les autres paramètres identiques à ceux de **myVM1** :

    | Paramètre | Machine virtuelle 2| Machine virtuelle 3|
    | ------- | ----- |---|
    | Nom |  **myVM2** |**myVM3**|
    | Zone de disponibilité | **2** |**3**|
    | Groupe de sécurité réseau | Sélectionnez le groupe **myNSG** existant| Sélectionnez le groupe **myNSG** existant|

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

# <a name="basic-sku"></a>[**Référence De base**](#tab/option-2-create-load-balancer-basic)

>[!NOTE]
>Il est recommandé de disposer d’un équilibreur de charge de référence SKU Standard pour les charges de travail de production.  Pour plus d’informations sur les références (SKU), consultez **[Références SKU Azure Load Balancer](skus.md)** .

## <a name="create-the-virtual-network"></a>Créer un réseau virtuel

Dans cette section, vous allez créer un réseau virtuel et un sous-réseau.

1. Dans la zone de recherche située en haut du portail, entrez **Réseau virtuel**. Sélectionnez **Réseaux virtuels** dans les résultats de la recherche.

2. Dans **Réseaux virtuels**, sélectionnez **+ Créer**.

3. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Sélectionner votre abonnement Azure                                  |
    | Groupe de ressources   | Sélectionnez **Créer nouveau**. </br> Dans **Nom**, entrez **CreatePubLBQS-rg**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |                                                                 |
    | Nom             | Entrez **myVNet**                                    |
    | Région           | Sélectionnez **(Europe) Europe Ouest** |

4. Sélectionnez l’onglet **Adresses IP**, ou sélectionnez le bouton **Suivant : Adresses IP** au bas de la page.

5. Sous l’onglet **Adresses IP**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Espace d’adressage IPv4 | Entrez **10.1.0.0/16** |

6. Sous **Nom de sous-réseau**, sélectionnez le mot **par défaut**.

7. Dans **Modifier le sous-réseau**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Nom du sous-réseau | Entrez **myBackendSubnet** |
    | Plage d’adresses de sous-réseau | Entrez **10.1.0.0/27** |

8. Sélectionnez **Enregistrer**.

9. Sélectionnez l'onglet **Sécurité** .

10. Sous **BastionHost**, sélectionnez **Activer**. Entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Nom du bastion | Entrez **myBastionHost** |
    | Espace d’adressage AzureBastionSubnet | Entrez **10.1.1.0/24** |
    | Adresse IP publique | Sélectionnez **Créer nouveau**. </br> Pour **Nom**, entrez **myBastionIP**. </br> Sélectionnez **OK**. |

11. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton **Vérifier + créer**.

12. Sélectionnez **Create** (Créer).

## <a name="create-load-balancer"></a>Créer un équilibreur de charge

Dans cette section, vous créez un équilibreur de charge qui équilibre la charge des machines virtuelles.

Lors de la création de l’équilibreur de charge, vous allez configurer les éléments suivants :

* Adresse IP du serveur frontal
* Pool de back-ends
* Règles d’équilibrage de charge du trafic entrant

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

2. Dans la page **Équilibreur de charge**, sélectionnez **Créer**.

3. Dans l’onglet **Fonctions de base** de la page **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | **Détails du projet** |   |
    | Abonnement               | Sélectionnez votre abonnement.    |    
    | Resource group         | Sélectionnez **CreateIntLBQS-rg**. |
    | **Détails de l’instance** |   |
    | Nom                   | Entrez **myLoadBalancer**                                   |
    | Région         | Sélectionnez **(Europe) Europe Ouest**.                                        |
    | Type          | sélectionnez **Interne**.                                        |
    | SKU           | Sélectionnez **De base**. |

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-basic-internal-load-balancer.png" alt-text="Capture d’écran de l’onglet Créer des informations de base de l’équilibreur de charge standard." border="true":::

4. Sélectionnez **Suivant : configuration de l’adresse IP front-end** au bas de la page.

5. Dans **Configuration de l’adresse IP front-end**, sélectionnez **+ Ajouter une adresse IP front-end**.

6. Entrez **LoadBalancerFrontend** dans **Nom**.

7. Sélectionnez **myBackendSubnet** dans **Sous-réseau**.

8. Sélectionnez **Dynamique** comme **Attribution**.

9. Sélectionnez **Ajouter**.

10. Sélectionnez **Suivant : Pools de back-end** au bas de la page.

11. Sous l’onglet **Pools de back-end**, sélectionnez **+ Ajouter un pool de back-end**.

12. Entrez **myBackendPool** comme **Nom** dans **Ajouter un pool de back-end**.

13. Sélectionnez **Machines virtuelles** dans **Associé à**.

14. Sélectionnez **IPv4** ou **IPv6** pour la **Version IP**.

15. Sélectionnez **Ajouter**.

16. Sélectionnez le bouton **Suivant : Règles de trafic entrant** au bas de la page.

17. Dans **Règle d’équilibrage de la charge** sous l’onglet **Règles de trafic entrant**, sélectionnez **+ Ajouter une règle d’équilibrage de charge**.

18. Dans **Ajouter une règle d’équilibrage de charge**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **myHTTPRule** |
    | Version de l’adresse IP | Sélectionnez **IPv4** ou **IPv6** en fonction de vos besoins. |
    | Adresse IP du serveur frontal | Sélectionnez **LoadBalancerFrontend**. |
    | Protocol | Sélectionnez **TCP**. |
    | Port | Entrez **80**. |
    | Port principal | Entrez **80**. |
    | Pool principal | Sélectionnez **MyBackendPool**. |
    | Sonde d’intégrité | Sélectionnez **Créer nouveau**. </br> Dans **Nom**, entrez **myHealthProbe**. </br> Sélectionnez **HTTP** dans **Protocole**. </br> Laissez les autres valeurs par défaut et sélectionnez **OK**. |
    | Persistance de session | Sélectionnez **Aucun**. |
    | Délai d’inactivité (minutes) | Entrez ou sélectionnez **15**. |
    | IP flottante | Sélectionnez **Désactivé**. |

19. Sélectionnez **Ajouter**.

20. Sélectionnez le bouton bleu **Vérifier + créer** au bas de la page.

21. Sélectionnez **Create** (Créer).

    > [!NOTE]
    > Dans cet exemple, nous avons créé une passerelle NAT pour fournir un accès Internet sortant. L’onglet Règles de trafic sortant dans la configuration est ignoré et n’est pas nécessaire avec la passerelle NAT. Pour plus d’informations sur la passerelle NAT Azure, consultez [Présentation de NAT de réseau virtuel Azure ?](../virtual-network/nat-gateway/nat-overview.md)
    > Pour plus d’informations sur les connexions sortantes dans Azure, consultez [SNAT (Traduction d'adresses réseau source) pour les connexions sortantes](../load-balancer/load-balancer-outbound-connections.md)

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Dans cette section, vous allez créer trois machines virtuelles (**myVM1**, **myVM2** et **myVM3**).

Les trois machines virtuelles vont être ajoutées à un groupe à haute disponibilité nommé **myAvailabilitySet**.

1. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

2. Dans **Machines virtuelles**, sélectionnez **+ Créer** > **Machine virtuelle**.
   
3. Dans **Créer une machine virtuelle**, tapez ou sélectionnez les valeurs sous l’onglet **De base** :

    | Paramètre | Valeur                                          |
    |-----------------------|----------------------------------|
    | **Détails du projet** |  |
    | Abonnement | Sélectionner votre abonnement Azure |
    | Groupe de ressources | Sélectionnez **CreateIntLBQS-rg**. |
    | **Détails de l’instance** |  |
    | Nom de la machine virtuelle | Entrez **myVM1** |
    | Région | Sélectionnez **(Europe) Europe Ouest** |
    | Options de disponibilité | Sélectionnez **Groupe à haute disponibilité** |
    | Groupe à haute disponibilité | Sélectionnez **Créer nouveau**. </br> Entrez **myAvailabilitySet** dans **Nom**. </br> Sélectionnez **OK**. |
    | Image | **Windows Server 2019 Datacenter – Gen 1** |
    | Instance Azure Spot | Conservez la valeur par défaut (case non cochée). |
    | Taille | Choisissez la taille de la machine virtuelle ou acceptez le paramètre par défaut |
    | **Compte administrateur** |  |
    | Nom d’utilisateur | Entrez un nom d’utilisateur |
    | Mot de passe | Entrez un mot de passe |
    | Confirmer le mot de passe | Entrez à nouveau le mot de passe |
    | **Règles des ports d’entrée** |   |
    | Aucun port d’entrée public | Sélectionnez **Aucun**. |

4. Sélectionnez l'onglet **Mise en réseau** ou choisissez **Suivant : Disques**, puis **Suivant : Mise en réseau**.
  
5. Sous l’onglet Réseau, sélectionnez ou entrez :

    | Paramètre | Valeur |
    |-|-|
    | **Interface réseau** |  |
    | Réseau virtuel | Sélectionnez **myVNet** |
    | Subnet | Sélectionnez **myBackendSubnet** |
    | Adresse IP publique | Sélectionnez **Aucun** |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **Avancé**|
    | Configurer un groupe de sécurité réseau | Sélectionnez **Créer nouveau**. </br> Dans **Créer un groupe de sécurité réseau**, entrez **myNSG** dans **Nom**. </br> Sous **Règles de trafic entrant**, sélectionnez **+Ajouter une règle de trafic entrant**. </br> Sous **Plages de ports de destination**, entrez **80**. </br> Sous **Priorité**, entrez **100**. </br> Dans **Nom**, entrez **myNSGRule** </br> Sélectionnez **Ajouter** </br> Sélectionnez **OK**. |
    | **Équilibrage de charge**  |
    | Placer cette machine virtuelle derrière une solution d’équilibrage de charge existante ? | Sélectionner la boîte |
    | **Paramètres d’équilibrage de charge** |   |
    | Options d’équilibrage de charge | Sélectionnez **Équilibreur de charge Azure**. |
    | Sélectionnez un équilibreur de charge | Sélectionnez **myLoadBalancer**>. |
    | Sélectionnez un pool principal | Sélectionnez **MyBackendPool**. |
 
6. Sélectionnez **Revoir + créer**. 
  
7. Passez en revue les paramètres, puis sélectionnez **Créer**.

8. Suivez les étapes 1 à 7 pour créer deux machines virtuelles supplémentaires avec les valeurs suivantes et tous les autres paramètres identiques à ceux de **myVM1** :

    | Paramètre | Machine virtuelle 2 | Machine virtuelle 3 |
    | ------- | ----- |---|
    | Nom |  **myVM2** |**myVM3**|
    | Groupe à haute disponibilité| Sélectionnez **myAvailabilitySet** | Sélectionnez **myAvailabilitySet**|
    | Groupe de sécurité réseau | Sélectionnez le groupe **myNSG** existant| Sélectionnez le groupe **myNSG** existant|

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]
---

## <a name="create-test-virtual-machine"></a>Créer une machine virtuelle de test

Dans cette section, vous allez créer une machine virtuelle nommée **myTestVM**.  Cette machine virtuelle sera utilisée pour tester la configuration de l’équilibreur de charge.

1. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

2. Dans **Machines virtuelles**, sélectionnez **+ Créer** > **Machine virtuelle**.
   
2. Dans **Créer une machine virtuelle**, tapez ou sélectionnez les valeurs sous l’onglet **De base** :

    | Paramètre | Valeur                                          |
    |-----------------------|----------------------------------|
    | **Détails du projet** |  |
    | Abonnement | Sélectionner votre abonnement Azure |
    | Groupe de ressources | Sélectionnez **CreateIntLBQS-rg**. |
    | **Détails de l’instance** |  |
    | Nom de la machine virtuelle | Entrez **myTestVM** |
    | Région | Sélectionnez **(Europe) Europe Ouest** |
    | Options de disponibilité | Sélectionnez **Aucune redondance d’infrastructure requise**. |
    | Image | Sélectionnez **Windows Server 2019 Datacenter** |
    | Instance Azure Spot | Conservez la valeur par défaut ou la case non cochée. |
    | Taille | Choisissez la taille de la machine virtuelle ou acceptez le paramètre par défaut |
    | **Compte administrateur** |  |
    | Nom d’utilisateur | Entrez un nom d’utilisateur |
    | Mot de passe | Entrez un mot de passe |
    | Confirmer le mot de passe | Entrez à nouveau le mot de passe |
    | **Règles des ports d’entrée** |   |
    | Aucun port d’entrée public | Sélectionnez **Aucun**. |

3. Sélectionnez l'onglet **Mise en réseau** ou choisissez **Suivant : Disques**, puis **Suivant : Mise en réseau**.
  
4. Sous l’onglet **Mise en réseau**, sélectionnez ou entrez :

    | Paramètre | Valeur |
    |-|-|
    | **Interface réseau** |  |
    | Réseau virtuel | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Adresse IP publique | Sélectionnez **Aucun**. |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **Avancé** |
    | Configurer un groupe de sécurité réseau | Sélectionnez **MyNSG** créé à l’étape précédente.|
       
5. Sélectionnez **Revoir + créer**. 
  
6. Passez en revue les paramètres, puis sélectionnez **Créer**.

## <a name="install-iis"></a>Installer IIS

1. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

2. Sélectionnez **myVM1**.

3. Sur la page **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Bastion**.

4. Sélectionnez **Utiliser Bastion**. 

5. Entrez le nom d’utilisateur et le mot de passe saisis pendant la création de la machine virtuelle.

6. Sélectionnez **Connecter**.

7. Sur le bureau du serveur, accédez à **Outils d’administration Windows** > **Windows PowerShell** > **Windows PowerShell**.

8. Dans la fenêtre PowerShell, exécutez les commandes suivantes pour :

    * installer le serveur IIS.
    * supprimer le fichier iisstart.htm par défaut.
    * ajouter un nouveau fichier iisstart.htm qui affiche le nom de la machine virtuelle.

   ```powershell
    
    # Install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    Remove-Item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```

9. Fermez la session Bastion avec **myVM1**.

10. Répétez les étapes 1 à 9 pour installer IIS et le fichier iisstart.htm mis à jour sur **myVM2** et **myVM3**.

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge

Dans cette section, vous allez tester l’équilibreur de charge en vous connectant à **myTestVM** et en vérifiant la page web.

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

2. Sélectionnez **myLoadBalancer**.

3. Notez ou copiez l’adresse en regard de **Adresse IP privée** dans la **Vue d’ensemble** de **myLoadBalancer**.

4. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

5. Sélectionnez **myTestVM**.

6. Sur la page **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Bastion**.

7. Sélectionnez **Utiliser Bastion**.

8. Entrez le nom d’utilisateur et le mot de passe saisis pendant la création de la machine virtuelle.

9. Ouvrez **Internet Explorer** sur **myTestVM**.

10. Entrez l’adresse IP de l’étape précédente dans la barre d’adresse du navigateur. La page personnalisée affichant l’un des noms de serveurs principaux s’affiche dans le navigateur.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="La capture d’écran présente une fenêtre de navigateur qui affiche la page personnalisée, comme prévu." border="true":::
   
Pour consulter la distribution de trafic de l’équilibreur de charge sur toutes les machines virtuelles, vous pouvez forcer l’actualisation de votre navigateur web depuis l’ordinateur client.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, l’équilibreur de charge et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources **CreateIntLBQS-rg** qui contient les ressources, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous :

* Avez créé un équilibreur de charge Azure Standard ou De base
* Avez attaché 3 machines virtuelles à l’équilibreur de charge
* Avez configuré la règle de trafic de l’équilibreur de charge, configuré la sonde d’intégrité, puis testé l’équilibreur de charge 

Pour en savoir plus sur Azure Load Balancer, consultez :
> [!div class="nextstepaction"]
> [Qu’est-ce que Azure Load Balancer ?](load-balancer-overview.md)