---
title: 'Démarrage rapide : créer un équilibreur de charge de base public à l’aide du portail Azure | Microsoft Docs'
description: Ce guide de démarrage rapide montre comment créer un équilibreur de charge de base public à l’aide du portail Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 7451d6ade7f8b042a68f456e604e2919cacab0a5
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378429"
---
# <a name="quickstart-create-a-public-basic-load-balancer-by-using-the-azure-portal"></a>Démarrage rapide : créer un équilibreur de charge de base public à l’aide du portail Azure

L’équilibrage de charge offre un niveau plus élevé de disponibilité et d’évolutivité en répartissant les requêtes entrantes sur plusieurs machines virtuelles. Vous pouvez utiliser le portail Azure pour créer un équilibreur de charge qui équilibre la charge des machines virtuelles. Ce guide de démarrage rapide vous montre comment créer des ressources réseau, des serveurs principaux et un équilibreur de charge au niveau tarifaire De Base.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Pour toutes les tâches de ce guide de démarrage rapide, connectez-vous au [portail Azure](http://portal.azure.com).

## <a name="create-a-basic-load-balancer"></a>Créer un équilibreur de charge de base

Dans cette section, vous créez un équilibreur de charge de base public à l’aide du portail. L’adresse IP publique est automatiquement configurée comme serveur frontal de l’équilibreur de charge lorsque vous créez l’adresse IP publique et la ressource de l’équilibreur de charge à l’aide du portail. Le nom du serveur frontal est **myLoadBalancer**.

1. En haut à gauche du portail, sélectionnez **Créer une ressource** > **Mise en réseau** > **Équilibreur de charge**.
2. Dans le volet **Create load balancer** (Créer un équilibreur de charge), entrez les valeurs suivantes :
   - **myLoadBalancer** pour le nom de l’équilibreur de charge
   - **Public** pour le type de l’équilibreur de charge 
   - **myPublicIP** pour l’adresse IP publique que vous devez créer, avec la référence **SKU** définie sur **De Base** et l’**Affectation** sur **Dynamique**
   - **myResourceGroupLB** pour le nom du nouveau groupe de ressources
3. Sélectionnez **Créer**.
   
![Créer un équilibrage de charge](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-back-end-servers"></a>Créer des serveurs principaux

Dans cette section, vous créez un réseau virtuel, et vous créez deux machines virtuelles pour le pool principal de votre équilibreur de charge de base. Vous installez ensuite Internet Information Services (IIS) sur les machines virtuelles pour faciliter le test de l’équilibreur de charge.

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel
1. En haut à gauche du portail, sélectionnez **Nouveau** > **Mise en réseau** > **Réseau virtuel**.
2. Dans le volet **Créer un réseau virtuel**, entrez les valeurs suivantes, puis sélectionnez **Créer** :
   - **myVNet** pour le nom du réseau virtuel
   - **myResourceGroupLB** pour le nom du groupe de ressources existant
   - **myBackendSubnet** pour le nom du sous-réseau

   ![Créez un réseau virtuel](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

1. En haut à gauche du portail, sélectionnez **Nouveau** > **Compute** > **Windows Server 2016 Datacenter**. 
2. Entrez les valeurs suivantes pour la machine virtuelle, puis sélectionnez **OK** :
   - **myVM1** pour le nom de la machine virtuelle.        
   - **azureuser** pour le nom d’utilisateur administrateur.    
   - **myResourceGroupLB** pour le groupe de ressources. (Sous **Groupe de ressources**, sélectionnez **Utiliser existant**, puis **myResourceGroupLB**.)   
3. Sélectionnez **DS1_V2** pour la taille de la machine virtuelle, puis cliquez sur **Sélectionner**.
4. Entrez ces valeurs pour les paramètres de la machine virtuelle :
   - **myAvailabilitySet** pour le nom du nouveau groupe à haute disponibilité que vous créez.
   - **myVNet** pour le nom du réseau virtuel. (Assurez-vous qu’il est sélectionné.)
   - **myBackendSubnet** pour le nom du sous-réseau. (Assurez-vous qu’il est sélectionné.)
   - **myVM1-ip** pour l’adresse IP publique.
   - **myNetworkSecurityGroup** pour le nom du nouveau groupe de sécurité réseau (NSG de type pare-feu) que vous devez créer.
5. Sélectionnez **Désactivé** pour désactiver les diagnostics de démarrage.
6. Sélectionnez **OK**, vérifiez les paramètres sur la page de résumé, puis sélectionnez **Créer**.
7. En suivant les étapes 1 à 6, créez une deuxième machine virtuelle nommée **VM2** avec :
   - **myAvailabilityset** comme groupe à haute disponibilité.
   - **myVnet** comme réseau virtuel.
   - **myBackendSubnet** comme sous-réseau.
   - **myNetworkSecurityGroup** comme groupe de sécurité réseau. 

### <a name="create-nsg-rules"></a>Créer les règles du groupe de sécurité réseau

Dans cette section, vous créez des règles NSG pour autoriser les connexions entrantes utilisant HTTP et RDP.

1. Sélectionnez **Toutes les ressources** dans le menu de gauche. Dans la liste des ressources, sélectionnez **myNetworkSecurityGroup** dans le groupe de ressources **myResourceGroupLB**.
2. Sous **Paramètres**, sélectionnez **Règles de sécurité de trafic entrant**, puis sélectionnez **Ajouter**.
3. Entrez les valeurs suivantes pour la règle de sécurité de trafic entrant nommée **myHTTPRule** afin d’autoriser les connexions HTTP entrantes qui utilisent le port 80. Sélectionnez ensuite **OK**.
   - **Service Tag** (Identification) pour **Source**
   - **Internet** pour **Source service tag** (Identification de source)
   - **80** pour **Destination port ranges** (Plages de ports de destination)
   - **TCP** pour **Protocole**
   - **Autoriser** pour **Action**
   - **100** pour **Priorité**
   - **myHTTPRule** pour **Nom**
   - **Autoriser HTTP** pour **Description**
 
   ![Créer une règle NSG](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
4. Répétez les étapes 2 et 3 pour créer une autre règle nommée **myRDPRule** pour autoriser une connexion RDP entrante via le port 3389. Utilisez les valeurs suivantes :
   - **Service Tag** (Identification) pour **Source**
   - **Internet** pour **Source service tag** (Identification de source)
   - **3389** pour **Destination port ranges** (Plages de ports de destination)
   - **TCP** pour **Protocole**
   - **Autoriser** pour **Action**
   - **200** pour **Priorité**
   - **myRDPRule** pour **Nom**
   - **Autoriser RDP** pour **Description**

   

### <a name="install-iis"></a>Installer IIS

1. Sélectionnez **Toutes les ressources** dans le menu de gauche. Dans la liste des ressources, sélectionnez **myVM1** dans le groupe de ressources **myResourceGroupLB**.
2. Sur la page **Vue d’ensemble**, sélectionnez **Connexion** à RDP dans la machine virtuelle.
3. Connectez-vous à la machine virtuelle avec le nom d’utilisateur **azureuser** et le mot de passe **Azure123456!**.
4. Sur le bureau du serveur, accédez à **Outils d’administration Windows** > **Gestionnaire de serveur**.
5. Dans le Gestionnaire de serveur, sélectionnez **Gérer**, puis **Ajouter des rôles et fonctionnalités**.
   ![Ajout du rôle du gestionnaire de serveur](./media/load-balancer-get-started-internet-portal/servermanager.png)
6. Dans l’Assistant Ajouter des rôles et fonctionnalités, utilisez les valeurs suivantes :
   - Sur la page **Sélectionner le type d’installation**, sélectionnez **Installation basée sur un rôle ou une fonctionnalité**.
   - Sur la page **Sélectionner le serveur de destination**, sélectionnez **myVM1**.
   - Sur la page **Sélectionner le rôle du serveur**, sélectionnez **Serveur Web (IIS)**.
   - Suivez les instructions jusqu’à la fin de l’Assistant. 
7. Répétez les étapes 1 à 6 pour la machine virtuelle **myVM2**.

## <a name="create-resources-for-the-basic-load-balancer"></a>Créer des ressources pour l’équilibreur de charge de base

Dans cette section, vous configurez les paramètres de l’équilibreur de charge pour un pool d’adresses principales et une sonde d’intégrité. Vous spécifiez également l’équilibreur de charge et les règles NAT.


### <a name="create-a-back-end-address-pool"></a>Créer un pool d’adresses principal

Pour distribuer le trafic vers les machines virtuelles, un pool d’adresses principales contient les adresses IP des cartes d’interface réseau virtuelles connectées à l’équilibreur de charge. Créez le pool d’adresses principales **myBackendPool** pour inclure **VM1** et **VM2**.

1. Sélectionnez **Toutes les ressources** dans le menu de gauche, puis sélectionnez **myLoadBalancer** dans la liste des ressources.
2. Sous **Paramètres**, sélectionnez **Pools principaux**, puis **Ajouter**.
3. Sur la page **Ajouter un pool principal**, procédez comme suit, puis sélectionnez **OK** :
   - Pour **Nom**, entrez **myBackEndPool**.
   - Pour **Associé à**, dans le menu déroulant, sélectionnez **Groupe à haute disponibilité**.
   - Dans **Groupe à haute disponibilité**, sélectionnez **myAvailabilitySet**.
   - Sélectionnez **Ajouter une configuration IP de réseau cible** pour ajouter chaque machine virtuelle (**myVM1** et **myVM2**) créée au pool principal.

   ![Ajout au pool d’adresses principales](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. Assurez-vous que le paramètre du pool principal de votre équilibreur de charge affiche les machines virtuelles **VM1** et **VM2**.

### <a name="create-a-health-probe"></a>Créer une sonde d’intégrité

Pour permettre à l’équilibreur de charge de surveiller l’état de votre application, vous utilisez une sonde d’intégrité. La sonde d’intégrité ajoute ou supprime dynamiquement des machines virtuelles de la rotation d’équilibrage de charge en fonction de leur réponse aux vérifications d’intégrité. Créez une sonde d’intégrité nommée **myHealthProbe** pour surveiller l’intégrité des machines virtuelles.

1. Sélectionnez **Toutes les ressources** dans le menu de gauche, puis sélectionnez **myLoadBalancer** dans la liste des ressources.
2. Sous **Paramètres**, sélectionnez **Health probes** (Sondes d’intégrité), puis **Ajouter**.
3. Utilisez les valeurs suivantes, puis sélectionnez **OK** :
   - **myHealthProbe** pour le nom de la sonde d’intégrité
   - **HTTP** pour le type de protocole
   - **80** pour le numéro de port
   - **15** pour **Intervalle**, le nombre de secondes entre les tentatives de la sonde
   - **2** pour **Unhealthy threshold** (Seuil de défaillance), le nombre d’échecs de sonde consécutifs qui se produisent avant qu’une machine virtuelle soit considérée comme défaillante

   ![Ajout d’une sonde](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge

Vous utilisez une règle d’équilibreur de charge pour définir la distribution du trafic vers les machines virtuelles. Vous définissez la configuration IP frontale pour le trafic entrant et le pool d’adresses IP principal pour recevoir le trafic, ainsi que le port source et le port de destination requis. 

Créez une règle d’équilibreur de charge nommée **myLoadBalancerRuleWeb** pour l’écoute sur le port 80 dans le serveur frontal **LoadBalancerFrontEnd**. La règle s’applique également à l’envoi du trafic réseau équilibré en charge au pool d’adresses principales **myBackEndPool**, également à l’aide du port 80. 

1. Sélectionnez **Toutes les ressources** dans le menu de gauche, puis sélectionnez **myLoadBalancer** dans la liste des ressources.
2. Sous **Paramètres**, sélectionnez **Règles d'équilibrage de charge**, puis **Ajouter**.
3. Utilisez les valeurs suivantes, puis sélectionnez **OK** :
   - **myHTTPRule** pour que le nom de la règle d’équilibrage de charge
   - **TCP** pour le type de protocole
   - **80** pour le numéro de port
   - **80** pour le port principal
   - **myBackendPool** pour le nom du pool principal
   - **myHealthProbe** pour le nom de la sonde d’intégrité
    
   ![Ajout d’une règle d’équilibreur de charge](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge
1. Recherchez l’adresse IP publique de l’équilibreur de charge sur l’écran **Vue d’ensemble**. Sélectionnez **Toutes les ressources**, puis **myPublicIP**.

2. Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur. La page par défaut du serveur web IIS s’affiche dans le navigateur.

   ![Serveur web IIS](./media/load-balancer-get-started-internet-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Supprimer les ressources

Vous pouvez supprimer le groupe de ressources, l’équilibreur de charge et toutes les ressources associées lorsque vous n’en avez plus besoin. Sélectionnez le groupe de ressources qui contient l’équilibreur de charge, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un groupe de ressources, des ressources réseau et des serveurs principaux. Vous avez ensuite utilisé ces ressources pour créer un équilibreur de charge de base. Pour en savoir plus sur Azure Load Balancer, consultez les didacticiels consacrés à Azure Load Balancer.

> [!div class="nextstepaction"]
> [Didacticiels Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
