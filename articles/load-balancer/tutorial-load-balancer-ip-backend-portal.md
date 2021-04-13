---
title: 'Tutoriel : Créer un équilibreur de charge public avec un back-end basé sur IP - Portail Azure'
titleSuffix: Azure Load Balancer
description: Dans ce tutoriel, découvrez comment créer un équilibreur de charge public avec un pool de back-ends basés sur IP.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 3/31/2021
ms.custom: template-tutorial
ms.openlocfilehash: f8174d46d1674e0cf81e36e89f6fb6180091a9b9
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123012"
---
# <a name="tutorial-create-a-public-load-balancer-with-an-ip-based-backend-using-the-azure-portal"></a>Tutoriel : Créer un équilibreur de charge public avec un back-end basé sur IP à l’aide du portail Azure

Dans ce tutoriel, vous allez découvrir comment créer un équilibreur de charge public avec un pool de back-ends basés sur IP. 

Un déploiement traditionnel d’Azure Load Balancer utilise l’interface réseau des machines virtuelles. Avec un back-end basé sur IP, les machines virtuelles sont ajoutées au back-end par adresse IP.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créez un réseau virtuel
> * Créer une passerelle NAT pour la connectivité sortante
> * Créer un équilibreur de charge Azure
> * Créer un pool de back-ends basés sur IP
> * Créer deux machines virtuelles
> * Tester l’équilibreur de charge
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Dans cette section, vous allez créer un réseau virtuel pour l’équilibreur de charge, la passerelle NAT et les machines virtuelles.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. En haut à gauche de l’écran, sélectionnez **Créer une ressource > Réseau > Réseau virtuel**, ou recherchez **Réseau virtuel** à partir de la zone de recherche.

2. Sélectionnez **Create** (Créer). 

3. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Sélectionner votre abonnement Azure                                  |
    | Groupe de ressources   | Sélectionner **TutorPubLBIP-rg** |
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
## <a name="create-nat-gateway"></a>Créer une passerelle NAT

Dans cette section, vous allez créer une passerelle NAT et l’affecter au sous-réseau dans le réseau virtuel que vous avez créé précédemment.

1. En haut à gauche de l’écran, sélectionnez **Créer une ressource > Réseau > Passerelle NAT**, ou recherchez **Passerelle NAT** dans la zone de recherche.

2. Sélectionnez **Create** (Créer). 

3. Dans **Créer une passerelle NAT (traduction d’adresses réseau)** , entrez ou sélectionnez les informations suivantes sous l’onglet **Informations de base** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Sélectionnez votre abonnement Azure.                                  |
    | Groupe de ressources   | Sélectionnez **Créer**, puis entrez **TutorPubLBIP-rg** dans la zone de texte. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |                                                                 |
    | Nom             | Entrez **myNATgateway**                                    |
    | Région           | Sélectionnez **(États-Unis) USA Est**  |
    | Zone de disponibilité | Sélectionnez **Aucun**. |
    | Délai d’inactivité (minutes) | Entrez **10**. |

4. Sélectionnez l’onglet **IP sortante**, ou sélectionnez le bouton **Suivant : IP sortante** situé au bas de la page.

5. Sous l’onglet **IP sortante**, entrez ou sélectionnez les informations suivantes :

    | **Paramètre** | **Valeur** |
    | ----------- | --------- |
    | Adresses IP publiques | Sélectionnez **Créer une adresse IP publique**. </br> Dans **Nom**, entrez **myPublicIP-NAT**. </br> Sélectionnez **OK**. |

6. Sélectionnez l’onglet **Sous-réseau**, ou sélectionnez le bouton **Suivant : Sous-réseau** situé au bas de la page.

7. Sous l’onglet **Sous-réseau**, sélectionnez **myVNet** dans la zone de liste déroulante **Réseau virtuel**.

8. Cochez la case en regard de **myBackendSubnet**.

9. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.

10. Sélectionnez **Create** (Créer).
## <a name="create-load-balancer"></a>Créer un équilibreur de charge

Dans cette section, vous allez créer un Standard Load Balancer Azure : 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Créer une ressource**. 
3. Dans la zone de recherche, entrez **Équilibreur de charge**. Sélectionnez **Équilibreur de charge** dans les résultats de recherche.
4. Dans la page **Équilibreur de charge**, sélectionnez **Créer**.
5. Sur la page **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | **Détails du projet** |   |
    | Abonnement               | Sélectionnez votre abonnement.    |    
    | Resource group         | Sélectionnez **TutorPubLBIP-rg**.|
    | **Détails de l’instance** |   |
    | Nom                   | Entrez **myLoadBalancer**                                   |
    | Région         | Sélectionnez **(États-Unis) USA Est**.                                        |
    | Type          | Sélectionnez **Public**.                                        |
    | SKU           | Conservez la valeur par défaut **Standard**. |
    | Niveau          | Conservez la valeur par défaut **Régional**. |
    | **Adresse IP publique** |   |
    | Adresse IP publique | Sélectionnez **Créer nouveau**. </br> Si vous avez une adresse IP publique existante que vous souhaitez utiliser, sélectionnez **Utiliser l’existant**. |
    | Nom de l’adresse IP publique | Entrez **myPublicIP-LB** dans la zone de texte.|
    | Zone de disponibilité | Sélectionnez **Redondant interzone** pour créer un équilibreur de charge résilient. Pour créer un équilibreur de charge zonal, sélectionnez une zone spécifique parmi 1, 2 ou 3 |
    | Ajouter une adresse IPv6 publique | Sélectionnez **Non**. </br> Pour plus d’informations sur les adresses IPv6 et l’équilibreur de charge, consultez [Qu’est-ce que le protocole IPv6 pour réseau virtuel Azure ?](../virtual-network/ipv6-overview.md)  |
    | Préférence de routage | Laissez la valeur par défaut **Microsoft network**. </br> Pour plus d’informations sur la préférence de routage, consultez la section [Qu’est-ce qu’une préférence de routage (préversion) ?](../virtual-network/routing-preference-overview.md). |

6. Acceptez les valeurs par défaut pour les paramètres restants, puis sélectionnez **Vérifier + créer**.

7. Sous l’onglet **Review + create (Vérifier + créer)** , sélectionnez **Créer**.

## <a name="create-load-balancer-resources"></a>Créer les ressources d’équilibreur de charge

Dans cette section, vous configurez :

* Les paramètres d’équilibreur de charge d’un pool d’adresses de back-ends
* Une sonde d’intégrité
* Une règle d’équilibreur de charge

### <a name="create-a-backend-pool"></a>Créer un pool principal

Un pool d’adresses de back-ends contient les adresses IP des cartes d’interface réseau virtuelles connectées à l’équilibreur de charge. 

Créez le pool d’adresses principal **myBackendPool** afin d’inclure des machines virtuelles pour l’équilibrage de charge du trafic Internet.

1. Sélectionnez **Tous les services** dans le menu de gauche, **Toutes les ressources**, puis **myLoadBalancer** dans la liste des ressources.

2. Sous **Paramètres**, sélectionnez **Pools de back-ends**, puis **Ajouter**.

3. Dans la page **Ajouter un pool de back-ends**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **myBackendPool**. |
    | Réseau virtuel | Sélectionnez **myVNet**. |
    | Configuration d’un pool de back-ends | Sélectionnez **Adresse IP**. |
    | Version de l’adresse IP | Sélectionnez **IPv4**. |

4. Sélectionnez **Ajouter**.

### <a name="create-a-health-probe"></a>Créer une sonde d’intégrité

L’équilibreur de charge supervise l’état de votre application avec une sonde d’intégrité. 

La sonde d’intégrité ajoute ou supprime des machines virtuelles dans l’équilibreur de charge en fonction de leur réponse aux contrôles d’intégrité. 

Créez une sonde d’intégrité nommée **myHealthProbe** pour surveiller l’intégrité des machines virtuelles.

1. Sélectionnez **Tous les services** dans le menu de gauche, **Toutes les ressources**, puis **myLoadBalancer** dans la liste des ressources.

2. Sous **Paramètres**, sélectionnez **Sondes d’intégrité**, puis **+ Ajouter**.
    
    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **MyHealthProbe**. |
    | Protocol | Sélectionnez **TCP**. |
    | Port | Entrez **80**.|
    | Intervalle | Entrez **15** pour **l’intervalle** en secondes entre les tentatives de la sonde. |
    | Seuil de défaillance sur le plan de l’intégrité | Sélectionnez **2**. |
   
3. Laissez les autres valeurs par défaut et sélectionnez **Ajouter**.

### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge

Une règle d’équilibrage de charge est utilisée pour définir la distribution du trafic vers les machines virtuelles. Vous définissez la configuration IP front-end pour le trafic entrant et le pool d’adresses IP de back-ends pour la réception du trafic. Les ports source et de destination sont définis dans la règle. 

Dans cette section, vous allez créer une règle d’équilibreur de charge :

* A pour nom **myHTTPRule**.
* Se trouve dans le front-end nommé **LoadBalancerFrontEnd**.
* Écoute sur le **Port 80**.
* Dirige le trafic à charge équilibrée vers le back-end nommé **myBackendPool** sur le **Port 80**.

1. Sélectionnez **Tous les services** dans le menu de gauche, **Toutes les ressources**, puis **myLoadBalancer** dans la liste des ressources.

2. Sous **Paramètres**, sélectionnez **Règles d’équilibrage de charge**, puis **+ Ajouter**.

3. Entrez ou sélectionnez les informations suivantes pour la règle d’équilibreur de charge :
    
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
    | Persistance de session | Conservez la valeur par défaut **Aucune**. |
    | Délai d’inactivité (minutes) | Entrez **15** minutes. |
    | Réinitialisation du protocole TCP | Sélectionnez **Enabled**. |
    | IP flottante | Sélectionnez **Désactivé**. |
    | Traduction d’adresses réseau (SNAT) sources sortante | Sélectionnez **(Recommandé) Utiliser des règles de trafic sortant pour fournir aux membres du pool de back-ends l’accès à Internet**. |

4. Ne modifiez aucune des autres valeurs par défaut, puis sélectionnez **Ajouter**.

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Dans cette section, vous allez créer deux machines virtuelles (**myVM1** et **myVM2**) dans deux zones différentes (**Zone 1** et **Zone 2**).

Ces machines virtuelles sont ajoutées au pool de back-ends de l’équilibreur de charge créé auparavant.

1. En haut à gauche du portail, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**. 
   
2. Dans **Créer une machine virtuelle**, entrez ou sélectionnez les valeurs sous l’onglet **Informations de base** :

    | Paramètre | Valeur                                          |
    |-----------------------|----------------------------------|
    | **Détails du projet** |  |
    | Abonnement | Sélectionner votre abonnement Azure |
    | Groupe de ressources | Sélectionner **TutorPubLBIP-rg** |
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

    | Paramètre | Valeur |
    |-|-|
    | **Interface réseau** |  |
    | Réseau virtuel | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Adresse IP publique | Sélectionnez **Aucun**. |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **Avancé**|
    | Configurer un groupe de sécurité réseau | Sélectionnez **Créer nouveau**. </br> Dans **Créer un groupe de sécurité réseau**, entrez **myNSG** dans **Nom**. </br> Dans **Règles de trafic entrant**, sélectionnez **+Ajouter une règle de trafic entrant**. </br> Sous **Service**, sélectionnez **HTTP**. </br> Dans **Priorité**, entrez **100**. </br> Sous **Nom**, entrez **myHTTPRule** </br> Sélectionnez **Ajouter** </br> Sélectionnez **OK**. |
    | **Équilibrage de charge**  |
    | Placer cette machine virtuelle derrière une solution d’équilibrage de charge existante ? | Activez la case à cocher.|
    | **Paramètres d’équilibrage de charge** |
    | Options d’équilibrage de charge | Sélectionnez **Équilibreur de charge Azure** |
    | Sélectionnez un équilibreur de charge | Sélectionnez **myLoadBalancer**  |
    | Sélectionnez un pool principal | Sélectionnez **myBackendPool** |
   
5. Sélectionnez **Revoir + créer**. 
  
6. Passez en revue les paramètres, puis sélectionnez **Créer**.

7. Effectuez les étapes 1 à 6 pour créer une machine virtuelle avec les valeurs suivantes, avec tous les autres paramètres identiques à ceux de **myVM1** :

    | Paramètre | Machine virtuelle 2 |
    | ------- | ----- |
    | Nom |  **myVM2** |
    | Zone de disponibilité | **2** |
    | Groupe de sécurité réseau | Sélectionnez le groupe **myNSG** existant| 

## <a name="install-iis"></a>Installer IIS

1. Sélectionnez **Tous les services** dans le menu de gauche, sélectionnez **Toutes les ressources** puis, dans la liste de ressources, sélectionnez **myVM1** qui se trouve dans le groupe de ressources **TutorPubLBIP-rg**.

2. Dans la page **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Bastion**.

3. Sélectionnez le bouton **Utiliser Bastion**.

4. Entrez le nom d’utilisateur et le mot de passe saisis pendant la création de la machine virtuelle.

5. Sélectionnez **Connecter**.

6. Sur le bureau du serveur, accédez à **Outils d’administration Windows** > **Windows PowerShell**.

7. Dans la fenêtre PowerShell, exécutez les commandes suivantes pour :

    * Installer le serveur IIS
    * Supprimer le fichier iisstart.htm par défaut
    * Ajoutez un nouveau fichier iisstart.htm qui affiche le nom de la machine virtuelle :

   ```powershell
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
    Remove-Item C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Fermez la session Bastion avec **myVM1**.

9. Répétez les étapes 1 à 7 pour installer IIS et le fichier iisstart.htm mis à jour sur **myVM2**.

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge

1. Recherchez l’adresse IP publique de l’équilibreur de charge sur l’écran **Vue d’ensemble**. Sélectionnez **Tous les services** dans le menu de gauche, sélectionnez **Toutes les ressources**, puis **myPublicIP-LB**.

2. Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur. La page par défaut du serveur Web IIS s’affiche sur le navigateur.

   ![Serveur Web IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Pour voir l’équilibreur de charge répartir le trafic vers myVM2, forcez l’actualisation de votre navigateur web à partir de l’ordinateur client.
## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser cette application, supprimez le réseau virtuel, la machine virtuelle et la passerelle NAT en effectuant les étapes suivantes :

1. Dans le menu de gauche, sélectionnez **Groupes de ressources**.

2. Sélectionnez le groupe de ressources **TutorPubLBIP-rg**.

3. Sélectionnez **Supprimer le groupe de ressources**.

4. Entrez **TutorPubLBIP-rg**, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

* Créé un réseau virtuel
* Créé une passerelle NAT
* Créé un équilibreur de charge avec un pool de back-ends basés sur IP
* Testé l’équilibreur de charge

Passez à l’article suivant pour découvrir comment créer un équilibreur de charge interrégional :
> [!div class="nextstepaction"]
> [Créer un équilibreur de charge Azure interrégional à l’aide du portail Azure](tutorial-cross-region-portal.md)
