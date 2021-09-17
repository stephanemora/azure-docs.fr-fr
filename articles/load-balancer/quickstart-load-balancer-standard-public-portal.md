---
title: 'Démarrage rapide : Créer un équilibreur de charge public - Portail Azure'
titleSuffix: Azure Load Balancer
description: Ce guide de démarrage rapide montre comment créer un équilibreur de charge à l’aide du portail Azure.
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.date: 08/09/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: a25c35971d341c9ea6bce716931a76d682027e79
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732847"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Démarrage rapide : Créer un équilibreur de charge de base public pour équiper la charge de machines virtuelles en utilisant le portail Azure

Découvrez comment bien démarrer avec Azure Load Balancer en utilisant le portail Azure pour créer un équilibreur de charge public et trois machines virtuelles.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

---

# <a name="standard-sku"></a>[**Référence Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Il est recommandé de disposer d’un équilibreur de charge de référence SKU Standard pour les charges de travail de production.  Pour plus d’informations sur les références (SKU), consultez **[Références SKU Azure Load Balancer](skus.md)** .

## <a name="create-the-virtual-network"></a>Créer un réseau virtuel

Dans cette section, vous allez créer un réseau virtuel et un sous-réseau.

1. Dans la zone de recherche située en haut du portail, entrez **Réseau virtuel**. Dans les résultats de recherche, sélectionnez **Réseaux virtuels**.

2. Dans **Réseaux virtuels**, sélectionnez **+ Créer**.

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

Dans cette section, vous allez créer une passerelle NAT pour l’accès Internet sortant pour les ressources dans le réseau virtuel. 

1. Dans la zone de recherche située en haut du portail, entrez **Passerelle NAT**. Sélectionnez **Passerelle NAT** dans les résultats de la recherche.

2. Dans **Passerelles NAT**, sélectionnez **+ Créer**.

3. Dans **Créer une passerelle NAT (traduction d’adresses réseau)** , entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **CreatePubLBQS-rg**. |
    | **Détails de l’instance** |    |
    | Nom de la passerelle NAT | Entrez **myNATgateway**. |
    | Zone de disponibilité | Sélectionnez **Aucun**. |
    | Délai d’inactivité (minutes) | Entrez **15**. |

4. Sélectionnez l’onglet **IP sortante**, ou sélectionnez le bouton **Suivant : IP sortante** situé au bas de la page.

5. Dans **IP sortante**, sélectionnez **Créer une adresse IP publique** à côté d’**Adresses IP publiques**.

6. Entrez **myNATGatewayIP** pour le **Nom** dans **Ajouter une adresse IP publique**.

7. Sélectionnez **OK**.

8. Sélectionnez l’onglet **Sous-réseau**, ou sélectionnez le bouton **Suivant : Sous-réseau** situé au bas de la page.

9. Dans **Réseau virtuel**, dans l’onglet **Sous-réseau**, sélectionnez **myVNet**.

10. Sélectionnez **myBackendSubnet** sous **Nom du sous-réseau**.

11. Sélectionnez le bouton bleu **Vérifier + créer** au bas de la page, ou l’onglet **Vérifier + créer**.

12. Sélectionnez **Create** (Créer).

## <a name="create-load-balancer"></a><a name="create-load-balancer-resources"></a> Créer un équilibreur de charge

Dans cette section, vous créez un équilibreur de charge redondant dans une zone qui équilibre la charge des machines virtuelles. Avec la redondance dans une zone, une ou plusieurs zones de disponibilité peuvent échouer sans empêcher le chemin de données de survivre tant qu’une zone de la région reste intègre.

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
    | Resource group         | Sélectionnez **CreatePubLBQS-rg**. |
    | **Détails de l’instance** |   |
    | Nom                   | Entrez **myLoadBalancer**                                   |
    | Région         | Sélectionnez **(Europe) Europe Ouest**.                                        |
    | Type          | Sélectionnez **Public**.                                        |
    | SKU           | Conservez la valeur par défaut **Standard**. |
    | Niveau          | Conservez la valeur par défaut **Régional**. |

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png" alt-text="Capture d’écran de l’onglet Général de Créer un Standard Load Balancer." border="true":::

4. Sélectionnez **Suivant : Configuration IP frontale** au bas de la page.

5. Dans **Configuration IP frontale**, sélectionnez **+ Ajouter une IP frontale**.

6. Entrez **LoadBalancerFrontEnd** dans **Nom**.

7. Sélectionnez **IPv4** ou **IPv6** comme **Version IP**.

    > [!NOTE]
    > IPv6 n’est actuellement pas pris en charge avec la préférence de routage ou l’équilibrage de charge inter-régions (niveau global).

8. Sélectionnez **Adresse IP** pour le **Type d’IP**.

    > [!NOTE]
    > Pour plus d’informations sur les préfixes IP, consultez [Préfixe d’adresse IP publique Azure](../virtual-network/public-ip-address-prefix.md).

9. Sélectionnez **Créer** dans **Adresse IP publique**.

10. Dans **Ajouter une adresse IP publique**, entrez **myPublicIP** pour **Nom**.

11. Sélectionnez **Redondant interzone** dans **Zone de disponibilité**.

    > [!NOTE]
    > Dans les régions avec des [zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), vous avez la possibilité de sélectionner Aucune zone (option par défaut), une zone spécifique ou une zone redondante. Le choix dépend de vos exigences spécifiques en matière de défaillance de domaine. Dans les régions sans zones de disponibilité, ce champ n’apparaît pas. </br> Pour plus d’informations sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](../availability-zones/az-overview.md).

12. Laissez la valeur par défaut **Réseau Microsoft** pour **Préférence de routage**.

13. Sélectionnez **OK**.

14. Sélectionnez **Ajouter**.

15. Sélectionnez **Suivant : Pools de back-ends** en bas de la page.

16. Sous l’onglet **Pools de back-ends**, sélectionnez **+ Ajouter un pool de back-ends**.

17. Entrez **myBackendPool** comme **Nom** dans **Ajouter un pool de back-ends**.

18. Sélectionnez **myVNet** dans **Réseau virtuel**.

19. Sélectionnez la **Carte réseau** ou l’**Adresse IP** pour la **Configuration du pool principal**.

20. Sélectionnez **IPv4** ou **IPv6** comme **Version IP**.

21. Sélectionnez **Ajouter**.

22. Sélectionnez le bouton **Suivant : Règles entrantes** au bas de la page.

23. Dans **Règle d’équilibrage de charge** sous l’onglet **Règles de trafic entrant**, sélectionnez **+ Ajouter une règle d’équilibrage de charge**.

24. Entrez ou sélectionnez les informations suivantes dans **Ajouter une règle d’équilibrage de charge** :

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
    | Traduction d’adresses réseau (SNAT) sources sortante | Conservez la valeur par défaut **(Recommandé) Utiliser des règles de trafic sortant pour fournir aux membres du pool de back-ends l’accès à Internet**. |

25. Sélectionnez **Ajouter**.

26. Cliquez sur le bouton bleu **Vérifier + créer** au bas du panneau.

27. Sélectionnez **Create** (Créer).

    > [!NOTE]
    > Dans cet exemple, nous avons créé une passerelle NAT pour fournir un accès Internet sortant. L’onglet Règles de trafic sortant dans la configuration est ignoré, car il n’est pas obligatoire avec la passerelle NAT. Pour plus d’informations sur le service de passerelle NAT Azure, consultez [Qu’est-ce que le service NAT de réseau virtuel Azure ?](../virtual-network/nat-gateway/nat-overview.md)
    > Pour plus d’informations sur les connexions sortantes dans Azure, consultez [SNAT (Source Network Address Translation) pour les connexions sortantes](../load-balancer/load-balancer-outbound-connections.md)

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Dans cette section, vous allez créer trois machines virtuelles (**myVM1**, **myVM2** et **myVM3**) dans trois zones différentes (**Zone 1**, **Zone 2** et **Zone 3**). 

Ces machines virtuelles sont ajoutées au pool de back-ends de l’équilibreur de charge créé auparavant.

1. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

2. Dans **Machines virtuelles**, sélectionnez **+ Créer** > **Machine virtuelle**.
   
3. Dans **Créer une machine virtuelle**, tapez ou sélectionnez les valeurs sous l’onglet **De base** :

    | Paramètre | Valeur                                          |
    |-----------------------|----------------------------------|
    | **Détails du projet** |  |
    | Abonnement | Sélectionner votre abonnement Azure |
    | Groupe de ressources | Sélectionnez **CreatePubLBQS-rg**. |
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
    | Configurer un groupe de sécurité réseau | Sélectionnez **Créer nouveau**. </br> Dans **Créer un groupe de sécurité réseau**, entrez **myNSG** dans **Nom**. </br> Sous **Règles de trafic entrant**, sélectionnez **+Ajouter une règle de trafic entrant**. </br> Sous **Service**, sélectionnez **HTTP**. </br> Sous **Priorité**, entrez **100**. </br> Dans **Nom**, entrez **myNSGRule** </br> Sélectionnez **Ajouter** </br> Sélectionnez **OK**. |
    | **Équilibrage de charge**  |
    | Placer cette machine virtuelle derrière une solution d’équilibrage de charge existante ? | Activez la case à cocher. |
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

1. Dans la zone de recherche située en haut du portail, entrez **Réseau virtuel**. Dans les résultats de recherche, sélectionnez **Réseaux virtuels**.

2. Dans **Réseaux virtuels**, sélectionnez **+ Créer**.

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
    | Groupe de ressources | Sélectionnez **CreatePubLBQS-rg**. |
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
    | Placer cette machine virtuelle derrière une solution d’équilibrage de charge existante ? | Conservez la valeur par défaut (case non cochée). |
 
6. Sélectionnez l’onglet **Gestion** ou sélectionnez **Suivant** > **Gestion**.

7. Sous l’onglet **Gestion**, sélectionnez ou entrez :
    
    | Paramètre | Valeur |
    |---|---|
    | **Surveillance** | |
    | Diagnostics de démarrage | Sélectionnez **Désactivé** |

8. Sélectionnez **Revoir + créer**. 
  
9. Passez en revue les paramètres, puis sélectionnez **Créer**.

10. Suivez les étapes 1 à 9 pour créer deux machines virtuelles supplémentaires avec les valeurs suivantes et tous les autres paramètres identiques à ceux de **myVM1** :

    | Paramètre | Machine virtuelle 2 | Machine virtuelle 3 |
    | ------- | ----- |---|
    | Nom |  **myVM2** |**myVM3**|
    | Groupe à haute disponibilité| Sélectionnez **myAvailabilitySet** | Sélectionnez **myAvailabilitySet**|
    | Groupe de sécurité réseau | Sélectionnez le groupe **myNSG** existant| Sélectionnez le groupe **myNSG** existant|

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-load-balancer"></a>Créer un équilibreur de charge

Dans cette section, vous créez un équilibreur de charge qui équilibre la charge des machines virtuelles.

Lors de la création de l’équilibreur de charge, vous allez configurer les éléments suivants :

* Adresse IP du serveur frontal
* Pool de back-ends
* Règles d’équilibrage des charges entrantes

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

2. Dans la page **Équilibreur de charge**, sélectionnez **+ Créer**.

3. Dans l’onglet **Fonctions de base** de la page **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | **Détails du projet** |   |
    | Abonnement               | Sélectionnez votre abonnement.    |    
    | Resource group         | Sélectionnez **CreatePubLBQS-rg**. |
    | **Détails de l’instance** |   |
    | Nom                   | Entrez **myLoadBalancer**                                   |
    | Région         | Sélectionnez **(Europe) Europe Ouest**.                                        |
    | Type          | Sélectionnez **Public**.                                        |
    | SKU           | Sélectionnez **De base**. |

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-basic-load-balancer.png" alt-text="Capture d’écran de l’onglet Général de Créer un Load Balancer de base." border="true":::
 
4. Sélectionnez **Suivant : Configuration IP frontale** au bas de la page.

5. Dans **Configuration IP frontale**, sélectionnez **+ Ajouter une IP frontale**.

6. Entrez **LoadBalancerFrontEnd** dans **Nom**.

7. Sélectionnez **IPv4** ou **IPv6** comme **Version IP**.

8. Sélectionnez **Créer** dans **Adresse IP publique**.

9. Dans **Ajouter une adresse IP publique**, entrez **myPublicIP** pour **Nom**.

10. Dans **Affectation**, sélectionnez **Statique**.

11. Sélectionnez **OK**.

12. Sélectionnez **Ajouter**.

13. Sélectionnez **Suivant : Pools de back-ends** en bas de la page.

14. Sous l’onglet **Pools de back-ends**, sélectionnez **+ Ajouter un pool de back-ends**.

15. Entrez **myBackendPool** comme **Nom** dans **Ajouter un pool de back-ends**.

16. Sélectionnez **myVNet** dans **Réseau virtuel**.

17. Dans **Associé à**, sélectionnez **Machines virtuelles**.

18. Sélectionnez **IPv4** ou **IPv6** comme **Version IP**.

19. Dans **Machines virtuelles**, sélectionnez le bouton bleu **+ Ajouter**.

20. Dans **Ajouter des machines virtuelles au pool de back-ends**, sélectionnez les cases à côté de **myvm1**, **myvm2** et **myvm3**.

21. Sélectionnez **Ajouter**.

22. Sélectionnez **Ajouter** dans **Ajouter un pool de back-ends**.

23. Sélectionnez le bouton **Suivant : Règles entrantes** au bas de la page.

24. Dans **Règle d’équilibrage de charge** sous l’onglet **Règles de trafic entrant**, sélectionnez **+ Ajouter une règle d’équilibrage de charge**.

25. Entrez ou sélectionnez les informations suivantes dans **Ajouter une règle d’équilibrage de charge** :

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
    | IP flottante | Sélectionnez **Désactivé**. |

26. Sélectionnez **Ajouter**.

27. Cliquez sur le bouton bleu **Vérifier + créer** au bas du panneau.

28. Sélectionnez **Create** (Créer).

---

## <a name="install-iis"></a>Installer IIS

1. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

2. Sélectionnez **myVM1**.

2. Dans la page **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Bastion**.

3. Sélectionnez **Utiliser Bastion**.

4. Entrez le nom d’utilisateur et le mot de passe saisis pendant la création de la machine virtuelle.

5. Sélectionnez **Connecter**.

6. Sur le bureau du serveur, accédez à **Outils d’administration Windows** > **Windows PowerShell**.

7. Dans la fenêtre PowerShell, exécutez les commandes suivantes pour :

    * Installer le serveur IIS
    * Supprimer le fichier iisstart.htm par défaut
    * Ajoutez un nouveau fichier iisstart.htm qui affiche le nom de la machine virtuelle :

   ```powershell
    # Install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    Remove-Item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```

8. Fermez la session Bastion avec **myVM1**.

9. Répétez les étapes 1 à 8 pour installer IIS et le fichier iisstart.htm mis à jour sur **myVM2** et **myVM3**.

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge

1. Dans la zone de recherche située en haut de la page, entrez **Équilibreur de charge**.  Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

2. Trouvez l’adresse IP publique de l’équilibreur de charge sur la page **Vue d’ensemble** sous **Adresse IP publique**.

3. Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur. La page de la machine virtuelle personnalisée du serveur web IIS s’affiche dans le navigateur.

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/load-balancer-test.png" alt-text="Capture d’écran du test de l’équilibreur de charge":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, l’équilibreur de charge et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources **CreatePubLBQS-rg** qui contient les ressources, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous :

* Avez créé un équilibreur de charge Azure Standard ou De base
* Avez attaché 3 machines virtuelles à l’équilibreur de charge
* Vous avez testé l’équilibreur de charge.

Pour en savoir plus sur Azure Load Balancer, consultez :
> [!div class="nextstepaction"]
> [Qu’est-ce que Azure Load Balancer ?](load-balancer-overview.md)