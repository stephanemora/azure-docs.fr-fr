---
title: Accéder à un serveur SQL local à partir d’un VNet managé Data Factory en utilisant un point de terminaison privé
description: Ce tutoriel explique comment utiliser le portail Azure pour configurer le service Private Link et accéder à un serveur SQL Server local à partir d’un VNet managé en utilisant un point de terminaison privé.
author: lrtoyou1223
ms.author: lle
ms.service: data-factory
ms.topic: tutorial
ms.date: 05/06/2021
ms.openlocfilehash: bb29c7712bdbe629ff3aa8704c0c4654404f0da3
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111971842"
---
# <a name="tutorial-how-to-access-on-premises-sql-server-from-data-factory-managed-vnet-using-private-endpoint"></a>Tutoriel : Guide pratique pour accéder à un serveur SQL local à partir d’un VNet managé Data Factory en utilisant un point de terminaison privé

Ce tutoriel explique comment utiliser le portail Azure pour configurer le service Private Link et accéder à un serveur SQL Server local à partir d’un VNet managé en utilisant un point de terminaison privé.

> [!NOTE]
> La solution présentée dans cet article décrit la connectivité SQL Server, mais vous pouvez utiliser une approche similaire pour connecter et interroger les autres [connecteurs locaux](connector-overview.md) disponibles pris en charge dans Azure Data Factory.

:::image type="content" source="./media/tutorial-managed-virtual-network/sql-server-access-model.png" alt-text="Capture d’écran montrant le modèle d’accès du serveur SQL" lightbox="./media/tutorial-managed-virtual-network/sql-server-access-model-expanded.png":::

## <a name="prerequisites"></a>Prérequis

* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Réseau virtuel**. Si vous n’avez pas de réseau virtuel, créez-en un en suivant la procédure [Créer un réseau virtuel](../virtual-network/quick-create-portal.md).
* **Réseau virtuel connecté à un réseau local**. Créez une connexion entre le réseau virtuel et le réseau local en utilisant [ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=/azure/virtual-network/toc.json) ou un [VPN](../vpn-gateway/tutorial-site-to-site-portal.md?toc=/azure/virtual-network/toc.json).
* **Data Factory avec VNet managé activé**. Si vous n’avez pas de fabrique de données ou que le VNet managé n’est pas activé, créez-en une en suivant la procédure [Créer une fabrique de données avec VNet managé](tutorial-copy-data-portal-private.md).

## <a name="create-subnets-for-resources"></a>Créer des sous-réseaux pour les ressources

**Utilisez le portail pour créer des sous-réseaux dans votre réseau virtuel.**

| Subnet | Description |
|:--- |:--- |
|be-subnet |Sous-réseau pour les serveurs back-end|
|fe-subnet |Sous-réseau pour l’équilibreur de charge interne standard|
|pls-subnet|Sous-réseau pour le service Private Link|

:::image type="content" source="./media/tutorial-managed-virtual-network/subnets.png" alt-text="Capture d’écran montrant les sous-réseaux." lightbox="./media/tutorial-managed-virtual-network/subnets-expanded.png":::

## <a name="create-a-standard-load-balancer"></a>Créer un équilibreur de charge standard

Utilisez le portail pour créer un équilibreur de charge interne standard.

1. Dans l’angle supérieur gauche de l’écran, cliquez sur **Créer une ressource > Mise en réseau > Équilibreur de charge**.
2. Dans l’onglet **Fonctions de base** de la page **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    |:--- |:--- |
    |Abonnement|Sélectionnez votre abonnement.|
    |Resource group|Sélectionnez votre groupe de ressources.|
    |Nom|Entrez **myLoadBalancer**.|
    |Région|Sélectionnez **USA Est**.|
    |Type|sélectionnez **Interne**.|
    |SKU|Sélectionnez **Standard**.|
    |Réseau virtuel|Sélectionnez votre réseau virtuel.|
    |Subnet|Sélectionnez **fe-subnet** créé à l’étape précédente.|
    |Affectation d’adresses IP|Sélectionnez **Dynamique**.|
    |Zone de disponibilité|Sélectionnez **Redondant dans une zone**.|

3. Acceptez les valeurs par défaut pour les paramètres restants, puis sélectionnez **Vérifier + créer**.
4. Sous l’onglet **Review + create (Vérifier + créer)** , sélectionnez **Créer**.

    :::image type="content" source="./media/tutorial-managed-virtual-network/create-load-balancer.png" alt-text="Capture d’écran montrant l’étape de création d’un équilibreur de charge standard.":::

## <a name="create-load-balancer-resources"></a>Créer les ressources d’équilibreur de charge

### <a name="create-a-backend-pool"></a>Créer un pool principal

Un pool d’adresses de back-ends contient les adresses IP des cartes d’interface réseau virtuelles connectées à l’équilibreur de charge.

Créez le pool d’adresses principal **myBackendPool** afin d’inclure des machines virtuelles pour l’équilibrage de charge du trafic Internet.

1. Sélectionnez **Tous les services** dans le menu de gauche, **Toutes les ressources**, puis **myLoadBalancer** dans la liste des ressources.
2. Sous **Paramètres**, sélectionnez **Pools principaux**, puis **Ajouter**.
3. Dans la page **Ajouter un pool de backends**, entrez **myBackendPool** comme nom de votre pool principal, puis sélectionnez **Ajouter**.

### <a name="create-a-health-probe"></a>Créer une sonde d’intégrité

L’équilibreur de charge supervise l’état de votre application avec une sonde d’intégrité.

La sonde d’intégrité ajoute ou supprime des machines virtuelles dans l’équilibreur de charge en fonction de leur réponse aux contrôles d’intégrité.

Créez une sonde d’intégrité nommée **myHealthProbe** pour surveiller l’intégrité des machines virtuelles.

1. Sélectionnez **Tous les services** dans le menu de gauche, **Toutes les ressources**, puis **myLoadBalancer** dans la liste des ressources.
2. Sous **Paramètres**, sélectionnez **Sondes d’intégrité**, puis **Ajouter**.

    | Paramètre | Valeur |
    |:--- |:--- |
    |Nom|Entrez **MyHealthProbe**.|
    |Protocol|Sélectionnez **TCP**.|
    |Port|Entrez 22.|
    |Intervalle|Entrez **15** pour **l’intervalle** en secondes entre les tentatives de la sonde.|
    |Seuil de défaillance sur le plan de l’intégrité|Sélectionnez **2** pour le **Seuil de défaillance sur le plan de l’intégrité**, soit le nombre d’échecs de sonde consécutifs qui peuvent se produire avant qu’une machine virtuelle soit considérée comme non saine.|

3. Laissez les autres valeurs par défaut et sélectionnez **OK**.

### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge

Une règle d’équilibrage de charge est utilisée pour définir la distribution du trafic vers les machines virtuelles. Vous définissez la configuration IP front-end pour le trafic entrant et le pool d’adresses IP de back-ends pour la réception du trafic. Les ports source et de destination sont définis dans la règle.

Dans cette section, vous allez créer une règle d’équilibreur de charge :

1. Sélectionnez **Tous les services** dans le menu de gauche, **Toutes les ressources**, puis **myLoadBalancer** dans la liste des ressources.
2. Sous **Paramètres**, sélectionnez **Règles d’équilibrage de charge**, puis **Ajouter**.
3. Pour configurer la règle d’équilibrage de charge, utilisez les valeurs suivantes :

    | Paramètre | Valeur |
    |:--- |:--- |
    |Nom|Entrez **myRule**.|
    |Version de l’adresse IP|Sélectionnez **IPv4**.|
    |Adresse IP du serveur frontal|Sélectionnez **LoadBalancerFrontEnd**.|
    |Protocol|Sélectionnez **TCP**.|
    |Port|Entrez **1433**.|
    |Port principal|Entrez **1433**.|
    |Pool principal|Sélectionnez **MyBackendPool**.|
    |Sonde d’intégrité|Sélectionnez **myHealthProbe**.|
    |Délai d’inactivité (minutes)|Déplacez le curseur sur **15 minutes**.|
    |Réinitialisation du protocole TCP|Sélectionnez **Désactivé**.|

4. Laissez les autres valeurs par défaut, puis sélectionnez **OK**.

## <a name="create-a-private-link-service"></a>Créer un service Private Link

Dans cette section, vous allez créer un service Private Link derrière un équilibreur de charge standard.

1. En haut à gauche de la page dans le portail Azure, sélectionnez **Créer une ressource**.
2. Recherchez **Private Link** dans la zone **Rechercher dans la Place de marché**.
3. Sélectionnez **Create** (Créer).
4. Dans **Vue d’ensemble** sous **Centre Private Link**, sélectionnez le bouton bleu **Créer un service de liaison privée**.
5. Sous l’onglet **De base** sous **Créer un service de liaison privée**, entrez ou sélectionnez les informations suivantes :

    |Paramètre |Valeur |
    |---------|--------|
    |**Détails du projet**||
    |Abonnement |Sélectionnez votre abonnement.|
    |Groupe de ressources |Sélectionnez votre groupe de ressources.|
    |**Détails de l’instance**||
    |Nom  |Entrez **myPrivateLinkService**.|
    |Région  |Sélectionnez **USA Est**.|

6. Sélectionnez l’onglet **Paramètres sortants** ou sélectionnez **Suivant : Paramètres sortants** en bas de la page.
7. Sous l’onglet **Paramètres sortants**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    |:--- |:--- |
    |Équilibrage de charge|Sélectionnez **myLoadBalancer**.|
    |Adresse IP front-end de l’équilibreur de charge|Sélectionnez **LoadBalancerFrontEnd**.|
    |Sous-réseau NAT source|Sélectionnez **pls-subnet**.|
    |Activer le proxy TCP V2|Conservez la valeur par défaut **Non**.|
    |**Paramètres d’adresse IP privée**||
    |Conservez les paramètres par défaut.||

8. Sélectionnez l’onglet **Sécurité d’accès** ou sélectionnez **Suivant : Sécurité d’accès** en bas de la page.
9. Conservez la valeur par défaut **Contrôle d’accès en fonction du rôle uniquement** sous l’onglet **Sécurité d’accès**.
10. Sélectionnez l’onglet **Étiquettes** ou sélectionnez **Suivant : Étiquettes** au bas de la page.
11. Sélectionnez l’onglet **Vérifier + créer** ou sélectionnez **Suivant : Vérifier + créer** en bas de la page.
12. Sélectionnez **Créer** sous l’onglet **Vérifier + créer**.


## <a name="create-backend-servers"></a>Créer des serveurs principaux

1. En haut à gauche du portail, sélectionnez **Créer une ressource > Calcul > Machine virtuelle**.
2. Dans **Créer une machine virtuelle**, tapez ou sélectionnez les valeurs sous l’onglet **De base** :

    |Paramètre |Valeur|
    |---------|--------|
    |**Détails du projet**||
    |Abonnement |Sélectionnez votre abonnement Azure.|
    |Groupe de ressources |Sélectionnez votre groupe de ressources.|
    |**Détails de l’instance**||
    |Nom de la machine virtuelle  |Entrez **myVM1**.|
    |Région  |Sélectionnez **USA Est**.|
    |Options de disponibilité  |Sélectionnez **Zones de disponibilité**.|
    |Zone de disponibilité  |Sélectionnez **1**.| 
    |Image  |Sélectionnez **Ubuntu Server 18.04LTS – Gen1**.| 
    |Instance Azure Spot  |Sélectionnez **Non**.| 
    |Taille   |Choisissez la taille de la machine virtuelle ou acceptez le paramètre par défaut.| 
    |**Compte administrateur**||
    |Nom d’utilisateur |Entrez un nom d’utilisateur.|
    |Source de la clé publique SSH  |Générez une nouvelle paire de clés.|
    |Nom de la paire de clés  |mySSHKey.|    
    |**Règles des ports d’entrée**||
    |Aucun port d’entrée public |Aucun|   

3. Sélectionnez l'onglet **Mise en réseau** ou choisissez **Suivant : Disques**, puis **Suivant : Mise en réseau**.
4. Sous l’onglet Réseau, sélectionnez ou entrez :

    | Paramètre |Valeur|
    |---------|--------|
    |**Interface réseau**||
    |Réseau virtuel |Sélectionnez votre réseau virtuel.|
    |Subnet |**be-subnet**.|
    |Adresse IP publique |Sélectionnez **Aucun**.|
    |Groupe de sécurité réseau de la carte réseau |Sélectionnez **Aucun**.|
    |**Équilibrage de charge**||
    |Placer cette machine virtuelle derrière une solution d’équilibrage de charge existante ?|Sélectionnez **Oui**.|
    |**Paramètres d’équilibrage de charge**||
    |Options d’équilibrage de charge |Sélectionnez **Équilibrage de charge Azure**.|
    |Sélectionnez un équilibreur de charge |Sélectionnez **myLoadBalancer**.|
    |Sélectionnez un pool principal |Sélectionnez **MyBackendPool**.|    

5. Sélectionnez **Revoir + créer**.
6. Passez en revue les paramètres, puis sélectionnez **Créer**.
7. Vous pouvez répéter les étapes 1 à 6 pour avoir plus de 1 machine virtuelle de serveur back-end à des fins de haute disponibilité.

## <a name="creating-forwarding-rule-to-endpoint"></a>Création de la règle de transfert vers le point de terminaison

1. Connectez-vous et copiez le script [ip_fwd.sh](https://github.com/sajitsasi/az-ip-fwd/blob/main/ip_fwd.sh) sur vos machines virtuelles de serveur back-end. 
2. Exécutez le script avec les options suivantes :<br/>
    **sudo ./ip_fwd.sh -i eth0 -f 1433 -a <FQDN/IP> -b 1433**<br/>
    <FQDN/IP> correspond à l’adresse IP cible du serveur SQL Server.<br/>
    
    > [!Note] 
    > Pour que le nom de domaine complet fonctionne sur le serveur SQL Server local, vous devez ajouter un enregistrement dans la zone Azure DNS.
    
3. Exécutez la commande ci-dessous et vérifiez les tables iptable dans les machines virtuelles de serveur back-end. Dans vos tables iptable, vous pouvez voir un enregistrement comportant votre adresse IP cible.<br/>
    **sudo iptables -t nat -v -L PREROUTING -n --line-number**

    :::image type="content" source="./media/tutorial-managed-virtual-network/command-record-1.png" alt-text="Capture d’écran montrant l’enregistrement de la commande.":::

    >[!Note]
    > Si vous avez plusieurs serveurs SQL Server ou sources de données, vous devez définir plusieurs règles d’équilibreur de charge et enregistrements de table IP avec différents ports. Sinon, il y aura un conflit. Par exemple,<br/>
    >
    >|                  |Port dans la règle d’équilibreur de charge|Port back-end dans la règle d’équilibreur de charge|Commande exécutée dans la machine virtuelle de serveur back-end|
    >|------------------|---------|--------|---------|
    >|**Serveur SQL Server 1**|1433 |1433 |sudo ./ip_fwd.sh -i eth0 -f 1433 -a <FQDN/IP> -b 1433|
    >|**Serveur SQL Server 2**|1434 |1434 |sudo ./ip_fwd.sh -i eth0 -f 1434 -a <FQDN/IP> -b 1433|
    
## <a name="create-a-private-endpoint-to-private-link-service"></a>Créer un point de terminaison privé au service Private Link

1. Dans le menu de gauche, sélectionnez Tous les services, Toutes les ressources, puis votre fabrique de données dans la liste des ressources.
2. Sélectionnez **Créer et surveiller** pour lancer l’interface utilisateur de Data Factory dans un onglet séparé.
3. Accédez à l’onglet **Gérer**, puis accédez à la section **Points de terminaison privés managés**.
4. Sélectionnez + **Nouveau** sous **Points de terminaison privés managés**.
5. Sélectionnez la vignette **Service de liaison privée** dans la liste, puis **Continuer**.
6. Entrez le nom du point de terminaison privé et sélectionnez **myPrivateLinkService** dans la liste des services de liaison privée.
7. Ajoutez le nom de domaine complet de votre serveur SQL Server local cible et les adresses IP NAT de votre service de liaison privée.
    
    :::image type="content" source="./media/tutorial-managed-virtual-network/link-service-nat-ip.png" alt-text="Capture d’écran montrant l’adresse IP NAT dans le service lié." lightbox="./media/tutorial-managed-virtual-network/link-service-nat-ip-expanded.png":::

    :::image type="content" source="./media/tutorial-managed-virtual-network/private-endpoint.png" alt-text="Capture d’écran montrant les paramètres du point de terminaison privé.":::

8. Créez le point de terminaison privé.

## <a name="create-a-linked-service-and-test-the-connection"></a>Créer un service lié et tester la connexion

1. Accédez à l’onglet **Gérer**, puis accédez à la section **Points de terminaison privés managés**.
2. Sélectionnez + **Nouveau** sou **Service lié**.
3. Sélectionnez la vignette **Serveur SQL** dans la liste, puis **Continuer**.    

    :::image type="content" source="./media/tutorial-managed-virtual-network/linked-service-1.png" alt-text="Capture d’écran montrant la page de création du service lié.":::   

4. Activez **Création interactive**.

    :::image type="content" source="./media/tutorial-managed-virtual-network/linked-service-2.png" alt-text="Capture d’écran montrant comment activer Création interactive.":::

5. Entrez le **nom de domaine complet** de votre serveur SQL Server local, le **nom d’utilisateur** et le **mot de passe**.
6. Cliquez ensuite sur **Tester la connexion**.

    :::image type="content" source="./media/tutorial-managed-virtual-network/linked-service-3.png" alt-text="Capture d’écran montrant la page de création du service lié du serveur SQL.":::

## <a name="troubleshooting"></a>Résolution des problèmes

Accédez à la machine virtuelle de serveur back-end, puis vérifiez avec la commande telnet que le serveur SQL Server fonctionne : **telnet **<** FQDN**>** 1433**.

## <a name="next-steps"></a>Étapes suivantes

Passez au tutoriel suivant pour découvrir comment accéder à une instance managée Microsoft Azure SQL à partir d’un VNet managé Data Factory en utilisant un point de terminaison privé :

> [!div class="nextstepaction"]
> [Accéder à une instance managée SQL à partir d’un VNet managé Data Factory](tutorial-managed-virtual-network-sql-managed-instance.md)
