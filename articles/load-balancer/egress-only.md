---
title: Configuration de l’équilibreur de charge en sortie uniquement
titleSuffix: Azure Load Balancer
description: Dans cet article, découvrez comment créer un équilibreur de charge interne avec NAT sortante
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/21/2021
ms.author: allensu
ms.openlocfilehash: d2f54b8ea7161f297528ac6ea82fde548827033a
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681186"
---
# <a name="outbound-only-load-balancer-configuration"></a>Configuration de l’équilibreur de charge en sortie uniquement

Utilisez une combinaison d’équilibreurs de charge standard internes et externes pour créer une connectivité sortante pour les machines virtuelles derrière un équilibreur de charge interne. 

Cette configuration fournit une traduction d’adresses réseau (NAT) sortante pour un scénario d’équilibrage de charge interne, ce qui génère une configuration de « sortie uniquement » pour votre pool principal.

> [!NOTE]
> La configuration de la **traduction d’adresses réseau (NAT) du réseau virtuel Azure** est celle recommandée pour les déploiements en production. Pour en savoir plus sur le service **NAT du réseau virtuel Azure** et la ressource de **passerelle NAT**, consultez **[la page Qu’est-ce que le service NAT de réseau virtuel ?](../virtual-network/nat-gateway/nat-overview.md)** .
>
> Pour déployer une configuration d’équilibreur de charge de sortie uniquement avec le NAT du réseau virtuel Azure et une passerelle NAT, consultez [Didacticiel : intégrer une passerelle NAT à un équilibreur de charge interne - portail Azure](../virtual-network/nat-gateway/tutorial-nat-gateway-load-balancer-internal-portal.md).
>
> Pour plus d’informations sur les connexions sortantes dans Azure et l’accès sortant par défaut, consultez [Traduction d’adresses réseau sources SNAT pour les connexions sortantes](load-balancer-outbound-connections.md) et [Accès sortant par défaut](../virtual-network/default-outbound-access.md).

:::image type="content" source="./media/egress-only/load-balancer-egress-only.png" alt-text="La figure illustre une configuration d’équilibreur de charge de sortie uniquement" border="true":::

*Figure : Configuration de l’équilibreur de charge en sortie uniquement*

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-network-and-load-balancers"></a>Créer un réseau virtuel et des équilibreurs de charge

Dans cette section, vous allez créer un réseau virtuel et un sous réseau pour les équilibreurs de charge et la machine virtuelle.  Vous allez ensuite créer les équilibreurs de charge.

### <a name="create-the-virtual-network"></a>Créer un réseau virtuel

Dans cette section, vous allez créer le réseau virtuel et les sous-réseaux pour la machine virtuelle, l’équilibreur de charge et l’hôte bastion.

1. Dans la zone de recherche située en haut du portail, entrez **Réseau virtuel**. Dans les résultats de recherche, sélectionnez **Réseaux virtuels**.

2. Dans **Réseaux virtuels**, sélectionnez **+ Créer**.

3. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Sélectionner votre abonnement Azure                                  |
    | Groupe de ressources   | Sélectionnez **Créer nouveau**. </br> Dnas **Nom** entrez **myResourceGroupLB**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |                                                                 |
    | Nom             | Entrez **myVNet**                                    |
    | Région           | Sélectionnez **(États-Unis) USA Est 2** |

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

### <a name="create-internal-load-balancer"></a>Créer un équilibrage de charge interne

Dans cette section, vous allez créer l’équilibreur de charge interne.

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

2. Dans la page **Équilibreur de charge**, sélectionnez **Créer**.

3. Dans l’onglet **Fonctions de base** de la page **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | **Détails du projet** |   |
    | Abonnement               | Sélectionnez votre abonnement.    |    
    | Resource group         | Sélectionnez **myResourceGroupLB**. |
    | **Détails de l’instance** |   |
    | Nom                   | Entrez **myInternalLoadBalancer**                                   |
    | Région         | Sélectionnez **(États-Unis) USA Est 2**.                                        |
    | Type          | sélectionnez **Interne**.                                        |
    | SKU           | Conservez la valeur par défaut **Standard**. |

4. Sélectionnez **Suivant : Configuration IP frontale** au bas de la page.

5. Dans **Configuration IP frontale**, sélectionnez **+ Ajouter une adresse IP frontale**.

6. Entrez **LoadBalancerFrontEnd** dans **Nom**.

7. Sélectionnez **myBackendSubnet** dans **Sous-réseau**.

8. Sélectionnez **Dynamique** comme **Attribution**.

9. Sélectionnez **Zone-redondante** dans la **Zone de disponibilité**.

    > [!NOTE]
    > Dans les régions avec des [Zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), vous avez la possibilité de sélectionner Aucune zone (option par défaut), une zone spécifique ou une zone redondante. Le choix dépendra de vos exigences spécifiques en matière de défaillance de domaine. Dans les régions sans Zones de disponibilité, ce champ n’apparaît pas. </br> Pour plus d’informations sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](../availability-zones/az-overview.md).

10. Sélectionnez **Ajouter**.

11. Sélectionnez **Suivant : Pools principaux** en bas de la page.

12. Sous l’onglet **Pools principaux**, sélectionnez **+ Ajouter un pool principal**.

13. Entrez **myBackendPool** pour le **Nom** dans **Ajouter un pool principal**.

14. Sélectionnez la **Carte réseau** ou l’**Adresse IP** pour la **Configuration du pool principal**.

15. Sélectionnez **IPv4** ou **IPv6** pour la **Version IP**.

16. Sélectionnez **Ajouter**.

17. Cliquez sur le bouton bleu **Vérifier + créer** en bas de la page.

18. Sélectionnez **Create** (Créer).

### <a name="create-public-load-balancer"></a>Créer un équilibreur de charge public

Dans cette section, vous allez créer l’équilibreur de charge public.

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

2. Dans la page **Équilibreur de charge**, sélectionnez **Créer**.

3. Dans l’onglet **Fonctions de base** de la page **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | **Détails du projet** |   |
    | Abonnement               | Sélectionnez votre abonnement.    |    
    | Resource group         | Sélectionnez **myResourceGroupLB**. |
    | **Détails de l’instance** |   |
    | Nom                   | Entrez **myPublicLoadBalancer**                                   |
    | Région         | Sélectionnez **(États-Unis) USA Est 2**.                                        |
    | Type          | Sélectionnez **Public**.                                        |
    | SKU           | Conservez la valeur par défaut **Standard**. |
    | Niveau          | Conservez la valeur par défaut **Régional**. |

4. Sélectionnez **Suivant : Configuration IP frontale** au bas de la page.

5. Dans **Configuration IP frontale**, sélectionnez **+ Ajouter une adresse IP frontale**.

6. Entrez **LoadBalancerFrontEnd** dans **Nom**.

7. Sélectionnez **IPv4** ou **IPv6** pour la **Version IP**.

    > [!NOTE]
    > IPv6 n’est actuellement pas pris en charge avec la Préférence de routage ou l’équilibrage de charge Inter-régions (niveau Global).

8. Sélectionnez **Adresse IP** pour le **Type d’IP**.

    > [!NOTE]
    > Pour plus d’informations sur les préfixes IP, consultez [Préfixe d’adresse IP publique Azure](../virtual-network/public-ip-address-prefix.md).

9. Sélectionnez **Créer nouvelle** dans **Adresse IP publique**.

10. Dans **Ajouter une adresse IP publique**, entrez **myPublicIP** pour **Nom**.

11. Sélectionnez **Zone-redondante** dans la **Zone de disponibilité**.

    > [!NOTE]
    > Dans les régions avec des [Zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), vous avez la possibilité de sélectionner Aucune zone (option par défaut), une zone spécifique ou une zone redondante. Le choix dépendra de vos exigences spécifiques en matière de défaillance de domaine. Dans les régions sans Zones de disponibilité, ce champ n’apparaît pas. </br> Pour plus d’informations sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](../availability-zones/az-overview.md).

12. Laissez la valeur par défaut du **Réseau Microsoft** pour **Préférence de routage**.

13. Sélectionnez **OK**.

14. Sélectionnez **Ajouter**.

15. Sélectionnez **Suivant : Pools principaux** en bas de la page.

16. Sous l’onglet **Pools principaux**, sélectionnez **+ Ajouter un pool principal**.

17. Entrez **myPublicBackendPool** pour le **Nom** dans **Ajouter un pool principal**.

18. Sélectionnez **myVNet** dans **Réseau virtuel**.

19. Sélectionnez la **Carte réseau** ou l’**Adresse IP** pour la **Configuration du pool principal**.

20. Sélectionnez **IPv4** ou **IPv6** pour la **Version IP**.

21. Sélectionnez **Ajouter**.

22. Cliquez sur le bouton bleu **Vérifier + créer** en bas de la page.

23. Sélectionnez **Create** (Créer).

## <a name="create-virtual-machine"></a>Créer une machine virtuelle

Dans cette section, vous allez créer une machine virtuelle. Au cours de la création, vous allez l’ajouter au pool principal de l’équilibreur de charge interne. Une fois la machine virtuelle créée, vous ajouterez la machine virtuelle au pool principal de l’équilibreur de charge public.

1. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

2. Dans **Machines virtuelles**, sélectionnez **+ Créer** > **Machine virtuelle**.
   
3. Dans **Créer une machine virtuelle**, entrez ou sélectionnez les valeurs sous l’onglet **Informations de base** :

    | Paramètre | Valeur                                          |
    |-----------------------|----------------------------------|
    | **Détails du projet** |  |
    | Abonnement | Sélectionner votre abonnement Azure |
    | Groupe de ressources | Sélectionnez **myResourceGroupLB** |
    | **Détails de l’instance** |  |
    | Nom de la machine virtuelle | Entrez **myVM**. |
    | Région | Sélectionnez **(États-Unis) USA Est 2** |
    | Options de disponibilité | Sélectionnez **Aucune redondance d’infrastructure requise**. |
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
    | Configurer un groupe de sécurité réseau | Conservez la valeur par défaut **De base**. |
    | **Équilibrage de charge**  |
    | Placer cette machine virtuelle derrière une solution d’équilibrage de charge existante ? | Sélectionnez la zone. |
    | **Paramètres d’équilibrage de charge** |
    | Options d’équilibrage de charge | Sélectionnez **Équilibrage de charge Azure** |
    | Sélectionnez un équilibreur de charge | Entrez **myInternalLoadBalancer**  |
    | Sélectionnez un pool principal | Sélectionnez **myInternalBackendPool** |
   
6. Sélectionnez **Revoir + créer**. 
  
7. Passez en revue les paramètres, puis sélectionnez **Créer**.

### <a name="add-vm-to-backend-pool-of-public-load-balancer"></a>Ajouter une machine virtuelle au pool principal de l’équilibreur de charge public

Dans cette section, vous allez ajouter la machine virtuelle que vous avez créée précédemment au pool principal de l’équilibreur de charge public.

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

2. Sélectionnez **myPublicLoadBalancer**.

3. Dans **myPublicLoadBalancer**, sélectionnez **Pools principaux** dans **Paramètres**.

4. Sélectionnez **myPublicBackendPool** sous **Pool principal** sur la page **Pools principaux**.

5. Dans **myPublicBackendPool**, sélectionnez **myVNet** dans **Réseau virtuel**.

6. Dans **Machines virtuelles**, sélectionnez le bouton bleu **+ Ajouter**.

7. Sélectionnez la case à côté de **myVM** dans **Ajouter des machines virtuelles au pool principal**.

8. Sélectionnez **Ajouter**.

9. Sélectionnez **Enregistrer**.
## <a name="test-connectivity-before-outbound-rule"></a>Tester la connectivité avant la règle de trafic sortant

1. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

2. Sélectionnez **myVM**.

3. Sur la page **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Bastion**.

4. Entrez le nom d’utilisateur et le mot de passe saisis pendant la création de la machine virtuelle.

5. Sélectionnez **Connecter**.

6. Ouvrez Internet Explorer.

7. Entrez **https://whatsmyip.org** dans la barre d’adresse.

8. La connexion doit échouer. Par défaut, l’équilibreur de charge public standard [n’autorise pas le trafic sortant sans une règle de trafic sortant définie](load-balancer-overview.md#securebydefault).
 
## <a name="create-a-public-load-balancer-outbound-rule"></a>Créer une règle de trafic sortant d’équilibreur de charge public

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

2. Sélectionnez **myPublicLoadBalancer**.

3. Dans **myPublicLoadBalancer**, sélectionnez **Règles de trafic sortant** dans **Paramètres**.

4. Sélectionnez **+ Ajouter** dans **Règles de trafic sortant**.

5. Entrez ou sélectionnez les informations suivantes pour configurer la règle de trafic sortant.

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **myOutboundRule**. |
    | Adresse IP du serveur frontal | Sélectionnez **LoadBalancerFrontEnd**.|
    | Protocole | Conservez la valeur par défaut **Toutes**. |
    | Délai d’inactivité (minutes) | Déplacez le curseur sur **15 minutes**.|
    | Réinitialisation du protocole TCP | Sélectionnez **Enabled**.|
    | Pool principal | Sélectionnez **myPublicBackendPool**.|
    | **Allocation de port** |  |
    | Allocation de ports | Sélectionnez **Choisir manuellement le nombre de ports de sortie**. |
    | **Ports de sortie** |  |
    | Choisir par | Sélectionnez **Ports par instance**. |
    | Ports par instance | Entrez **10 000**

6. Sélectionnez **Ajouter**.

## <a name="test-connectivity-after-outbound-rule"></a>Tester la connectivité après la règle de trafic sortant

1. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

2. Sélectionnez **myVM**.

3. Dans la page **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Bastion**.

4. Entrez le nom d’utilisateur et le mot de passe saisis pendant la création de la machine virtuelle.

5. Sélectionnez **Connecter**.

6. Ouvrez Internet Explorer.

7. Entrez **https://whatsmyip.org** dans la barre d’adresse.

8. La connexion doit être établie.

9. L’adresse IP affichée doit être l’adresse IP frontale de **myPublicLoadBalancer**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, supprimez le groupe de ressources, les équilibreurs de charge, la machine virtuelle et toutes les ressources associées. 

Pour ce faire, sélectionnez le groupe de ressources **myResourceGroupLB**, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé une configuration de « sortie uniquement » avec une combinaison d’équilibreurs de charge publics et internes.  

Cette configuration vous permet d’équilibrer la charge du trafic interne entrant vers votre pool principal tout en empêchant les connexions entrantes publiques.

Pour plus d’informations sur l’Equilbreur de charge Azure et Azure Bastion, consultez [Qu’est-ce que l’Equilbreur de charge Azure ?](load-balancer-overview.md) et [Qu’est-ce qu’Azure Bastion ?](../bastion/bastion-overview.md).

