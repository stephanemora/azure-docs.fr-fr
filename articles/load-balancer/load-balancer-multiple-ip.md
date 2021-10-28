---
title: 'Tutoriel : Équilibrer la charge de plusieurs configurations IP - Portail Azure'
titleSuffix: Azure Load Balancer
description: Dans cet article, découvrez l’équilibrage de charge entre des configurations de carte réseau principales et secondaires à l’aide du portail Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 08/08/2021
ms.custom: template-tutorial
ms.openlocfilehash: c0a5f441b27d379ff94db41fabfdf398a19ca667
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254662"
---
# <a name="tutorial-load-balance-multiple-ip-configurations-using-the-azure-portal"></a>Tutoriel : Équilibrer la charge de plusieurs configurations IP à l’aide du portail Azure 

Pour héberger plusieurs sites web, vous pouvez utiliser une autre interface réseau associée à une machine virtuelle. Azure Load Balancer assume le déploiement de l’équilibrage de charge afin de prendre en charge la haute disponibilité des sites web.

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer et configurer un réseau virtuel, un sous-réseau et une passerelle NAT
> * Créer deux machines virtuelles Windows Server
> * Créer une carte réseau secondaire et des configurations réseau pour chaque machine virtuelle
> * Créer deux sites web IIS (Internet Information Server) sur chaque machine virtuelle
> * Lier les sites web aux configurations réseau
> * Créer et configurer un équilibreur de charge Azure
> * Tester l’équilibreur de charge

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-network"></a>Création d’un réseau virtuel

Dans cette section, vous allez créer un réseau virtuel pour l’équilibreur de charge et les machines virtuelles.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Réseau virtuel**. Dans les résultats de la recherche, sélectionnez **Réseaux virtuels**.

3. Dans **Réseaux virtuels**, sélectionnez **+ Créer**.

4. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Sélectionner votre abonnement Azure                                  |
    | Groupe de ressources   | Sélectionnez **Créer nouveau**. </br> Dans **Nom**, entrez **TutorialLBIP-rg**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |                                                                 |
    | Nom             | Entrez **myVNet**                                    |
    | Région           | Sélectionnez **(Europe) Europe Ouest** |

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

10. Sélectionnez l'onglet **Sécurité** .

11. Sous **BastionHost**, sélectionnez **Activer**. Entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Nom du bastion | Entrez **myBastionHost** |
    | Espace d’adressage AzureBastionSubnet | Entrer **10.1.1.0/27** |
    | Adresse IP publique | Sélectionnez **Créer nouveau**. </br> Pour **Nom**, entrez **myBastionIP**. </br> Sélectionnez **OK**. |


12. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton **Vérifier + créer**.

13. Sélectionnez **Create** (Créer).

## <a name="create-nat-gateway"></a>Créer une passerelle NAT

Dans cette section, vous allez créer une passerelle NAT pour l’accès Internet sortant des ressources dans le réseau virtuel. 

1. Dans la zone de recherche située en haut du portail, entrez **Passerelle NAT**. Sélectionnez **Passerelle NAT** dans les résultats de la recherche.

2. Dans **Passerelles NAT**, sélectionnez **+ Créer**.

3. Dans **Créer une passerelle NAT (traduction d’adresses réseau)** , entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **TutorialLBIP-rg**. |
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

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Dans cette section, vous allez créer deux machines virtuelles pour héberger les sites web IIS.

1. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

2. Dans **Machines virtuelles**, sélectionnez **+ Créer**, puis **+ Machine virtuelle**.

3. Dans **Créer une machine virtuelle**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Value                                          |
    |-----------------------|----------------------------------|
    | **Détails du projet** |  |
    | Abonnement | Sélectionner votre abonnement Azure |
    | Groupe de ressources | Sélectionnez **TutorialLBIP-rg** |
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

3. Sélectionnez l'onglet **Mise en réseau** ou choisissez **Suivant : Disques**, puis **Suivant : Mise en réseau**.
  
4. Sous l’onglet Réseau, sélectionnez ou entrez :

    | Paramètre | Value |
    |-|-|
    | **Interface réseau** |  |
    | Réseau virtuel | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Adresse IP publique | Sélectionnez **Aucun**. |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **Avancé**|
    | Configurer un groupe de sécurité réseau | Sélectionnez **Créer nouveau**. </br> Dans **Créer un groupe de sécurité réseau**, entrez **myNSG** dans **Nom**. </br> Sous **Règles de trafic entrant**, sélectionnez **+Ajouter une règle de trafic entrant**. </br> Sous **Service**, sélectionnez **HTTP**. </br> Sous **Priorité**, entrez **100**. </br> Dans **Nom**, entrez **myNSGRule** </br> Sélectionnez **Ajouter** </br> Sélectionnez **OK**. |
   
7. Sélectionnez **Revoir + créer**. 
  
8. Passez en revue les paramètres, puis sélectionnez **Créer**.

9. Effectuez les étapes 1 à 8 pour créer une autre machine virtuelle avec les valeurs suivantes et tous les autres paramètres identiques à ceux de **myVM1** :

    | Paramètre | Machine virtuelle 2 |
    | ------- | ---- |
    | Nom |  **myVM2** |
    | Zone de disponibilité | **2** |
    | Groupe de sécurité réseau | Sélectionnez le groupe **myNSG** existant |

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-secondary-network-configurations"></a>Créer des configurations réseau secondaires

Dans cette section, vous allez remplacer l’adresse IP privée de la carte réseau existante de chaque machine virtuelle par **statique**. Ensuite, vous allez ajouter une nouvelle ressource de carte réseau à chaque machine virtuelle avec une configuration d’adresse IP privée **statique**.

1. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

2. Sélectionnez **myVM1**.

3. Si la machine virtuelle est en cours d’exécution, arrêtez-la. 

4. Dans **Paramètres**, sélectionnez **Mise en réseau**.

5. Dans **Mise en réseau**, sélectionnez le nom de l’interface réseau en regard d’**Interface réseau**. L’interface réseau commence par le nom de la machine virtuelle et un nombre aléatoire lui est attribué. Dans cet exemple, **myVM1266**.

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-nic.png" alt-text="Capture d’écran de la configuration réseau myVM1 dans le portail Azure.":::

6. Dans la page d’interface réseau, sélectionnez **Configurations IP** dans **Paramètres**.

7. Dans **Configurations IP**, sélectionnez **ipconfig1**.

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-ipconfig1.png" alt-text="Capture d’écran de la configuration de l’interface réseau myVM1.":::

8. Sélectionnez **Statique** dans **Affectation** dans la configuration **ipconfig1**.

9. Sélectionnez **Enregistrer**.

10. Retournez à la page **Vue d’ensemble** de **myVM1**.

11. Dans **Paramètres**, sélectionnez **Mise en réseau**.

12. Dans la page **Mise en réseau**, sélectionnez **Attacher l’interface réseau**.

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-attach-nic.png" alt-text="Capture d’écran de l’option Attacher l’interface réseau pour myVM1.":::

13. Dans **Attacher l’interface réseau**, sélectionnez **Créer et attacher une interface réseau**.

14. Dans **Créer une interface réseau**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Resource group | Sélectionnez **TutorialLBIP-rg**. |
    | **Interface réseau** |  |
    | Name | Entrez **myVM1NIC2** |
    | Subnet | Sélectionnez **myBackendSubnet (10.1.0.0/24)** . |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **Avancé**. |
    | Configurer un groupe de sécurité réseau | Sélectionnez **myNSG**. |
    | Affectation d’adresses IP privées | Sélectionnez **Statique**. |
    | Adresse IP privée | Entrez **10.1.0.6**. |

15. Sélectionnez **Create** (Créer). 

16. Démarrez la machine virtuelle.

17. Répétez les étapes 1 à 16 pour **myVM2**, en remplaçant les informations suivantes :

    | Paramètre | myVM2 |
    | ------  | ----- |
    | Name | **myVM2NIC2** |
    | Adresse IP privée | **10.1.0.7** |

## <a name="configure-virtual-machines"></a>Configurer des machines virtuelles

Vous allez vous connecter à **myVM1** et **myVM2** avec Azure Bastion et définir la configuration réseau secondaire dans cette section. Vous allez ajouter une route pour la passerelle de la configuration réseau secondaire. Vous installerez ensuite IIS sur chaque machine virtuelle et personnaliserez les sites web pour afficher le nom d’hôte de la machine virtuelle.

1. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

2. Sélectionnez **myVM1**.

3. Démarrez **myVM1**.

4. Dans **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Bastion**.

5. Sélectionnez **Utiliser Bastion**.

6. Entrez le nom d’utilisateur et le mot de passe que vous avez saisis lors de la création de la machine virtuelle.

7. Sélectionnez **Autoriser** pour permettre à Bastion d’utiliser le Presse-papiers.

8. Sur le bureau du serveur, accédez à Démarrer > Outils d’administration Windows > Windows PowerShell > Windows PowerShell.

9. Dans la fenêtre PowerShell, exécutez la commande `route print`, qui retourne une sortie similaire à la sortie suivante d’une machine virtuelle à deux interfaces réseau :

    ```console
    ===========================================================================
    Interface List
      6...00 22 48 86 00 53 ......Microsoft Hyper-V Network Adapter #2
     13...00 22 48 83 0b da ......Microsoft Hyper-V Network Adapter #3
      1...........................Software Loopback Interface 1
    ===========================================================================
    ```
    Dans cet exemple, **Microsoft Hyper-V Network Adapter #3 (interface 13)** est l’interface réseau secondaire à laquelle aucune passerelle par défaut n’est assignée.

10. Dans la fenêtre PowerShell, exécutez la commande `ipconfig /all` pour voir quelle adresse IP est assignée à l’interface réseau secondaire. Dans cet exemple, l’adresse 10.1.0.6 est affectée à l’interface 13. Aucune adresse de passerelle par défaut n’est retournée pour l’interface réseau secondaire.

11. Pour router tout le trafic destiné aux adresses hors du sous-réseau vers la passerelle, exécutez la commande suivante :

    ```console
    route -p add 0.0.0.0 MASK 0.0.0.0 10.1.0.1 METRIC 5015 IF 13
    ```

    Dans cet exemple, **10.1.0.1** est la passerelle par défaut pour le réseau virtuel que vous avez créé précédemment.

12. Exécutez les commandes suivantes dans les fenêtres PowerShell pour installer et configurer IIS et les sites web de test :

    ```powershell
    ## Install IIS and the management tools. ##
    Install-WindowsFeature -Name Web-Server -IncludeManagementTools

    ## Set the binding for the Default website to 10.1.0.4:80. ##
    $para1 = @{
        Name = 'Default Web Site'
        BindingInformation = '10.1.0.4:80:'
        Protocol = 'http'
    }
    New-IISSiteBinding @para1

    ## Remove the default site binding. ##
    $para2 = @{
        Name = 'Default Web Site'
        BindingInformation = '*:80:'
    }
    Remove-IISSiteBinding @para2 -Force

    ## Remove the default htm file. ##
    Remove-Item c:\inetpub\wwwroot\iisstart.htm

    ## Add a new htm file that displays the Contoso website. ##
    $para3 = @{
        Path = 'c:\inetpub\wwwroot\iisstart.htm'
        Value = $("Hello World from www.contoso.com" + "-" + $env:computername)
    }
    Add-Content @para3

    ## Create folder to host website. ##
    $para4 = @{
        Path = 'c:\inetpub\'
        Name = 'fabrikam'
        Type = 'directory'
    }
    New-Item @para4

     ## Create a new website and site binding for the second IP address 10.1.0.6. ##
    $para5 = @{
        Name = 'Fabrikam'
        PhysicalPath = 'c:\inetpub\fabrikam'
        BindingInformation = '10.1.0.6:80:'
    }
    New-IISSite @para5

    ## Add a new htm file that displays the Fabrikam website. ##
    $para6 = @{
        Path = 'C:\inetpub\fabrikam\iisstart.htm'
        Value = $("Hello World from www.fabrikam.com" + "-" + $env:computername)

    }
    Add-Content @para6

    ```
13. Fermez la connexion Bastion à **myVM1**.

14. Répétez les étapes 1 à 13 pour **myVM2**. Utilisez le code PowerShell ci-dessous pour **myVM2** pour l’installation d’IIS.

    ```powershell
    ## Install IIS and the management tools. ##
    Install-WindowsFeature -Name Web-Server -IncludeManagementTools

    ## Set the binding for the Default website to 10.1.0.5:80. ##
    $para1 = @{
        Name = 'Default Web Site'
        BindingInformation = '10.1.0.5:80:'
        Protocol = 'http'
    }
    New-IISSiteBinding @para1

    ## Remove the default site binding. ##
    $para2 = @{
        Name = 'Default Web Site'
        BindingInformation = '*:80:'
    }
    Remove-IISSiteBinding @para2

    ## Remove the default htm file. ##
    Remove-Item C:\inetpub\wwwroot\iisstart.htm

    ## Add a new htm file that displays the Contoso website. ##
    $para3 = @{
        Path = 'c:\inetpub\wwwroot\iisstart.htm'
        Value = $("Hello World from www.contoso.com" + "-" + $env:computername)
    }
    Add-Content @para3

    ## Create folder to host website. ##
    $para4 = @{
        Path = 'c:\inetpub\'
        Name = 'fabrikam'
        Type = 'directory'
    }
    New-Item @para4

    ## Create a new website and site binding for the second IP address 10.1.0.7. ##
    $para5 = @{
        Name = 'Fabrikam'
        PhysicalPath = 'c:\inetpub\fabrikam'
        BindingInformation = '10.1.0.7:80:'
    }
    New-IISSite @para5

    ## Add a new htm file that displays the Fabrikam website. ##
    $para6 = @{
        Path = 'C:\inetpub\fabrikam\iisstart.htm'
        Value = $("Hello World from www.fabrikam.com" + "-" + $env:computername)
    }
    Add-Content @para6

    ```

## <a name="create-load-balancer"></a>Créer un équilibreur de charge

Dans cette section, vous créez un équilibreur de charge redondant dans une zone qui équilibre la charge des machines virtuelles. 

Avec la redondance dans une zone, une ou plusieurs zones de disponibilité peuvent échouer sans empêcher le chemin de données de survivre tant qu’une zone de la région reste intègre.

Lors de la création de l’équilibreur de charge, vous allez configurer les éléments suivants :

* Deux adresses IP de front-end, une pour chaque site web.
* Pool principal
* Règles d’équilibrage des charges entrantes

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

2. Dans la page **Équilibreur de charge**, sélectionnez **Créer**.

3. Dans l’onglet **Fonctions de base** de la page **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | **Détails du projet** |   |
    | Abonnement               | Sélectionnez votre abonnement.    |    
    | Resource group         | Sélectionnez **TutorialLBIP-rg**. |
    | **Détails de l’instance** |   |
    | Nom                   | Entrez **myLoadBalancer**                                   |
    | Région         | Sélectionnez **(Europe) Europe Ouest**.                                        |
    | Type          | Sélectionnez **Public**.                                        |
    | SKU           | Conservez la valeur par défaut **Standard**. |
    | Niveau          | Conservez la valeur par défaut **Régional**. |

4. Sélectionnez **Suivant : configuration de l’adresse IP front-end** au bas de la page.

5. Dans **Configuration IP de front-end**, sélectionnez **+ Ajouter une adresse IP de front-end**.

6. Entrez **Frontend-contoso** dans **Nom**.

7. Sélectionnez **IPv4** pour **Version de l’adresse IP**.

    > [!NOTE]
    > IPv6 n’est pas pris en charge avec la préférence de routage ni l’équilibrage de charge entre les régions (niveau Global).

8. Sélectionnez **Adresse IP** pour **Type IP**.

    > [!NOTE]
    > Pour plus d’informations sur les préfixes IP, consultez [Préfixe d’adresse IP publique Azure](../virtual-network/ip-services/public-ip-address-prefix.md).

9. Sélectionnez **Créer une nouvelle** dans **Adresse IP publique**.

10. Dans **Ajouter une adresse IP publique**, entrez **myPublicIP-contoso** pour **Nom**.

11. Sélectionnez **Redondant dans une zone** dans **Zone de disponibilité**.

    > [!NOTE]
    > Dans les régions avec des [zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), vous avez la possibilité de sélectionner aucune zone (option par défaut), une zone spécifique ou redondant dans une zone. Le choix dépendra de vos exigences spécifiques en matière de défaillance de domaine. Dans les régions sans Zones de disponibilité, ce champ n’apparaît pas. </br> Pour plus d’informations sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](../availability-zones/az-overview.md).

12. Laissez la valeur par défaut **Réseau Microsoft** pour **Préférence de routage**.

13. Sélectionnez **OK**.

14. Sélectionnez **Ajouter**.

14. Sélectionnez **+ Ajouter une adresse IP de front-end**.

15. Entrez **Frontend-fabrikam** dans **Nom**.

7. Sélectionnez **IPv4** pour **Version de l’adresse IP**.

8. Sélectionnez **Adresse IP** pour **Type IP**.

9. Sélectionnez **Créer une nouvelle** dans **Adresse IP publique**.

10. Dans **Ajouter une adresse IP publique**, entrez **myPublicIP-fabrikam** pour **Nom**.

11. Sélectionnez **Redondant dans une zone** dans **Zone de disponibilité**.

14. Sélectionnez **Ajouter**.

15. Sélectionnez **Suivant : Pools de back-end** au bas de la page.

16. Sous l’onglet **Pools de back-ends**, sélectionnez **+ Ajouter un pool de back-ends**.

17. Entrez **myBackendPool-contoso** pour **Nom** dans **Ajouter un pool de back-ends**.

18. Sélectionnez **myVNet** dans **Réseau virtuel**.

19. Sélectionnez **Carte d’interface réseau** pour **Configuration du pool de back-ends**.

20. Sélectionnez **IPv4** pour **Version de l’adresse IP**.

21. Dans **Machines virtuelles**, sélectionnez **+ Ajouter**.

22. Sélectionnez **myVM1** et **myVM2** qui correspondent à **ipconfig1 (10.1.0.4)** et **ipconfig1 (10.1.0.5)** .

23. Sélectionnez **Ajouter**.

21. Sélectionnez **Ajouter**.

22. Sélectionnez **+ Ajouter un pool de back-ends**.

23. Entrez **myBackendPool-fabrikam** pour **Nom** dans **Ajouter un pool de back-ends**.

24. Sélectionnez **myVNet** dans **Réseau virtuel**.

19. Sélectionnez **Carte d’interface réseau** pour **Configuration du pool de back-ends**.

20. Sélectionnez **IPv4** pour **Version de l’adresse IP**.

21. Dans **Machines virtuelles**, sélectionnez **+ Ajouter**.

22. Sélectionnez **myVM1** et **myVM2** qui correspondent à **ipconfig1 (10.1.0.6)** et **ipconfig1 (10.1.0.7)** .

23. Sélectionnez **Ajouter**.

21. Sélectionnez **Ajouter**.

22. Sélectionnez le bouton **Suivant : Règles de trafic entrant** au bas de la page.

23. Dans **Règle d’équilibrage de la charge** sous l’onglet **Règles de trafic entrant**, sélectionnez **+ Ajouter une règle d’équilibrage de charge**.

24. Dans **Ajouter une règle d’équilibrage de charge**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Value |
    | ------- | ----- |
    | Name | Entrez **myHTTPRule-contoso** |
    | Version de l’adresse IP | Sélectionnez **IPv4**. |
    | Adresse IP du serveur frontal | Sélectionnez **Frontend-contoso**. |
    | Protocol | Sélectionnez **TCP**. |
    | Port | Entrez **80**. |
    | Port principal | Entrez **80**. |
    | Pool principal | Sélectionnez **myBackendPool-contoso**. |
    | Sonde d’intégrité | Sélectionnez **Créer nouveau**. </br> Dans **Nom**, entrez **myHealthProbe-contoso**. </br> Sélectionnez **HTTP** dans **Protocole**. </br> Laissez les autres valeurs par défaut et sélectionnez **OK**. |
    | Persistance de session | Sélectionnez **Aucun**. |
    | Délai d’inactivité (minutes) | Entrez ou sélectionnez **15**. |
    | Réinitialisation du protocole TCP | Sélectionnez **Enabled**. |
    | IP flottante | Sélectionnez **Désactivé**. |
    | Traduction d’adresses réseau (SNAT) sources sortante | Conservez la valeur par défaut **(Recommandé) Utiliser des règles de trafic sortant pour fournir aux membres du pool de back-ends l’accès à Internet**. |

25. Sélectionnez **Ajouter**.

26. Sélectionnez **Ajouter une règle d’équilibrage de charge**.

27. Dans **Ajouter une règle d’équilibrage de charge**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Value |
    | ------- | ----- |
    | Name | Entrez **myHTTPRule-fabrikam** |
    | Version de l’adresse IP | Sélectionnez **IPv4**. |
    | Adresse IP du serveur frontal | Sélectionnez **Frontend-fabrikam**. |
    | Protocol | Sélectionnez **TCP**. |
    | Port | Entrez **80**. |
    | Port principal | Entrez **80**. |
    | Pool principal | Sélectionnez **myBackendPool-fabrikam**. |
    | Sonde d’intégrité | Sélectionnez **Créer nouveau**. </br> Dans **Nom**, entrez **myHealthProbe-fabrikam**. </br> Sélectionnez **HTTP** dans **Protocole**. </br> Laissez les autres valeurs par défaut et sélectionnez **OK**. |
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
    > Pour plus d’informations sur les connexions sortantes dans Azure, consultez [SNAT (Source Network Address Translation) pour les connexions sortantes](../load-balancer/load-balancer-outbound-connections.md)

## <a name="test-load-balancer"></a>Tester l’équilibreur de charge

Dans cette section, vous allez découvrir l’adresse IP publique de chaque site web. Vous allez entrer l’IP dans un navigateur pour tester les sites web que vous avez créés précédemment.

1. Dans la zone de recherche située en haut du portail, entrez **IP publique**. Sélectionnez **Adresses IP publique** dans les résultats de la recherche.

2. Sélectionnez **myPublicIP-contoso**.

3. Copiez l’**adresse IP** figurant dans la page de présentation de **myPublicIP-contoso**.

    :::image type="content" source="./media/load-balancer-multiple-ip/public-ip-contoso.png" alt-text="Capture d’écran de l’adresse IP publique de myPublicIP-fabrikam.":::

4. Ouvrez un navigateur web et collez l’adresse IP publique dans la barre d’adresse.

    :::image type="content" source="./media/load-balancer-multiple-ip/test-contoso.png" alt-text="Capture d’écran du site web contoso dans le navigateur web.":::

5. Retournez à **Adresses IP publiques**. Sélectionnez **myPublicIP-fabrikam**.

6. Copiez l’**adresse IP** figurant dans la page de présentation de **myPublicIP-fabrikam**.

    :::image type="content" source="./media/load-balancer-multiple-ip/public-ip-fabrikam.png" alt-text="Capture d’écran de l’adresse IP publique de myPublicIP-contoso.":::

7. Ouvrez un navigateur web et collez l’adresse IP publique dans la barre d’adresse.

    :::image type="content" source="./media/load-balancer-multiple-ip/test-fabrikam.png" alt-text="Capture d’écran du site web fabrikam dans le navigateur web.":::

8. Pour tester l’équilibreur de charge, actualisez le navigateur ou arrêtez l’une des machines virtuelles.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser cette application, supprimez les machines virtuelles et l’équilibreur de charge en effectuant les étapes suivantes :

1. Dans la zone de recherche située en haut du portail, entrez **Groupe de ressources**.  Sélectionnez **Groupes de ressources** dans les résultats de la recherche.

2. Sélectionnez **TutorialLBIP-rg** dans **Groupes de ressources**.

3. Sélectionnez **Supprimer le groupe de ressources**.

4. Entrez **TutorialLBIP-rg** dans **TAPEZ LE NOM DU GROUPE DE RESSOURCES :** . Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour découvrir comment créer un équilibreur de charge interrégional :

> [!div class="nextstepaction"]
> [Créer un équilibreur de charge entre les régions à l’aide du portail Azure](tutorial-cross-region-portal.md)