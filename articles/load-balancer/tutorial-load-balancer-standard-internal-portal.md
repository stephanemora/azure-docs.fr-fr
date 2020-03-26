---
title: 'Tutoriel : Créer un équilibreur de charge interne - Portail Azure'
titleSuffix: Azure Load Balancer
description: Ce tutoriel montre comment créer un équilibreur de charge interne Standard à l’aide du portail Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2020
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 918a7700df6b5be3ebca7949875127e42f8d3a91
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75780694"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-standard-load-balancer-in-the-azure-portal"></a>Tutoriel : Équilibrer la charge du trafic interne avec un équilibreur de charge Standard dans le portail Azure

L’équilibrage de charge offre un niveau plus élevé de disponibilité et d’évolutivité en répartissant les requêtes entrantes sur des machines virtuelles. Vous pouvez utiliser le portail Azure pour créer un équilibreur de charge Standard et équilibrer le trafic interne entre des machines virtuelles. Ce tutoriel montre comment créer et configurer un équilibreur de charge interne, des serveurs back-end et des ressources réseau au niveau tarifaire Standard.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

Si vous préférez, vous pouvez effectuer ces étapes dans [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) ou [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) au lieu du portail.

Pour suivre les étapes de ce tutoriel, connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Créer un réseau virtuel, des serveurs back-end et une machine virtuelle de test

Créez d’abord un réseau virtuel. Sur le réseau virtuel, créez deux machines virtuelles à utiliser pour le pool de back-ends de votre équilibreur de charge Standard, ainsi qu’une troisième machine virtuelle à utiliser pour tester l’équilibreur de charge. 

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

1. En haut à gauche du portail, sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.
   
1. Dans le volet **Créer un réseau virtuel**, tapez ou sélectionnez les valeurs suivantes :
   
   - **Name** : Tapez **MyVNet**.
   - **Groupe de ressources** : Sélectionnez **Créer**, entrez **MyResourceGroupLB**, puis sélectionnez **OK**. 
   - **Sous-réseau** > **Nom** : Entrez **MyBackendSubnet**.
   
1. Sélectionnez **Create** (Créer).

   ![Créez un réseau virtuel](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

1. En haut à gauche du portail, sélectionnez **Créer une ressource** > **Calcul** > **Windows Server 2016 Datacenter**. 
   
1. Dans **Créer une machine virtuelle**, tapez ou sélectionnez les valeurs suivantes sous l’onglet **De base** :
   - **Abonnement** > **Groupe de ressources** : Faites défiler la liste et sélectionnez **MyResourceGroupLB**.
   - **Détails de l’instance** > **Nom de la machine virtuelle** : Entrez **MyVM1**.
   - **Détails de l’instance** > **Région** : Sélectionnez **USA Est**.
  
   
1. Sélectionnez l'onglet **Mise en réseau** ou choisissez **Suivant : Disques**, puis **Suivant : Mise en réseau**. 
   
   Vérifiez que les éléments suivants sont sélectionnés :
   - **Réseau virtuel** : **MyVNet**
   - **Sous-réseau** : **MyBackendSubnet**
   - **Groupe de sécurité réseau de la carte réseau** : Sélectionnez **De base**.
   - **Adresse IP publique** > Sélectionnez **Créer**, entrez les valeurs suivantes, puis sélectionnez **OK** :
       - **Name** : **MyVM1-IP**
       - **SKU** : sélectionnez **Standard**.
   - **Ports d’entrée publics** : Sélectionnez **Autoriser les ports sélectionnés**.
   - **Sélectionner des ports d’entrée** : faites défiler et sélectionnez **RDP (3389)** .

   
   
1. Sélectionnez l’onglet **Gestion** ou sélectionnez **Suivant** > **Gestion**. Sous **Supervision**, définissez **Diagnostics de démarrage** sur **Désactivé**.
   
1. Sélectionnez **Revoir + créer**.
   
1. Passez en revue les paramètres, puis sélectionnez **Créer**. 

1. Suivez la procédure de création d’une deuxième machine virtuelle nommée **MyVM2**, avec tous les autres paramètres identiques à ceux de MyVM1. 

1. Suivez les étapes pour créer une troisième machine virtuelle nommée **MyTestVM**. 

## <a name="create-a-standard-load-balancer"></a>Créer un équilibreur de charge standard

Créez un équilibreur de charge interne standard à l’aide du portail. Le nom et l’adresse IP que vous créez sont automatiquement configurés comme front-end de l’équilibreur de charge.

1. En haut à gauche du portail, sélectionnez **Créer une ressource** > **Mise en réseau** > **Équilibreur de charge**.
   
2. Sous l’onglet **De base** de la page **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis choisissez **Vérifier + créer** :

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | Abonnement               | Sélectionnez votre abonnement.    |    
    | Resource group         | Sélectionnez **Créer** et tapez *MyResourceGroupLB* dans la zone de texte.|
    | Nom                   | *myLoadBalancer*                                   |
    | Région         | Sélectionnez **USA Est**.                                        |
    | Type          | sélectionnez **Interne**.                                        |
    | SKU           | Sélectionnez **Standard**.                          |
    | Réseau virtuel           | Sélectionnez *MyVNet*.                          |    
    | Affectation d’adresses IP              | Sélectionnez **Statique**.   |
    | Adresse IP privée|entrez une adresse qui se trouve dans l'espace d'adressage de votre réseau virtuel et de votre sous-réseau, par exemple *10.3.0.7*.  |

3. Sous l’onglet **Vérifier + créer**, cliquez sur **Créer**. 
   

## <a name="create-standard-load-balancer-resources"></a>Créer des ressources d’équilibreur de charge Standard

Dans cette section, vous configurez les paramètres de l’équilibreur de charge pour un pool d’adresses back-end et une sonde d’intégrité, puis spécifiez les règles de l’équilibreur de charge.

### <a name="create-a-back-end-address-pool"></a>Créer un pool d’adresses principal

Pour répartir le trafic entre les machines virtuelles, l’équilibreur de charge utilise un pool d’adresses back-end. Le pool d’adresses back-end contient les adresses IP des cartes d’interface réseau virtuelles connectées à l’équilibreur de charge. 

**Pour créer un pool d’adresses back-end qui inclut VM1 et VM2 :**

1. Sélectionnez **Toutes les ressources** dans le menu de gauche, puis sélectionnez **MyLoadBalancer** dans la liste de ressources.
   
1. Sous **Paramètres**, sélectionnez **Pools principaux**, puis **Ajouter**.
   
1. Dans la page **Ajouter un pool de backends**, tapez ou sélectionnez les valeurs suivantes :
   
   - **Name** : entrez **MyBackendPool**.
   
1. Sous **Machines virtuelles**. 
   1. Ajoutez **MyVM1** et **MyVM2** au pool back-end.
   2. Une fois chaque machine ajoutée, faites défiler la liste déroulante vers le bas, puis sélectionnez sa **Configuration IP réseau**. 
     
1. Sélectionnez **Ajouter**.
   
   ![Ajouter le pool d’adresses back-end](./media/tutorial-load-balancer-standard-internal-portal/3-load-balancer-backend-02.png)
   
1. Dans la page **Pools back-ends**, développez **MyBackendPool**, puis vérifiez que **VM1** et **VM2** sont listées.

### <a name="create-a-health-probe"></a>Créer une sonde d’intégrité

Pour permettre à l’équilibreur de charge de superviser l’état d’une machine virtuelle, vous utilisez une sonde d’intégrité. La sonde d’intégrité ajoute ou supprime dynamiquement des machines virtuelles de la rotation d’équilibrage de charge en fonction de leur réponse aux vérifications d’intégrité. 

**Pour créer une sonde d’intégrité en vue de superviser l’intégrité des machines virtuelles :**

1. Sélectionnez **Toutes les ressources** dans le menu de gauche, puis sélectionnez **MyLoadBalancer** dans la liste de ressources.
   
1. Sous **Paramètres**, sélectionnez **Health probes** (Sondes d’intégrité), puis **Ajouter**.
   
1. Dans la page **Ajouter une sonde d’intégrité**, tapez ou sélectionnez les valeurs suivantes :
   
   - **Name** : Entrez **MyHealthProbe**.
   - **Protocole** : Faites défiler et sélectionnez **HTTP**. 
   - **Port** : Entrez **80**. 
   - **Chemin d’accès** : Acceptez **/** comme URI par défaut. Vous pouvez remplacer cette valeur avec n’importe quel autre URI. 
   - **Intervalle** : Entrez **15**. L’intervalle est le nombre de secondes entre les tentatives de la sonde.
   - **Seuil de défaillance sur le plan de l’intégrité** : Entrez **2**. Cette valeur est le nombre d’échecs de sonde consécutifs qui se produisent avant qu’une machine virtuelle soit considérée comme défaillante.
   
1. Sélectionnez **OK**.
   
   ![Ajouter une sonde](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge

Une règle d’équilibreur de charge définit la distribution du trafic vers les machines virtuelles. La règle définit la configuration IP front-end pour le trafic entrant, le pool d’adresses IP front-end pour recevoir le trafic ainsi que les ports source et de destination nécessaires. 

La règle d’équilibreur de charge nommée **MyLoadBalancerRule** écoute sur le port 80 dans le front-end **LoadBalancerFrontEnd**. La règle envoie le trafic réseau au pool d’adresses front-end **MyBackEndPool**, ainsi que sur le port 80. 

**Pour créer la règle d’équilibreur de charge :**

1. Sélectionnez **Toutes les ressources** dans le menu de gauche, puis sélectionnez **MyLoadBalancer** dans la liste de ressources.
   
1. Sous **Paramètres**, sélectionnez **Règles d'équilibrage de charge**, puis **Ajouter**.
   
1. Dans la page **Ajouter une règle d’équilibrage de charge**, tapez ou sélectionnez les valeurs suivantes (si elles ne sont pas déjà présentes) :
   
   - **Name** : Entrez **MyLoadBalancerRule**.
   - **Adresse IP du serveur frontal :** entrez **LoadBalancerFrontEnd** si absent.
   - **Protocole** : Sélectionnez **TCP**.
   - **Port** : Entrez **80**.
   - **Port principal** : Entrez **80**.
   - **Pool principal** : Sélectionnez **MyBackendPool**.
   - **Sonde d’intégrité** : Sélectionnez **MyHealthProbe**. 
   
1. Sélectionnez **OK**.
   
   ![Ajouter une règle d’équilibreur de charge](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge

Installez Internet Information Services (IIS) sur les serveurs back-up, puis utilisez MyTestVM pour tester l’équilibreur de charge à l’aide de son adresse IP privée. Chaque machine virtuelle back-end affiche une version différente de la page web IIS par défaut. Ainsi, vous pouvez voir l’équilibreur de charge répartir les requêtes entre les deux machines virtuelles.

Sur le portail, dans la page **Vue d’ensemble** de **MyLoadBalancer**, recherchez son adresse IP sous **Adresse IP privée**. Pointez sur l’adresse, puis sélectionnez l’icône **Copier** pour la copier. Dans cet exemple, il s’agit de **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Se connecter aux machines virtuelles avec le Bureau à distance

Tout d’abord, connectez-vous aux trois machines virtuelles avec le Bureau à distance (RDP). 

>[!NOTE]
>Par défaut, les machines virtuelles ont déjà le port **RDP** ouvert pour autoriser l’accès du Bureau à distance. 

**Pour se connecter à distance (via RDP) aux machines virtuelles :**

1. Dans le portail, sélectionnez **Toutes les ressources** dans le menu de gauche. Dans la liste des ressources, sélectionnez chacune des machines virtuelles du groupe de ressources **MyResourceGroupLB**.
   
1. Dans la page **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Télécharger le fichier RDP**. 
   
1. Ouvrez le fichier RDP que vous avez téléchargé, puis sélectionnez **Se connecter**.
   
1. Dans l’écran Sécurité Windows, sélectionnez **Plus de choix**, puis **Utiliser un autre compte**. 
   
   Entrez votre nom d'utilisateur et votre mot de passe, puis sélectionnez **OK**.
   
1. Répondez **Oui** à toute invite de certificat. 
   
   Le bureau de la machine virtuelle s’ouvre dans une nouvelle fenêtre. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Installer IIS et remplacer la page IIS par défaut sur les machines virtuelles back-end

Sur chaque serveur back-end, utilisez PowerShell pour installer IIS et remplacer la page web IIS par défaut par une page personnalisée.

>[!NOTE]
>Vous pouvez également utiliser l’**Assistant Ajout de rôles et de fonctionnalités** dans **Gestionnaire de serveur** pour installer IIS. 

**Pour installer IIS et mettre à jour la page web par défaut avec PowerShell :**

1. Sur MyVM1 et MyVM2, lancez **Windows PowerShell** à partir du menu **Démarrer**. 

2. Exécutez les commandes suivantes pour installer IIS et remplacer la page web IIS par défaut :
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
      remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add custom htm file
      Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Fermez les connexions RDP à MyVM1 et MyVM2 en sélectionnant **Déconnexion**. N’arrêtez pas les machines virtuelles.

### <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge

1. Sur MyTestVM, ouvrez **Internet Explorer**, puis cliquez sur **OK** pour toutes les invites de configuration. 
   
1. Collez ou tapez l’adresse IP privée de l’équilibreur de charge (*10.3.0.7*) dans la barre d’adresses du navigateur. 
   
   La page par défaut personnalisée du serveur web IIS s’affiche dans le navigateur. Le message est soit **Hello World from MyVM1**, soit **Hello World from MyVM2**.
   
1. Actualisez le navigateur pour voir l’équilibreur de charge répartir le trafic entre les machines virtuelles. Vous serez peut-être amené à effacer le cache de votre navigateur entre chaque tentative.

   Parfois, la page **MyVM1** s’affiche, et parfois la page **MyVM2** s’affiche, selon la répartition des requêtes entre les machines virtuelles back-end. 

   ![Nouvelle page IIS par défaut](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer l’équilibreur de charge et toutes les ressources associées quand vous n’en avez plus besoin, ouvrez le groupe de ressources **MyResourceGroupLB**, puis sélectionnez **Supprimer un groupe de ressources**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un équilibreur de charge interne Standard. Vous avez créé et configuré des ressources réseau, des serveurs back-end, une sonde d’intégrité et des règles à utiliser avec l’équilibreur de charge. Vous avez installé IIS sur les machines virtuelles back-end et vous avez utilisé une machine virtuelle de test pour tester l’équilibreur de charge dans le navigateur. 

Découvrez maintenant comment équilibrer la charge des machines virtuelles entre les zones de disponibilité.

> [!div class="nextstepaction"]
> [Équilibrer la charge de machines virtuelles entre des zones de disponibilité](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
