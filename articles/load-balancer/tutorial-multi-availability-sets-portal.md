---
title: 'Tutoriel : Créer un équilibreur de charge avec plusieurs groupes à haute disponibilité dans le pool de back-ends - Portail Azure'
titleSuffix: Azure Load Balancer
description: Dans ce tutoriel, vous déployez un équilibreur de charge Azure avec plusieurs groupes à haute disponibilité dans le pool de back-ends.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 08/12/2021
ms.custom: template-tutorial
ms.openlocfilehash: a59f97e41705e222bbbca6656cd67d2e692ec5fa
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232401"
---
# <a name="tutorial-create-a-load-balancer-with-more-than-one-availability-set-in-the-backend-pool-using-the-azure-portal"></a>Tutoriel : Créer un équilibreur de charge avec plusieurs groupes à haute disponibilité dans le pool de back-ends à l’aide du portail Azure

Dans le cadre d’un déploiement à haute disponibilité, les machines virtuelles sont souvent regroupées en plusieurs groupes à haute disponibilité. 

Load Balancer prend en charge plusieurs groupes à haute disponibilité avec des machines virtuelles dans le pool de back-ends.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créez un réseau virtuel
> * Créer une passerelle NAT pour la connectivité sortante
> * Créer une référence SKU Azure Load Balancer standard
> * Créer quatre machines virtuelles et deux groupes à haute disponibilité
> * Ajouter des machines virtuelles de groupes à haute disponibilité au un pool de back-ends de l’équilibreur de charge
> * Tester l’équilibreur de charge

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Dans cette section, vous allez créer un réseau virtuel pour l’équilibreur de charge et les autres ressources utilisées dans le tutoriel.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Réseau virtuel**.

3. Dans les résultats de recherche, sélectionnez **Réseaux virtuels**.

4. Sélectionnez **+ Créer**.

5. Sous l’onglet **Informations de base** de la boîte de dialogue **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ------|
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **Créer nouveau**. </br> Entrez **TutorLBmultiAVS-rg** dans **Nom**. |
    | **Détails de l’instance** |   |
    | Nom | Entrez **myVNet**. |
    | Région | Sélectionnez **(USA) USA Ouest 2**. |

6. Sélectionnez l’onglet **Adresses IP** ou le bouton **Suivant : Adresses IP** situé en bas de la page.

7. Sous l’onglet **Adresses IP**, sous **Nom du sous-réseau**, sélectionnez **Par défaut**.

8. Dans le volet **Modifier le sous-réseau**, sous **Nom du sous-réseau**, entrez **myBackendSubnet**.

9. Sélectionnez **Enregistrer**.

10. Sélectionnez l’onglet **Sécurité** ou le bouton **Suivant : Sécurité** situé en bas de la page.

11. Sous l’onglet **Sécurité**, dans **BastionHost**, sélectionnez **Activer**.

12. Entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom du bastion | Entrez **MyBastionHost**. |
    | Espace d’adressage AzureBastionSubnet | Entrez **10.1.1.0/27**. |
    | Adresse IP publique | Sélectionnez **Créer nouveau**. </br> Entrez **myBastionIP** dans **Nom**. |

13. Sélectionnez l’onglet **Vérifier + créer** ou le bouton bleu **Vérifier + créer** situé en bas de la page.

14. Sélectionnez **Create** (Créer).

## <a name="create-nat-gateway"></a>Créer une passerelle NAT 

Dans cette section, vous allez créer une passerelle NAT pour la connectivité sortante des machines virtuelles.

1. Dans la zone de recherche située en haut du portail, entrez **Passerelle NAT**.

2. Sélectionnez **Passerelle NAT** dans les résultats de la recherche.

3. Sélectionnez **+ Créer**.

4. Sous l’onglet **Informations de base** de **Créer une passerelle NAT (traduction d’adresses réseau)** , entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Groupe de ressources | Sélectionnez **TutorLBmultiAVS-rg**. |
    | **Détails de l’instance** |   |
    | Nom de la passerelle NAT | Entrez **myNATgateway**. |
    | Région | Sélectionnez **(USA) USA Ouest 2**. |
    | Zone de disponibilité | Sélectionnez **Aucun**. |
    | Délai d’inactivité (minutes) | Entrez **15**. |

5. Sélectionnez l’onglet **IP sortante**, ou sélectionnez le bouton **Suivant : IP sortante** situé au bas de la page.

6. Sélectionnez **Créer une adresse IP publique** en regard d’**Adresses IP publiques** sous l’onglet **IP sortante**.

7. Dans **Nom**, entrez **myNATgatewayIP**.

8. Sélectionnez **OK**.

9. Sélectionnez l’onglet **Sous-réseau**, ou sélectionnez le bouton **Suivant : Sous-réseau** situé au bas de la page.

10. Sélectionnez **myVNet** dans le menu déroulant sous **Réseau virtuel**.

11. Cochez la case en regard de **myBackendSubnet**.

12. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.

13. Sélectionnez **Create** (Créer).

## <a name="create-load-balancer"></a>Créer un équilibreur de charge

Dans cette section, vous allez créer un équilibreur de charge pour les machines virtuelles.

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

2. Dans la page **Équilibreur de charge**, sélectionnez **Créer**.

3. Dans l’onglet **Fonctions de base** de la page **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | **Détails du projet** |   |
    | Abonnement               | Sélectionnez votre abonnement.    |    
    | Groupe de ressources         | Sélectionnez **TutorLBmultiAVS-rg**. |
    | **Détails de l’instance** |   |
    | Nom                   | Entrez **myLoadBalancer**                                   |
    | Région         | Sélectionnez **(USA) USA Ouest 2**.                                        |
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
    > Pour plus d’informations sur les préfixes IP, consultez [Préfixe d’adresse IP publique Azure](../virtual-network/ip-services/public-ip-address-prefix.md).

9. Sélectionnez **Créer nouvelle** dans **Adresse IP publique**.

10. Dans **Ajouter une adresse IP publique**, entrez **myPublicIP-lb** pour **Nom**.

11. Sélectionnez **Redondant interzone** dans la **Zone de disponibilité**.

    > [!NOTE]
    > Dans les régions avec des [zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), vous avez la possibilité de sélectionner aucune zone (option par défaut), une zone spécifique ou redondant dans une zone. Le choix dépendra de vos exigences spécifiques en matière de défaillance de domaine. Dans les régions sans Zones de disponibilité, ce champ n’apparaît pas. </br> Pour plus d’informations sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](../availability-zones/az-overview.md).

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

    > [!NOTE]
    > Dans cet exemple, nous avons créé une passerelle NAT pour fournir un accès Internet sortant. L’onglet Règles de trafic sortant dans la configuration est ignoré, car il n’est pas obligatoire avec la passerelle NAT. Pour plus d’informations sur la passerelle NAT Azure, consultez [Présentation de NAT de réseau virtuel Azure ?](../virtual-network/nat-gateway/nat-overview.md)
    > Pour plus d’informations sur les connexions sortantes dans Azure, consultez [SNAT (Traduction d'adresses réseau source) pour les connexions sortantes](../load-balancer/load-balancer-outbound-connections.md)

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Dans cette section, vous allez créer deux groupes de disponibilité avec deux machines virtuelles par groupe. Ces machines seront ajoutées au pool de back-ends de l’équilibreur de charge lors de la création. 

### <a name="create-first-set-of-vms"></a>Créer un premier ensemble de machines virtuelles

1. Sélectionnez **+ Créer une ressource** dans la section en haut à gauche du portail.

2. Dans **Nouveau**, sélectionnez **Calcul** > **Machine virtuelle**.

3. Sous l’onglet **Informations de base** de la page **Créer une machine virtuelle**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement |
    | Groupe de ressources | Sélectionnez **TutorLBmultiAVS-rg**. |
    | **Détails de l’instance** |   |
    | Nom de la machine virtuelle | Entrez **myVM1**. |
    | Région | Sélectionnez **(USA) USA Ouest 2**. |
    | Options de disponibilité | Sélectionnez **Groupe à haute disponibilité**. |
    | Groupe à haute disponibilité | Sélectionnez **Créer nouveau**. </br> Entrez **myAvailabilitySet1** dans **Nom**. </br> Sélectionnez **OK**. |
    | Image | Sélectionnez **Windows Server 2019 Datacenter - Gen1**. |
    | Instance Azure Spot | Conservez la valeur par défaut (case non cochée). |
    | Taille | Sélectionnez une taille pour la machine virtuelle. |
    | **Compte administrateur** |   |
    | Nom d’utilisateur | Entrez un nom d’utilisateur. |
    | Mot de passe | Entrez un mot de passe. |
    | **Règles des ports d’entrée** |   |
    | Aucun port d’entrée public | Sélectionnez **Aucun**. |

4. Sélectionnez l’onglet **Réseau**, ou sélectionnez **Suivant : Disques**, puis le bouton **Suivant : Réseau** situé au bas de la page.

5. Sous l’onglet **Mise en réseau**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Value |
    | ------- | ----- |
    | **Interface réseau** |   |
    | Réseau virtuel | Sélectionnez **myVNet**. |
    | Subnet | Sélectionnez **myBackendSubnet**. |
    | Adresse IP publique | Sélectionnez **Aucun**. |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **Avancé**. |
    | Configurer un groupe de sécurité réseau | Sélectionnez **Créer nouveau**. </br> Dans **Nom**, entrez **myNSG**. </br> Sélectionnez **+Ajouter une règle de trafic entrant** dans **Règles de trafic entrant**. </br> Sélectionnez **HTTP** pour le **Service**. </br> Entrez **myHTTPRule** pour **Nom**. </br> Sélectionnez **Ajouter**. </br> Sélectionnez **OK**. | 
    | **Équilibrage de charge** |   |
    | Placer cette machine virtuelle derrière une solution d’équilibrage de charge existante ? | Activez la case à cocher. |
    | **Paramètres d’équilibrage de charge** |   |
    | Options d’équilibrage de charge | Sélectionnez **Équilibreur de charge Azure**. |
    | Sélectionnez un équilibreur de charge | Sélectionnez **myLoadBalancer**. |
    | Sélectionnez un pool principal | Sélectionnez **MyBackendPool**. |

6. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.

7. Sélectionnez **Create** (Créer).

8. Répétez les étapes 1 à 7 pour créer la deuxième machine virtuelle du groupe. Remplacez les paramètres de la machine virtuelle par les informations suivantes :

    | Paramètre | Value |
    | ------- | ----- |
    | Nom | Entrez **myVM2**. |
    | Groupe à haute disponibilité | Sélectionnez **myAvailabilitySet1**. |
    | Réseau virtuel | Sélectionnez **myVNet**. |
    | Subnet | Sélectionnez **myBackendSubnet**. |
    | Adresse IP publique | Sélectionnez **Aucun**. |
    | Groupe de sécurité réseau | Sélectionnez **myNSG**. |
    | Options d’équilibrage de charge | Sélectionnez **Équilibreur de charge Azure**. |
    | Sélectionnez un équilibreur de charge | Sélectionnez **myLoadBalancer**. |
    | Sélectionnez un pool principal | Sélectionnez **MyBackendPool**. |

### <a name="create-second-set-of-vms"></a>Créer un deuxième ensemble de machines virtuelles

1. Sélectionnez **+ Créer une ressource** dans la section en haut à gauche du portail.

2. Dans **Nouveau**, sélectionnez **Calcul** > **Machine virtuelle**.

3. Sous l’onglet **Informations de base** de la page **Créer une machine virtuelle**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement |
    | Groupe de ressources | Sélectionnez **TutorLBmultiAVS-rg**. |
    | **Détails de l’instance** |   |
    | Nom de la machine virtuelle | Entrez **myVM3**. |
    | Région | Sélectionnez **(USA) USA Ouest 2**. |
    | Options de disponibilité | Sélectionnez **Groupe à haute disponibilité**. |
    | Groupe à haute disponibilité | Sélectionnez **Créer nouveau**. </br> Entrez **myAvailabilitySet2** dans **Nom**. </br> Sélectionnez **OK**. |
    | Image | Sélectionnez **Windows Server 2019 Datacenter - Gen1**. |
    | Instance Azure Spot | Conservez la valeur par défaut (case non cochée). |
    | Taille | Sélectionnez une taille pour la machine virtuelle. |
    | **Compte administrateur** |   |
    | Nom d’utilisateur | Entrez un nom d’utilisateur. |
    | Mot de passe | Entrez un mot de passe. |
    | **Règles des ports d’entrée** |   |
    | Aucun port d’entrée public | Sélectionnez **Aucun**. |

4. Sélectionnez l’onglet **Réseau**, ou sélectionnez **Suivant : Disques**, puis le bouton **Suivant : Réseau** situé au bas de la page.

5. Sous l’onglet **Mise en réseau**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Value |
    | ------- | ----- |
    | **Interface réseau** |   |
    | Réseau virtuel | Sélectionnez **myVNet**. |
    | Subnet | Sélectionnez **myBackendSubnet**. |
    | Adresse IP publique | Sélectionnez **Aucun**. |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **Avancé**. |
    | Configurer un groupe de sécurité réseau | Sélectionnez **myNSG**. | 
    | **Équilibrage de charge** |   |
    | Placer cette machine virtuelle derrière une solution d’équilibrage de charge existante ? | Activez la case à cocher. |
    | **Paramètres d’équilibrage de charge** |   |
    | Options d’équilibrage de charge | Sélectionnez **Équilibreur de charge Azure**. |
    | Sélectionnez un équilibreur de charge | Sélectionnez **myLoadBalancer**. |
    | Sélectionnez un pool principal | Sélectionnez **MyBackendPool**. |

6. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.

7. Sélectionnez **Create** (Créer).

8. Répétez les étapes 1 à 7 pour créer la deuxième machine virtuelle du groupe. Remplacez les paramètres de la machine virtuelle par les informations suivantes :

    | Paramètre | Value |
    | ------- | ----- |
    | Nom | Entrez **myVM4**. |
    | Groupe à haute disponibilité | Sélectionnez **myAvailabilitySet2**. |
    | Réseau virtuel | Sélectionnez **myVNet**. |
    | Groupe de sécurité réseau | Sélectionnez **myNSG**. |
    | Options d’équilibrage de charge | Sélectionnez **Équilibreur de charge Azure**. |
    | Sélectionnez un équilibreur de charge | Sélectionnez **myLoadBalancer**. |
    | Sélectionnez un pool principal | Sélectionnez **MyBackendPool**. |

## <a name="install-iis"></a>Installer IIS

Dans cette section, vous allez utiliser l’hôte Azure Bastion que vous avez créé précédemment pour vous connecter aux machines virtuelles et installer IIS.

1. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**.

2. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

3. Sélectionnez **myVM1**.

4. Dans la page **Vue d’ensemble** de myVM1, sélectionnez **Se connecter** > **Bastion**.

5. Sélectionnez **Utiliser Bastion**.

6. Entrez le **Nom d’utilisateur** et le **Mot de passe** que vous avez créés lors de la création de la machine virtuelle.

7. Sélectionnez **Connecter**.

7. Sur le bureau du serveur, accédez à **Outils d’administration Windows** > **Windows PowerShell**.

8. Dans la fenêtre PowerShell, exécutez les commandes suivantes pour :

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

9. Répétez les étapes 1 à 8 pour **myVM2**, **myVM3** et **myVM4**.

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge

Dans cette section, vous allez découvrir l’adresse IP publique de l’équilibreur de charge. Vous allez utiliser l’adresse IP pour tester le fonctionnement de l’équilibreur de charge.

1. Dans la zone de recherche située en haut du portail, entrez **IP publique**.

2. Sélectionnez **Adresses IP publique** dans les résultats de la recherche.

3. Sélectionnez **myPublicIP-lb**.

4. Notez l’adresse IP publique indiquée dans **Adresse IP** dans la page **Vue d’ensemble** de **myPublicIP-lb** :

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/find-public-ip.png" alt-text="Recherchez l’adresse IP publique de l’équilibreur de charge." border="true":::

5. Ouvrez un navigateur web, puis entrez l’adresse IP publique dans la barre d’adresses :

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/verify-load-balancer.png" alt-text="Testez l’équilibreur de charge avec un navigateur web." border="true":::

6. Sélectionnez Actualiser dans le navigateur pour afficher le trafic équilibré sur les autres machines virtuelles du pool de back-ends.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser cette application, supprimez l’équilibreur de charge et les ressources de prise en charge en effectuant les étapes suivantes :

1. Dans la zone de recherche située en haut du portail, entrez **Groupe de ressources**.

2. Sélectionnez **Groupes de ressources** dans les résultats de la recherche.

3. Sélectionnez **TutorLBmultiAVS-rg**.

4. Dans la page de vue d’ensemble de **TutorLBmultiAVS-rg**, sélectionnez **Supprimer le groupe de ressources**.

5. Entrez **TutorLBmultiAVS-rg** dans **ENTREZ LE NOM DU GROUPE DE RESSOURCES**.

6. Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous :

* Avez créé un réseau virtuel et un hôte Azure Bastion.
* Avez créé un équilibreur de charge Azure standard.
* Avez créé deux groupes à haute disponibilité avec deux machines virtuelles par groupe.
* Avez installé IIS et testé l’équilibreur de charge.

Passez à l’article suivant pour découvrir comment créer un équilibreur de charge Azure interrégional :
> [!div class="nextstepaction"]
> [Créer un équilibreur de charge interrégional](tutorial-cross-region-portal.md)