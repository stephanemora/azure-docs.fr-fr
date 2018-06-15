---
title: 'Didacticiel : créer un équilibreur de charge de base public à l’aide du portail Azure | Microsoft Docs'
description: Ce didacticiel vous montre comment créer un équilibreur de charge de base interne à l’aide du portail Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: a16e9ad5b72d87614f5d3630e24e6aa36def8c51
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779553"
---
# <a name="tutorial-load-balance-internal-traffic-with-basic-load-balancer-to-vms-using-the-azure-portal"></a>Didacticiel : équilibrer la charge du trafic interne vers les machines virtuelles avec un équilibreur de charge de base à l’aide du portail Azure

L’équilibrage de charge offre un niveau plus élevé de disponibilité et d’évolutivité en répartissant les demandes entrantes sur plusieurs machines virtuelles. Vous pouvez utiliser le portail Azure pour équilibrer la charge du trafic interne vers les machines virtuelles avec un équilibreur de charge de base. Ce didacticiel montre comment créer des ressources réseau, des serveurs principaux et un équilibreur de charge de base interne.

Si vous préférez, vous pouvez suivre ce didacticiel en utilisant [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) ou [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel
1. Sur le côté gauche de l’écran, cliquez sur **Nouveau** > **Réseau** > **Réseau virtuel** et entrez ces valeurs pour le réseau virtuel :
    - *myVNet* : pour le nom du réseau virtuel.
    - *myResourceGroupILB* : pour le nom du groupe de ressources existant.
    - *myBackendSubnet* : pour le nom du sous-réseau.
2. Cliquez sur **Créer** pour créer le réseau virtuel.

## <a name="create-a-basic-load-balancer"></a>Créer un équilibreur de charge de base public
Créez un équilibreur de charge de base interne à l’aide du portail.

1. En haut à gauche de l’écran, cliquez sur **Créer une ressource** > **Mise en réseau** > **Équilibreur de charge**.
2. Dans la page **Créer un équilibreur de charge**, entrez les valeurs suivantes pour l’équilibreur de charge :
    - *myLoadBalancer* : pour le nom de l’équilibreur de charge.
    - **Internal** : pour le type de l’équilibreur de charge.
    - **Basic** : pour la version de référence SKU.
    - **10.1.0.7** : pour l’adresse IP privée statique.
    - *myVNet* : pour le réseau virtuel que vous choisissez dans la liste des réseaux existants.
    - *mySubnet* : pour le sous-réseau que vous choisissez dans la liste des sous-réseaux existants.
    - *myResourceGroupILB* : pour le nom du nouveau groupe de ressources que vous créez.
3. Cliquez sur **Créer** pour générer l’équilibreur de charge.
   
    ![Créer un équilibrage de charge](./media/tutorial-load-balancer-basic-internal-portal/1-load-balancer.png)


## <a name="create-backend-servers"></a>Créer des serveurs principaux

Dans cette section, vous allez créer deux machines virtuelles pour le pool principal de votre équilibreur de charge de base, puis installer IIS sur les machines virtuelles afin de tester l’équilibreur de charge.

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

1. Sur le côté gauche de l’écran, cliquez sur **Créer une ressource** > **Calcul** > **Windows Server 2016 Datacenter** et entrez ces valeurs pour la machine virtuelle :
    - *myVM1* : pour le nom de la machine virtuelle.        
    - *azureuser* : pour le nom d’utilisateur administrateur.   
    - *myResourceGroupILB* : dans **Groupe de ressources**, sélectionnez **Utiliser existant**, puis *myResourceGroupILB*.
2. Cliquez sur **OK**.
3. Sélectionnez **DS1_V2** pour la taille de la machine virtuelle, puis cliquez sur **Sélectionner**.
4. Entrez ces valeurs pour les paramètres de la machine virtuelle :
    - *myAvailabilitySet* : pour le nom du nouveau groupe à haute disponibilité que vous créez.
    -  *myVNet* : vérifiez qu’il est sélectionné en tant que réseau virtuel.
    - *myBackendSubnet* : vérifiez qu’il est sélectionné en tant que sous-réseau.
    - *myNetworkSecurityGroup* : pour le nom du nouveau groupe de sécurité réseau (pare-feu) que vous devez créer.
5. Cliquez sur **Désactivé** pour désactiver les diagnostics de démarrage.
6. Cliquez sur **OK**, vérifiez les paramètres sur la page de résumé, puis cliquez sur **Créer**.
7. À l’aide des étapes 1 à 6, créez une deuxième machine virtuelle nommée *VM2* avec *myAvailibilityset* en tant que groupe à haute disponibilité, *myVnet* en tant que réseau virtuel, *myBackendSubnet* en tant que sous-réseau et *myNetworkSecurityGroup* en tant que groupe de sécurité réseau. 

### <a name="install-iis-and-customize-the-default-web-page"></a>Installer IIS et personnaliser la page web par défaut

1. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis, dans la liste de ressources, cliquez sur **myVM1** qui se trouve dans le groupe de ressources *myResourceGroupILB*.
2. Sur la page **Vue d’ensemble**, cliquez sur **Connexion** à RDP dans la machine virtuelle.
3. Connectez-vous à la machine virtuelle.
4. Sur le bureau du serveur, accédez à **Outils d’administration Windows**>**Gestionnaire de serveur**.
5. Lancez Windows PowerShell sur VM1 et utilisez les commandes suivantes pour installer le serveur IIS et mettre à jour le fichier htm par défaut.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
5. Fermez la connexion RDP à *myVM1*.
6. Répétez les étapes 1 à 5 avec *myVM2* pour installer IIS et personnaliser la page web par défaut.

## <a name="create-nsg-rules"></a>Créer les règles du groupe de sécurité réseau

Dans cette section, vous créez des règles du groupe de sécurité réseau pour autoriser les connexions entrantes à l’aide de HTTP et RDP.

1. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis dans la liste de ressources, cliquez sur **myNetworkSecurityGroup** qui se trouve dans le groupe de ressources **myResourceGroupLB**.
2. Sous **Paramètres**, cliquez sur **Règles de sécurité entrantes**, puis sur **Ajouter**.
3. Entrez ces valeurs pour la règle de sécurité entrante nommée *myHTTPRule* afin d’autoriser les connexions HTTP entrantes à l’aide du port 80 :
    - *Service Tag* : pour **Source**.
    - *Internet* : pour **Balise de service source**
    - *80* : pour **Plages de port de destination**
    - *TCP* : pour **Protocole**
    - *Allow* : pour **Action**
    - *100* pour **Priorité**
    - *myHTTPRule* pour le nom
    - *Allow HTTP* pour la description
4. Cliquez sur **OK**.
 
5. Répétez les étapes 2 à 4 pour créer une autre règle nommée *myRDPRule* pour autoriser une connexion RDP entrante à l’aide du port 3389 avec les valeurs suivantes :
    - *Service Tag* : pour **Source**.
    - *Internet* : pour **Balise de service source**
    - *3389* : pour **Plages de port de destination**
    - *TCP* : pour **Protocole**
    - *Allow* : pour **Action**
    - *200* pour **Priorité**
    - *myRDPRule* pour le nom
    - *Allow RDP* pour la description

## <a name="create-basic-load-balancer-resources"></a>Créer des ressources d’équilibreur de charge de base

Dans cette section, vous configurez les paramètres de l’équilibreur de charge pour un pool d’adresses principal et une sonde d’intégrité et spécifiez l’équilibreur de charge et les règles NAT.


### <a name="create-a-backend-address-pool"></a>Créer un pool d’adresses principal

Pour distribuer le trafic vers les machines virtuelles, un pool d’adresses principal contient les adresses IP des cartes d’interface réseau virtuelles connectées à l’équilibreur de charge. Créez le pool d’adresses principal *myBackendPool* pour inclure *VM1* et *VM2*.

1. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis cliquez sur **myLoadBalancer** dans la liste des ressources.
2. Cliquez sur **Paramètres**, sur **Pools principaux**, puis sur **Ajouter**.
3. Sur la page **Ajouter un pool principal**, procédez comme suit :
    - Pour nom, tapez *myBackEndPool, comme nom du pool principal.
    - Pour **Associé à**, dans le menu déroulant, cliquez sur **Groupe à haute disponibilité**
    - Pour **Groupe à haute disponibilité**, cliquez sur **myAvailabilitySet**.
    - Cliquez sur **Ajouter une configuration IP de réseau cible** pour ajouter chaque machine virtuelle (*myVM1* & *myVM2*) créée au pool principal.
    - Cliquez sur **OK**.

        ![Ajout au pool d’adresses principal - ](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)

3. Vérifiez que le paramètre du pool principal de l’équilibreur de charge affiche les machines virtuelles **VM1** et **VM2**.

### <a name="create-a-health-probe"></a>Créer une sonde d’intégrité

Pour permettre à l’équilibreur de charge de surveiller l’état de votre application, vous utilisez une sonde d’intégrité. La sonde d’intégrité ajoute ou supprime dynamiquement des machines virtuelles de la rotation d’équilibrage de charge en fonction de leur réponse aux vérifications d’intégrité. Créez une sonde d’intégrité *myHealthProbe* pour surveiller l’intégrité des machines virtuelles.

1. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis cliquez sur **myLoadBalancer** dans la liste des ressources.
2. Cliquez sur **Paramètres**, sur **Sondes d’intégrité**, puis sur **Ajouter**.
3. Utilisez ces valeurs pour créer la sonde d’intégrité :
    - *myHealthProbe* : pour le nom de la sonde d’intégrité.
    - **HTTP** : pour le type de protocole.
    - *80* : pour le numéro de port.
    - *15* : pour **l’intervalle** en secondes entre les tentatives de la sonde.
    - *2* : pour le nombre de **seuils de défaillance** ou d’échecs de sonde consécutifs qui se produisent avant qu’une machine virtuelle soit considérée comme défaillante.
4. Cliquez sur **OK**.

   ![Ajout d'une sonde](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge

Une règle d’équilibreur de charge sert à définir la distribution du trafic vers les machines virtuelles. Vous définissez la configuration IP frontale pour le trafic entrant et le pool d’adresses IP principal pour recevoir le trafic, ainsi que le port source et le port de destination requis. Créez une règle d’équilibreur de charge *myLoadBalancerRuleWeb* pour écouter le port 80 dans le frontend *LoadBalancerFrontEnd* et envoyer le trafic réseau équilibré en charge vers le pool d’adresses principal *myBackEndPool* qui utilise également le port 80. 

1. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis cliquez sur **myLoadBalancer** dans la liste des ressources.
2. Cliquez sur **Paramètres**, **Règles d’équilibrage de charge**, puis sur **Ajouter**.
3. Utilisez ces valeurs pour configurer la règle d’équilibrage de charge :
    - *myHTTPRule* : pour que le nom de la règle d’équilibrage de charge.
    - **TCP** : pour le type de protocole.
    - *80* : pour le numéro de port.
    - *80* : pour le port principal.
    - *myBackendPool* : pour le nom du pool principal.
    - *myHealthProbe* : pour le nom de la sonde d’intégrité.
4. Cliquez sur **OK**.
    
    ![Ajout d’une règle d’équilibrage de charge](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="create-a-virtual-machine-to-test-the-load-balancer"></a>Créer une machine virtuelle pour tester l’équilibreur de charge
Pour tester l’équilibreur de charge interne, vous devez créer une machine virtuelle qui se trouve dans le même réseau virtuel que les machines virtuelles du serveur principal.
1. Sur le côté gauche de l’écran, cliquez sur **Créer une ressource** > **Calcul** > **Windows Server 2016 Datacenter** et entrez ces valeurs pour la machine virtuelle :
    - *myVMTest* : pour le nom de la machine virtuelle.        
    - *myResourceGroupILB* : dans **Groupe de ressources**, sélectionnez **Utiliser existant**, puis *myResourceGroupILB*.
2. Cliquez sur **OK**.
3. Sélectionnez **DS1_V2** pour la taille de la machine virtuelle, puis cliquez sur **Sélectionner**.
4. Entrez ces valeurs pour les paramètres de la machine virtuelle :
    -  *myVNet* : vérifiez qu’il est sélectionné en tant que réseau virtuel.
    - *myBackendSubnet* : vérifiez qu’il est sélectionné en tant que sous-réseau.
5. Cliquez sur **Désactivé** pour désactiver les diagnostics de démarrage.
6. Cliquez sur **OK**, vérifiez les paramètres sur la page de résumé, puis cliquez sur **Créer**.

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge
1. Dans le portail Azure, récupérez l’adresse IP privée de l’équilibreur de charge dans l’écran **Vue d’ensemble**. Pour ce faire : a. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis cliquez sur **myLoadBalancer** dans la liste des ressources.
    b. Copiez l’adresse IP privée (dans cet exemple, 10.1.0.7) dans la page de détails **Vue d’ensemble**.

2. Créez une connexion à distance avec *myVMTest* comme suit : a. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis, dans la liste de ressources, cliquez sur **myVMTest** qui se trouve dans le groupe de ressources *myResourceGroupILB*.
2. Dans la page **Vue d’ensemble**, cliquez sur **Connexion** pour démarrer une session à distance avec la machine virtuelle.
3. Connectez-vous à la machine virtuelle *myVMTest*.
3. Collez l’adresse IP privée dans la barre d’adresses du navigateur dans *myVMTest*. La page par défaut du serveur Web IIS s’affiche sur le navigateur.

      ![Serveur Web IIS](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png)

Pour visualiser la distribution de trafic par l’équilibreur de charge sur les deux machines virtuelles exécutant votre application, vous pouvez forcer l’actualisation de votre navigateur web.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, l’équilibreur de charge et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources qui contient l’équilibreur de charge, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé un groupe de ressources, des ressources réseau et des serveurs principaux. Ensuite, vous avez utilisé ces ressources pour créer un équilibreur de charge interne afin d’équilibrer la charge du trafic interne vers les machines virtuelles. Découvrir maintenant comment [équilibrer la charge des machines virtuelles entre des zones de disponibilité](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
