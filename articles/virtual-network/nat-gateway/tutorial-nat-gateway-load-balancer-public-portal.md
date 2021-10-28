---
title: 'Tutoriel : Intégrer une passerelle NAT à un équilibreur de charge public - Portail Azure'
titleSuffix: Virtual Network NAT
description: Dans ce tutoriel, découvrez comment intégrer une passerelle NAT de réseau virtuel à un équilibreur de charge public à l’aide du portail Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: ffc5b04928842b940a369ef00807bf0a2e1b0230
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249017"
---
# <a name="tutorial-integrate-a-nat-gateway-with-a-public-load-balancer-using-the-azure-portal"></a>Tutoriel : Intégrer une passerelle NAT à un équilibreur de charge public à l’aide du portail Azure

Dans ce tutoriel, vous allez apprendre à intégrer une passerelle NAT à un équilibreur de charge public.

Par défaut, un Standard Load Balancer Azure est sécurisé. La connectivité sortante est définie explicitement par l’activation de la traduction d’adresses réseau (SNAT) sortante. La traduction d’adresses réseau est activée dans une règle d’équilibrage de charge ou des règles de trafic sortant. 

L’intégration de la passerelle NAT évite d’avoir à utiliser des règles de trafic sortant pour la traduction d’adresses réseau sortantes du pool de back-ends. 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un équilibreur de charge Azure
> * Créer deux machines virtuelles pour le pool de back-ends de l’équilibreur de charge Azure
> * Créer une passerelle NAT
> * Valider la connectivité sortante des machines virtuelles dans le pool de back-ends de l’équilibreur de charge

## <a name="prerequisites"></a>Prérequis

Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-the-virtual-network"></a>Créer un réseau virtuel

Dans cette section, vous allez créer un réseau virtuel et un sous-réseau.

1. Dans la zone de recherche située en haut du portail, entrez **Réseau virtuel**. Dans les résultats de la recherche, sélectionnez **Réseaux virtuels**.

2. Dans **Réseaux virtuels**, sélectionnez **+ Créer**.

3. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Sélectionner votre abonnement Azure                                  |
    | Groupe de ressources   | Sélectionnez **Créer nouveau**. </br> Dans **Nom**, entrez **TutorPubLBNAT-rg**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |                                                                 |
    | Nom             | Entrez **myVNet**                                    |
    | Région           | Sélectionnez **(États-Unis) USA Est** |

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

## <a name="create-load-balancer"></a>Créer un équilibreur de charge

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
    | Resource group         | Sélectionnez **TutorPubLBNAT-rg**. |
    | **Détails de l’instance** |   |
    | Nom                   | Entrez **myLoadBalancer**                                   |
    | Région         | Sélectionnez **(États-Unis) USA Est**.                                        |
    | Type          | Sélectionnez **Public**.                                        |
    | SKU           | Conservez la valeur par défaut **Standard**. |
    | Niveau          | Conservez la valeur par défaut **Régional**. |


4. Sélectionnez **Suivant : configuration de l’adresse IP front-end** au bas de la page.

5. Dans **Configuration de l’adresse IP front-end**, sélectionnez **+ Ajouter une adresse IP front-end**.

6. Entrez **LoadBalancerFrontend** dans **Nom**.

7. Sélectionnez **IPv4** ou **IPv6** pour la **Version IP**.

    > [!NOTE]
    > IPv6 n’est actuellement pas pris en charge avec la Préférence de routage ou l’équilibrage de charge Inter-régions (niveau Global).

8. Sélectionnez **Adresse IP** pour **Type IP**.

    > [!NOTE]
    > Pour plus d’informations sur les préfixes IP, consultez [Préfixe d’adresse IP publique Azure](../ip-services/public-ip-address-prefix.md).

9. Sélectionnez **Créer nouvelle** dans **Adresse IP publique**.

10. Dans **Ajouter une adresse IP publique**, entrez **myPublicIP** pour **Nom**.

11. Sélectionnez **Zone-redondante** dans la **Zone de disponibilité**.

    > [!NOTE]
    > Dans les régions avec des [zones de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), vous avez la possibilité de sélectionner aucune zone (option par défaut), une zone spécifique ou redondant dans une zone. Le choix dépendra de vos exigences spécifiques en matière de défaillance de domaine. Dans les régions sans Zones de disponibilité, ce champ n’apparaît pas. </br> Pour plus d’informations sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](../../availability-zones/az-overview.md).

12. Laissez la valeur par défaut **Réseau Microsoft** pour **Préférence de routage**.

13. Sélectionnez **OK**.

14. Sélectionnez **Ajouter**.

15. Sélectionnez **Suivant : Pools de back-end** au bas de la page.

16. Sous l’onglet **Pools de back-end**, sélectionnez **+ Ajouter un pool de back-end**.

17. Entrez **myBackendPool** comme **Nom** dans **Ajouter un pool de back-end**.

18. Sélectionnez **myVNet** dans **Réseau virtuel**.

19. Sélectionnez **Carte d’interface réseau** ou **Adresse IP** pour la **Configuration du pool de back-end**.

20. Sélectionnez **IPv4** ou **IPv6** pour la **Version IP**.

21. Sélectionnez **Ajouter**.

22. Sélectionnez le bouton **Suivant : Règles de trafic entrant** au bas de la page.

23. Dans **Règle d’équilibrage de la charge** sous l’onglet **Règles de trafic entrant**, sélectionnez **+ Ajouter une règle d’équilibrage de charge**.

24. Dans **Ajouter une règle d’équilibrage de charge**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Value |
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

26. Sélectionnez le bouton bleu **Vérifier + créer** au bas de la page.

27. Sélectionnez **Create** (Créer).

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Dans cette section, vous allez créer deux machines virtuelles (**myVM1** et **myVM2**) dans deux zones différentes (**Zone 1** et **Zone 2**).

Ces machines virtuelles sont ajoutées au pool de back-ends de l’équilibreur de charge créé auparavant.

1. En haut à gauche du portail, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**. 
   
2. Dans **Créer une machine virtuelle**, tapez ou sélectionnez les valeurs sous l’onglet **De base** :

    | Paramètre | Value                                          |
    |-----------------------|----------------------------------|
    | **Détails du projet** |  |
    | Abonnement | Sélectionner votre abonnement Azure |
    | Groupe de ressources | Sélectionner **TutorPubLBNAT-rg** |
    | **Détails de l’instance** |  |
    | Nom de la machine virtuelle | Entrez **myVM1** |
    | Région | Sélectionnez **(États-Unis) USA Est** |
    | Options de disponibilité | Sélectionnez **Zones de disponibilité** |
    | Zone de disponibilité | Sélectionnez **1** |
    | Image | Sélectionnez **Windows Server 2019 Datacenter** |
    | Instance Azure Spot | Conservez la valeur par défaut |
    | Taille | Choisissez la taille de la machine virtuelle ou acceptez le paramètre par défaut |
    | **Compte administrateur** |  |
    | Nom d’utilisateur | Entrez un nom d’utilisateur |
    | Mot de passe | Entrez un mot de passe |
    | Confirmer le mot de passe | Entrez à nouveau le mot de passe |
    | **Règles des ports d’entrée** |  |
    | Aucun port d’entrée public | Sélectionnez **Aucun** |

3. Sélectionnez l'onglet **Mise en réseau** ou choisissez **Suivant : Disques**, puis **Suivant : Mise en réseau**.
  
4. Sous l’onglet Réseau, sélectionnez ou entrez :

    | Paramètre | Value |
    |-|-|
    | **Interface réseau** |  |
    | Réseau virtuel | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Adresse IP publique | Sélectionnez **Aucun**. |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **Avancé**|
    | Configurer un groupe de sécurité réseau | Sélectionnez **Créer nouveau**. </br> Dans **Créer un groupe de sécurité réseau**, entrez **myNSG** dans **Nom**. </br> Sous **Règles de trafic entrant**, sélectionnez **+Ajouter une règle de trafic entrant**. </br> Dans **Plages de ports de destination**, entrez **80**. </br> Sous **Priorité**, entrez **100**. </br> Dans **Nom**, entrez **myNSGRule** </br> Sélectionnez **Ajouter** </br> Sélectionnez **OK**. |
    | **Équilibrage de charge**  |
    | Placer cette machine virtuelle derrière une solution d’équilibrage de charge existante ? | Activez la case à cocher.|
    | **Paramètres d’équilibrage de charge** |
    | Options d’équilibrage de charge | Sélectionnez **Équilibreur de charge Azure** |
    | Sélectionnez un équilibreur de charge | Sélectionnez **myLoadBalancer**  |
    | Sélectionnez un pool principal | Sélectionnez **myBackendPool** |
   
5. Sélectionnez **Revoir + créer**. 
  
6. Passez en revue les paramètres, puis sélectionnez **Créer**.

7. Suivez les étapes 1 à 7 pour créer une machine virtuelle avec les valeurs suivantes et tous les autres paramètres identiques à ceux de **myVM1** :

    | Paramètre | Machine virtuelle 2 |
    | ------- | ----- |
    | Nom |  **myVM2** |
    | Zone de disponibilité | **2** |
    | Groupe de sécurité réseau | Sélectionnez le groupe **myNSG** existant | 

## <a name="create-nat-gateway"></a>Créer une passerelle NAT

Dans cette section, vous allez créer une passerelle NAT et l’affecter au sous-réseau dans le réseau virtuel que vous avez créé précédemment.

1. En haut à gauche de l’écran, sélectionnez **Créer une ressource > Réseau > Passerelle NAT**, ou recherchez **Passerelle NAT** dans la zone de recherche.

2. Sélectionnez **Create** (Créer). 

3. Dans **Créer une passerelle NAT (traduction d’adresses réseau)** , entrez ou sélectionnez les informations suivantes sous l’onglet **Informations de base** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Sélectionnez votre abonnement Azure.                                  |
    | Groupe de ressources   | Sélectionnez **TutorPubLBNAT-rg**. |
    | **Détails de l’instance** |                                                                 |
    | Nom             | Entrez **myNATGateway**                                    |
    | Région           | Sélectionnez **(États-Unis) USA Est**  |
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

## <a name="test-nat-gateway"></a>Tester la passerelle NAT

Dans cette section, nous allons tester la passerelle NAT. Nous allons d’abord découvrir l’IP publique de la passerelle NAT. Nous allons ensuite nous connecter à la machine virtuelle de test et vérifier la connexion sortante via la passerelle NAT.
    
1. Recherchez l’adresse IP publique de la passerelle NAT sur l’écran **Vue d’ensemble**. Sélectionnez **Tous les services** dans le menu de gauche, sélectionnez **Toutes les ressources**, puis sélectionnez **myPublicIP**.

2. Prenez note de l’adresse IP publique :

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-public-portal/find-public-ip.png" alt-text="Capture d’écran de l’adresse IP publique de la passerelle NAT" border="true":::

3. Sélectionnez **Tous les services** dans le menu de gauche, sélectionnez **Toutes les ressources** puis, dans la liste de ressources, sélectionnez **myVM1** qui se trouve dans le groupe de ressources **TutorPubLBNAT-rg**.

4. Dans la page **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Bastion**.

5. Sélectionnez le bouton bleu **Utiliser le bastion**.

6. Entrez le nom d’utilisateur et le mot de passe saisis pendant la création de la machine virtuelle.

7. Ouvrez **Internet Explorer** sur **myVM1**.

8. Entrez **https://whatsmyip.com** dans la barre d’adresse.

9. Vérifiez que l’adresse IP affichée correspond à l’adresse de la passerelle NAT que vous avez notée à l’étape précédente :

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-public-portal/my-ip.png" alt-text="Capture d’écran d’Internet Explorer affichant une adresse IP sortante externe" border="true":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser cette application, supprimez le réseau virtuel, la machine virtuelle et la passerelle NAT en effectuant les étapes suivantes :

1. Dans le menu de gauche, sélectionnez **Groupes de ressources**.

2. Sélectionnez le groupe de ressources **TutorPubLBNAT-rg**.

3. Sélectionnez **Supprimer le groupe de ressources**.

4. Entrez **TutorPubLBNAT-rg**, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le NAT de réseau virtuel Azure, consultez :
> [!div class="nextstepaction"]
> [Vue d’ensemble de NAT de réseau virtuel](nat-overview.md)