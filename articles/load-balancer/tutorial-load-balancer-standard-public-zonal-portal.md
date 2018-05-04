---
title: Équilibrer la charge de machines virtuelles à l’intérieur d’une zone - Portail Azure | Microsoft Docs
description: Créer un équilibreur de charge standard avec frontend zonal pour équilibrer la charge de machines virtuelles dans une zone de disponibilité à l’aide du portail Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/18/2018
ms.author: kumud
ms.openlocfilehash: 41a33436cb0d2c4c2bbfef4888bb704c62e2b91e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
---
# <a name="load-balance-vms-within-an-availability-zone-with-a-standard-load-balancer-using-the-azure-portal"></a>Équilibrer la charge de machines virtuelles dans une zone de disponibilité avec un équilibreur de charge standard à l’aide du portail Azure

Cet article décrit les étapes de création d’un [équilibreur de charge standard](https://aka.ms/azureloadbalancerstandard) public avec un frontend zonal utilisant une adresse IP publique standard dans le portail Azure. Dans ce scénario, vous pouvez spécifier une zone particulière pour vos instances de serveur frontal et de serveur principal, afin d’aligner votre chemin de données et vos ressources avec une zone spécifique.
Pour plus d’informations sur l’utilisation des zones de disponibilité avec un équilibreur de charge standard, voir [Équilibreur de charge standard et zones de disponibilité](load-balancer-standard-availability-zones.md).

Si vous préférez, vous pouvez suivre ce didacticiel en utilisant [Azure CLI](load-balancer-standard-public-zonal-cli.md).

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure à l’adresse [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-standard-load-balancer"></a>Créer un équilibreur de charge standard public

L’équilibreur de charge standard prend uniquement en charge une adresse IP publique standard. Lorsque vous créez une nouvelle adresse IP publique en créant l’équilibreur de charge, celle-ci est automatiquement configurée comme version de référence standard et est également automatiquement redondante dans une zone.

1. En haut à gauche de l’écran, cliquez sur **Créer une ressource** > **Mise en réseau** > **Équilibreur de charge**.
2. Dans la page **Créer un équilibreur de charge**, entrez les valeurs suivantes pour l’équilibreur de charge :
    - *myLoadBalancer* : pour le nom de l’équilibreur de charge.
    - **Public** : pour le type de l’équilibreur de charge.
     - *myPublicIPZonal* : pour la nouvelle adresse IP publique créée. Pour ce faire, cliquez sur **Choisir une adresse IP publique,** puis sur **Créer**. Pour le nom, tapez *myPublicIP*, conservez la référence SKU standard par défaut et sélectionnez **Zone 1** dans **Zone de disponibilité**.
    - *myResourceGroupLB* : pour le nom du nouveau groupe de ressources que vous créez.
    - **westeurope** : pour l’emplacement.
3. Cliquez sur **Créer** pour générer l’équilibreur de charge.
   
    ![Créer un équilibreur de charge standard zonal à l’aide du portail Azure](./media/tutorial-load-balancer-standard-zonal-portal/create-load-balancer-zonal-frontend.png)


## <a name="create-backend-servers"></a>Créer des serveurs principaux

Dans cette section, vous créez un réseau virtuel ainsi que deux machines virtuelles dans la même zone (en l’occurrence la zone 1) de la région où ajouter le pool principal de votre équilibreur de charge, puis vous installez IIS sur les machines virtuelles afin de tester l’équilibreur de charge à redondance de zone. Donc, si une machine virtuelle tombe en panne, la sonde d’intégrité de la machine virtuelle dans la même zone devient inopérationnelle, et le trafic continue d’être acheminé par l’autre machine virtuelle située dans la même zone.

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel
1. Sur le côté gauche de l’écran, cliquez sur **Créer une ressource** > **Réseau** > **Réseau virtuel** et entrez ces valeurs pour le réseau virtuel :
    - *myVNet* : pour le nom du réseau virtuel.
    - *myResourceGroupZLB* : pour le nom du groupe de ressources existant
    - *myBackendSubnet* : pour le nom du sous-réseau.
2. Cliquez sur **Créer** pour créer le réseau virtuel.

    ![Créez un réseau virtuel](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

1. Sur le côté gauche de l’écran, cliquez sur **Créer une ressource**, dans la zone de recherche tapez *Groupe de sécurité réseau* et dans la page du groupe de sécurité réseau, cliquez sur **Créer**.
2. Dans la page Création d’un groupe de sécurité réseau, entrez ces valeurs :
    - *myNetworkSecurityGroup* : pour le nom du groupe de sécurité réseau.
    - *myResourceGroupLBAZ* : pour le nom du groupe de ressources existant.
   
    ![Créez un réseau virtuel](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>Créer les règles du groupe de sécurité réseau

Dans cette section, vous créez des règles du groupe de sécurité réseau pour autoriser les connexions entrantes à l’aide de HTTP et RDP en utilisant le portail Azure.

1. Dans le portail Azure, cliquez sur **Toutes les ressources** dans le menu de gauche, puis recherchez et cliquez sur **myNetworkSecurityGroup** dans le groupe de ressources **myResourceGroupZLB**.
2. Sous **Paramètres**, cliquez sur **Règles de sécurité entrantes**, puis sur **Ajouter**.
3. Entrez ces valeurs pour la règle de sécurité entrante nommée *myHTTPRule* afin d’autoriser les connexions HTTP entrantes à l’aide du port 80 :
    - *Service Tag* : pour **Source**.
    - *Internet* : pour **Balise de service source**
    - *80* : pour **Plages de port de destination**
    - *TCP* : pour **Protocole**
    - *Allow* : pour **Action**
    - *100* pour **Priorité**
    - *myHTTPRule* pour **Nom**
    - *Autoriser HTTP (Allow HTTP)* : dans **Description**
4. Cliquez sur **OK**.
 
 ![Créez un réseau virtuel](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)

5. Répétez les étapes 2 à 4 pour créer une autre règle nommée *myRDPRule* pour autoriser une connexion RDP entrante à l’aide du port 3389 avec les valeurs suivantes :
    - *Service Tag* : pour **Source**.
    - *Internet* : pour **Balise de service source**
    - *3389* : pour **Plages de port de destination**
    - *TCP* : pour **Protocole**
    - *Allow* : pour **Action**
    - *200* pour **Priorité**
    - *myRDPRule* pour le nom
    - *Allow RDP* pour la description

    ![Créer la règle RDP](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

1. Sur le côté gauche de l’écran, cliquez sur **Créer une ressource** > **Calcul** > **Windows Server 2016 Datacenter** et entrez ces valeurs pour la machine virtuelle :
    - *myVM1* : pour le nom de la machine virtuelle.        
    - *azureuser* : pour le nom d’utilisateur administrateur.    
    - *myResourceGroupZLB* : dans **Groupe de ressources**, sélectionnez **Utiliser existant**, puis *myResourceGroupZLB*.
2. Cliquez sur **OK**.
3. Sélectionnez **DS1_V2** pour la taille de la machine virtuelle, puis cliquez sur **Sélectionner**.
4. Entrez ces valeurs pour les paramètres de la machine virtuelle :
    - *zone 1* : pour la zone de disponibilité dans laquelle vous placez la machine virtuelle.
    -  *myVNet* : vérifiez qu’il est sélectionné en tant que réseau virtuel.
    - *myVM1PIP* : pour l’adresse IP publique standard que vous créez. Cliquez sur *Créer*, puis comme nom, tapez *myVM1PIP* et comme zone, sélectionnez **1**. Par défaut, la référence SKU de l’adresse IP est Standard.
    - *myBackendSubnet* : vérifiez qu’il est sélectionné en tant que sous-réseau.
    - *myNetworkSecurityGroup* : pour le nom du nouveau groupe de sécurité réseau (pare-feu) qui existe déjà.
5. Cliquez sur **Désactivé** pour désactiver les diagnostics de démarrage.
6. Cliquez sur **OK**, vérifiez les paramètres sur la page de résumé, puis cliquez sur **Créer**.
7. Créez une deuxième machine virtuelle nommée *myVM2* dans Zone 1 avec *myVnet* comme réseau virtuel, *myVM2PIP* comme adresse IP publique standard, *myBackendSubnet* comme sous-réseau et **myNetworkSecurityGroup* comme groupe de sécurité réseau, à l’aide des étapes 1 à 6.

    ![Créer la règle RDP](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>Installer IIS sur des machines virtuelles

1. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis dans la liste de ressources, cliquez sur **myVM1** qui se trouve dans le groupe de ressources *myResourceGroupZLB*.
2. Sur la page **Vue d’ensemble**, cliquez sur **Connexion** à RDP dans la machine virtuelle.
3. Connectez-vous à la machine virtuelle avec le nom d’utilisateur et le mot de passe que vous avez spécifiés lors de la création de celle-ci (il se peut que vous deviez choisir **Plus de choix**, puis **Utiliser un compte différent** pour spécifier les informations d’identification que vous avez entrées lors de la création de la machine virtuelle), puis sélectionnez **OK**. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Sélectionnez**Oui** pour poursuivre le processus de connexion.
4. Sur le bureau du serveur, accédez à **Outils d’administration Windows**>**Windows PowerShell**.
6. Dans la fenêtre PowerShell, exécutez les commandes suivantes pour installer le serveur IIS, supprimez le fichier default.htm, ajoutez un nouveau fichier default.htm qui affiche le nom de la machine virtuelle :

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
   ```
8. Fermez la session RDP avec *myVM1*.
9. Répétez les étapes 1 à 8 pour installer IIS sur *myVM2*.

## <a name="create-load-balancer-resources"></a>Créer les ressources d’équilibreur de charge

Dans cette section, vous configurez les paramètres de l’équilibreur de charge pour un pool d’adresses principal et une sonde d’intégrité et spécifiez l’équilibreur de charge et les règles NAT.


### <a name="create-a-backend-address-pool"></a>Créer un pool d’adresses principal

Pour distribuer le trafic vers les machines virtuelles, un pool d’adresses principal contient les adresses IP des cartes d’interface réseau virtuelles connectées à l’équilibreur de charge. Créez le pool d’adresses principal *myBackendPool* pour inclure *VM1* et *VM2*.

1. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis cliquez sur **myLoadBalancer* dans la liste des ressources.
2. Cliquez sur **Paramètres**, sur **Pools principaux**, puis sur **Ajouter**.
3. Sur la page **Ajouter un pool principal**, procédez comme suit :
    - Tapez *myBackEndPool* comme nom du pool principal.
    - Pour **Réseau virtuel**, dans le menu déroulant, cliquez sur *myVNet*
    - Dans **Machine virtuelle** et **Adresse IP**, ajoutez *myVM1* et *myVM2* ainsi que leurs adresses IP publiques.
4. Cliquez sur **Add**.
5. Vérifiez que le paramétrage du pool principal de l’équilibreur de charge affiche les deux machines virtuelles **myVM1** et **myVM2**.
 
    ![Créer le pool principal](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

### <a name="create-a-health-probe"></a>Créer une sonde d’intégrité

Pour permettre à l’équilibrage de charge de surveiller l’état de votre application, vous utilisez une sonde d’intégrité. La sonde d’intégrité ajoute ou supprime dynamiquement des machines virtuelles de la rotation d’équilibrage de charge en fonction de leur réponse aux vérifications d’intégrité. Créez une sonde d’intégrité *myHealthProbe* pour surveiller l’intégrité des machines virtuelles.

1. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis cliquez sur **myLoadBalancer** dans la liste des ressources.
2. Cliquez sur **Paramètres**, sur **Sondes d’intégrité**, puis sur **Ajouter**.
3. Utilisez ces valeurs pour créer la sonde d’intégrité :
    - *myHealthProbe* : pour le nom de la sonde d’intégrité.
    - **HTTP** : pour le type de protocole.
    - *80* : pour le numéro de port.
    - *15* : pour **l’intervalle** en secondes entre les tentatives de la sonde.
    - *2* : pour le nombre de **seuils de défaillance** ou d’échecs de sonde consécutifs qui se produisent avant qu’une machine virtuelle soit considérée comme défaillante.
4. Cliquez sur **OK**.

   ![Ajout d'une sonde](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge

Une règle d’équilibrage de charge est utilisée pour définir la distribution du trafic vers les machines virtuelles. Vous définissez la configuration IP frontale pour le trafic entrant et le pool d’adresses IP principal pour recevoir le trafic, ainsi que le port source et le port de destination requis. Créez une règle d’équilibreur de charge *myLoadBalancerRuleWeb* pour écouter le port 80 dans le frontal *FrontendLoadBalancer* et envoyer le trafic réseau équilibré en charge vers le pool d’adresses principal *myBackEndPool* qui utilisé également le port 80. 

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
    
    ![Ajout d’une règle d’équilibrage de charge](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge
1. Recherchez l’adresse IP publique de l’équilibreur de charge sur l’écran **Vue d’ensemble**. Cliquez sur **Toutes les ressources**, puis sur **myPublicIP**. 

2. Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur. La page par défaut qui inclut le nom de la page du serveur Web s’affiche dans le navigateur.

      ![Serveur Web IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. Pour voir l’équilibreur de charge en action, vous pouvez forcer l’arrêt de la machine virtuelle affichée et actualiser le navigateur pour voir le nom de l’autre serveur dans le navigateur.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, l’équilibreur de charge et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources qui contient l’équilibreur de charge, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’[équilibreur de charge standard](load-balancer-standard-overview.md).
- [Équilibrer la charge de machines virtuelles entre des zones de disponibilité](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
