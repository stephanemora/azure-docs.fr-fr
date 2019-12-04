---
title: Filtrer le trafic entrant avec le pare-feu Azure DNAT à l’aide du portail
description: Ce didacticiel vous apprend à déployer et configurer un pare-feu Azure DNAT à l’aide du portail Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 2f390f3ad540a2a25055dfcc97cc3af1f22c2b73
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195738"
---
# <a name="tutorial-filter-inbound-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Didacticiel : Filtrer le trafic entrant avec pare-feu Azure DNAT via le portail Azure

Vous pouvez configurer Azure Firewall Destination Network Address Translation (DNAT) pour traduire et filtrer le trafic entrant vers vos sous-réseaux. Lorsque vous configurez le DNAT, l’action de collection de règles NAT est définie sur **DNAT**. Chaque règle de la collection de règles NAT peut ensuite être utilisée pour traduire l’IP et le port publics de votre pare-feu en IP et port privés. Les règles DNAT ajoutent implicitement une règle de réseau correspondante pour autoriser le trafic traduit. Vous pouvez remplacer ce comportement en ajoutant explicitement une collection de règles de réseau avec des règles de refus correspondant au trafic traduit. Pour plus d’informations sur la logique de traitement des règles de Pare-feu Azure, consultez l’article [Logique de traitement des règles du service Pare-feu Azure](rule-processing.md).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer un environnement réseau de test
> * Déployer un pare-feu
> * Créer un itinéraire par défaut
> * Configurer une règle DNAT
> * Tester le pare-feu

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Pour ce didacticiel, vous créez deux réseaux virtuels appairés :

- **VN-Hub** : le pare-feu est dans ce réseau virtuel.
- **VN-Spoke** : le serveur de la charge de travail est dans ce réseau virtuel.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).
2. Dans la page d’accueil du portail Azure, cliquez sur **Groupes de ressources**, puis cliquez sur **Ajouter**.
3. Pour **Nom du groupe de ressources**, entrez **RG-DNAT-Test**.
4. Pour **Abonnement**, sélectionnez votre abonnement.
5. Pour **Emplacement du groupe de ressources**, sélectionnez un emplacement. Toutes les ressources suivantes que vous créez doivent se trouver dans le même emplacement.
6. Cliquez sur **Créer**.

## <a name="set-up-the-network-environment"></a>Configurer l’environnement réseau

Tout d’abord, créez les réseaux virtuels, puis appairez-les.

### <a name="create-the-hub-vnet"></a>Créer le réseau virtuel du hub

1. À partir de la page d’accueil du portail Azure, cliquez sur **Tous les services**.
2. Sous **Mise en réseau**, cliquez sur **Réseaux virtuels**.
3. Cliquez sur **Add**.
4. Dans le champ **Nom**, saisissez **VN-Hub**.
5. Pour **Espace d’adressage**, entrez **10.0.0.0/16**.
6. Pour **Abonnement**, sélectionnez votre abonnement.
7. Pour **Groupe de ressources**, sélectionnez **Existant** puis **RG-DNAT-Test**.
8. Pour **Emplacement**, sélectionnez le même emplacement que celui utilisé précédemment.
9. Sous **Sous-réseau**, pour **Nom**, entrez **AzureFirewallSubnet**.

     Le pare-feu se trouvera dans ce sous-réseau et le nom du sous-réseau **doit** être AzureFirewallSubnet.
     > [!NOTE]
     > La taille du sous-réseau AzureFirewallSubnet est /26. Pour plus d’informations sur la taille du sous-réseau, consultez le [FAQ Pare-feu Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

10. Pour **Plage d’adresses**, tapez **10.0.1.0/26**.
11. Utilisez les autres paramètres par défaut, puis cliquez sur **Créer**.

### <a name="create-a-spoke-vnet"></a>Créer un réseau virtuel spoke

1. À partir de la page d’accueil du portail Azure, cliquez sur **Tous les services**.
2. Sous **Mise en réseau**, cliquez sur **Réseaux virtuels**.
3. Cliquez sur **Add**.
4. Pour **Nom**, tapez **VN-Spoke**.
5. Pour **Espace d’adressage**, entrez **192.168.0.0/16**.
6. Pour **Abonnement**, sélectionnez votre abonnement.
7. Pour **Groupe de ressources**, sélectionnez **Existant** puis **RG-DNAT-Test**.
8. Pour **Emplacement**, sélectionnez le même emplacement que celui utilisé précédemment.
9. Sous **Sous-réseau**, tapez **SN-Workload** pour **Nom**.

    Le serveur se trouve dans ce sous-réseau.
10. Pour **Plage d’adresses**, entrez **192.168.1.0/24**.
11. Utilisez les autres paramètres par défaut, puis cliquez sur **Créer**.

### <a name="peer-the-vnets"></a>Homologuer les réseaux virtuels

Maintenant, appairez les deux réseaux virtuels.

#### <a name="hub-to-spoke"></a>Hub à spoke

1. Cliquez sur le réseau virtuel **VN-Hub**.
2. Sous **Paramètres**, cliquez sur **Peerings**.
3. Cliquez sur **Add**.
4. Tapez **Peer-HubSpoke** pour le nom.
5. Sélectionnez **VN-Spoke** pour le réseau virtuel.
6. Cliquez sur **OK**.

#### <a name="spoke-to-hub"></a>Spoke à hub

1. Cliquez sur le réseau virtuel **VN-Spoke**.
2. Sous **Paramètres**, cliquez sur **Peerings**.
3. Cliquez sur **Add**.
4. Tapez **Peer-SpokeHub** pour le nom.
5. Sélectionnez **VN-Hub** pour le réseau virtuel.
6. Cliquez sur **Autoriser le trafic transféré**.
7. Cliquez sur **OK**.

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Créez une machine virtuelle de charge de travail, puis placez-la dans le sous-réseau **SN-Workload**.

1. À partir de la page d’accueil du portail Azure, cliquez sur **Tous les services**.
2. Sous **Compute**, cliquez sur **Machines virtuelles**.
3. Cliquez sur **Ajouter**, puis sur **Windows Server**. Sélectionnez **Windows Server 2016 Datacenter**, puis cliquez sur **Créer**.

**Concepts de base**

1. Pour **Nom**, entrez **Srv-Workload**.
5. Entrez un nom d’utilisateur et un mot de passe.
6. Pour **Abonnement**, sélectionnez votre abonnement.
7. Pour **Groupe de ressources**, cliquez sur **Existant** puis sur **RG-DNAT-Test**.
8. Pour **Emplacement**, sélectionnez le même emplacement que celui utilisé précédemment.
9. Cliquez sur **OK**.

**Taille**

1. Choisissez une taille appropriée pour une machine virtuelle de test exécutant Windows Server. Par exemple, **B2ms** (8 Go de RAM, 16 Go de stockage).
2. Cliquez sur **Sélectionner**.

**Paramètres**

1. Sous **Réseau**, sélectionnez **VN-Spoke** pour **Réseau virtuel**.
2. Pour **Sous-réseau**, sélectionnez **SN-Workload**.
3. Cliquez sur **Adresse IP publique**, puis sur **Aucune**.
4. Pour **Sélectionner des ports entrants publics**, sélectionnez **Aucun port d’entrée public**. 
2. Laissez les autres paramètres par défaut et cliquez sur **OK**.

**Résumé**

Consultez le résumé, puis cliquez sur **Créer**. L’exécution de cette opération nécessite quelques minutes.

Une fois le déploiement terminé, notez l’adresse IP privée de la machine virtuelle. Elle servira ultérieurement pour configurer le pare-feu. Cliquez sur le nom de la machine virtuelle. Sous **Paramètres**, cliquez sur **Mise en réseau** pour trouver l’adresse IP privée.

## <a name="deploy-the-firewall"></a>Déployer le pare-feu

1. À partir de la page accueil du portail, cliquez sur **Créer une ressource**.
2. Cliquez sur **Mise en réseau**, et après **Recommandés**, cliquez sur **Afficher tout**.
3. Cliquez sur **Pare-feu**, puis sur **Créer**. 
4. Sur la page **Créer un pare-feu**, utilisez le tableau suivant pour configurer le pare-feu :

   |Paramètre  |Valeur  |
   |---------|---------|
   |Nom     |FW-DNAT-test|
   |Subscription     |\<votre abonnement\>|
   |Resource group     |**Utiliser l’existant** : RG-DNAT-Test |
   |Location     |Sélectionnez le même emplacement que celui utilisé précédemment|
   |Choisir un réseau virtuel     |**Utiliser l’existant** : VN-Hub|
   |Adresse IP publique     |**Créer un nouveau**. L’adresse IP publique doit être le type de référence (SKU) Standard.|

5. Cliquez sur **Revoir + créer**.
6. Passez en revue le résumé, puis cliquez sur **Créer** pour créer le pare-feu.

   Le déploiement nécessite quelques minutes.
7. Une fois le déploiement terminé, accédez au groupe de ressources **RG-DNAT-Test**, puis cliquez sur le pare-feu **FW-DNAT-test**.
8. Notez l’adresse IP privée. Vous l’utiliserez plus tard lors de la création de l’itinéraire par défaut.

## <a name="create-a-default-route"></a>Créer un itinéraire par défaut

Pour le sous-réseau **SN-Workload**, vous devez configurer l’itinéraire sortant par défaut pour qu’il traverse le pare-feu.

1. À partir de la page d’accueil du portail Azure, cliquez sur **Tous les services**.
2. Sous **Mise en réseau**, cliquez sur **Tables de routage**.
3. Cliquez sur **Add**.
4. Dans le champ **Nom**, saisissez **RT-FWroute**.
5. Pour **Abonnement**, sélectionnez votre abonnement.
6. Pour **Groupe de ressources**, sélectionnez **Existant** puis **RG-DNAT-Test**.
7. Pour **Emplacement**, sélectionnez le même emplacement que celui utilisé précédemment.
8. Cliquez sur **Créer**.
9. Cliquez sur **Actualiser**, puis sur la table de routage **RT-FWroute**.
10. Cliquez sur **Sous-réseaux**, puis sur **Associer**.
11. Cliquez sur **Réseau virtuel**, puis sélectionnez **VN-Spoke**.
12. Pour **Sous-réseau**, cliquez sur **SN-Workload**.
13. Cliquez sur **OK**.
14. Cliquez sur **Itinéraires**, puis sur **Ajouter**.
15. Pour **Nom de l’itinéraire**, entrez **FW-DG**.
16. Pour **Préfixe d’adresse**, entrez **0.0.0.0/0**.
17. Pour **Type de tronçon suivant**, sélectionnez **Appliance virtuelle**.

    Le Pare-feu Azure est en réalité un service managé, mais l’appliance virtuelle fonctionne dans ce cas.
18. Pour **Adresse de tronçon suivant**, entrez l’adresse IP privée pour le pare-feu que vous avez notée précédemment.
19. Cliquez sur **OK**.

## <a name="configure-a-nat-rule"></a>Configurer une règle NAT

1. Ouvrez **RG-DNAT-Test**, puis cliquez sur le pare-feu **FW-DNAT-test**. 
2. Sur la page **FW-DNAT-test**, sous **Paramètres**, cliquez sur **Règles**. 
3. Cliquez sur **Ajouter une collection de règles NAT**. 
4. Dans le champ **Nom**, saisissez **RC-DNAT-01**. 
5. Pour **Priorité**, entrez **200**. 
6. Sous **Règles**, pour **Nom**, entrez **RL-01**.
7. Pour **Protocole**, sélectionnez **TCP**.
8. Pour **Adresses sources**, entrez *. 
9. Dans le champ **Adresses de destination**, tapez l’adresse IP publique du pare-feu. 
10. Pour **Ports de destination**, entrez **3389**. 
11. Dans le champ **Adresse traduite**, saisissez l’adresse IP privée de la machine virtuelle Srv-Workload. 
12. Dans le champ **Port traduit**, tapez **3389**. 
13. Cliquez sur **Add**. 

## <a name="test-the-firewall"></a>Tester le pare-feu

1. Connectez un bureau distant à l’adresse IP publique du pare-feu. Vous devriez être connecté à la machine virtuelle **Srv-Workload**.
2. Fermez le bureau à distance.

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous pouvez garder vos ressources de pare-feu pour le prochain didacticiel, ou, si vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources **RG-DNAT-Test** pour supprimer toutes les ressources associées au pare-feu.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Configurer un environnement réseau de test
> * Déployer un pare-feu
> * Créer un itinéraire par défaut
> * Configurer une règle DNAT
> * Tester le pare-feu

Ensuite, vous pouvez surveiller les journaux d’activité de Pare-feu Azure.

> [!div class="nextstepaction"]
> [Didacticiel : Superviser les journaux d’activité de Pare-feu Azure](./tutorial-diagnostics.md)
