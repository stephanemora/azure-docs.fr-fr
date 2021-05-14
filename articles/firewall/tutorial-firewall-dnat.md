---
title: Filtrer le trafic Internet entrant avec le Pare-feu Azure DNAT à l’aide du portail
description: Dans cet article, vous découvrez comment déployer et configurer un pare-feu Azure DNAT à l’aide du portail Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/29/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: f31cffc6996ffe5b733ba322584cb400afcc5093
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108291853"
---
# <a name="filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Filtrer le trafic Internet entrant avec le Pare-feu Azure DNAT à l’aide du Portail Azure

Vous pouvez configurer le Pare-feu DNAT (Azure Destination Network Address Translation) pour traduire et filtrer le trafic Internet entrant vers vos sous-réseaux. Lorsque vous configurez le DNAT, l’action de collection de règles NAT est définie sur **DNAT**. Chaque règle de la collection de règles NAT peut ensuite être utilisée pour traduire l’adresse IP et le port publics de votre pare-feu en adresse IP et port privés. Les règles DNAT ajoutent implicitement une règle de réseau correspondante pour autoriser le trafic traduit. Par souci de sécurité, l’approche recommandée consiste à ajouter une source Internet spécifique pour autoriser l’accès de DNAT au réseau et éviter d’utiliser des caractères génériques. Pour plus d’informations sur la logique de traitement des règles de Pare-feu Azure, consultez l’article [Logique de traitement des règles du service Pare-feu Azure](rule-processing.md).

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Configurer un environnement réseau de test
> * Déployer un pare-feu
> * Créer un itinéraire par défaut
> * Configurer une règle DNAT
> * Tester le pare-feu

> [!NOTE]
> Cet article utilise des règles de pare-feu classiques pour gérer le pare-feu. La méthode recommandée consiste à utiliser une [stratégie de pare-feu](../firewall-manager/policy-overview.md). Pour effectuer cette procédure à l’aide d’une stratégie de pare-feu, consultez [Tutoriel : Filtrer le trafic Internet entrant avec une stratégie DNAT de Pare-feu Azure à l’aide du portail Azure](tutorial-firewall-dnat-policy.md).

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.



## <a name="create-a-resource-group"></a>Créer un groupe de ressources

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).
2. Dans la page d’accueil du portail Azure, sélectionnez **Groupes de ressources**, puis sélectionnez **Ajouter**.
4. Pour **Abonnement**, sélectionnez votre abonnement.
1. Pour **Nom du groupe de ressources**, entrez **RG-DNAT-Test**.
5. Pour **Région**, sélectionnez une région. Toutes les autres ressources que vous créez doivent se trouver dans la même région.
6. Sélectionnez **Revoir + créer**.
1. Sélectionnez **Create** (Créer).

## <a name="set-up-the-network-environment"></a>Configurer l’environnement réseau

Pour cet article, vous créez deux réseaux virtuels appairés :

- **VN-Hub** : le pare-feu est dans ce réseau virtuel.
- **VN-Spoke** : le serveur de la charge de travail est dans ce réseau virtuel.

Tout d’abord, créez les réseaux virtuels, puis appairez-les.

### <a name="create-the-hub-vnet"></a>Créer le réseau virtuel du hub

1. À partir de la page d’accueil du portail Azure, sélectionnez **Tous les services**.
2. Sous **Mise en réseau**, sélectionnez **Réseaux virtuels**.
3. Sélectionnez **Ajouter**.
7. Pour **Groupe de ressources**, sélectionnez **RG-DNAT-Test**.
1. Dans le champ **Nom**, saisissez **VN-Hub**.
1. Pour **Région**, sélectionnez la région que vous avez utilisée précédemment.
1. Sélectionnez **Suivant : Adresses IP**.
1. Pour **Espace d’adressage IPv4**, acceptez la valeur par défaut **10.0.0.0/16**.
1. Sous **Nom du sous-réseau**, sélectionnez Par défaut.
1. Modifiez le **Nom du sous-réseau**, puis tapez **AzureFirewallSubnet**.

     Le pare-feu se trouvera dans ce sous-réseau et le nom du sous-réseau **doit** être AzureFirewallSubnet.
     > [!NOTE]
     > La taille du sous-réseau AzureFirewallSubnet est /26. Pour plus d’informations sur la taille du sous-réseau, consultez le [FAQ Pare-feu Azure](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

10. Pour **Espace d’adressage de sous-réseau**, tapez **10.0.1.0/26**.
11. Sélectionnez **Enregistrer**.
1. Sélectionnez **Revoir + créer**.
1. Sélectionnez **Create** (Créer).

### <a name="create-a-spoke-vnet"></a>Créer un réseau virtuel spoke

1. À partir de la page d’accueil du portail Azure, sélectionnez **Tous les services**.
2. Sous **Mise en réseau**, sélectionnez **Réseaux virtuels**.
3. Sélectionnez **Ajouter**.
1. Pour **Groupe de ressources**, sélectionnez **RG-DNAT-Test**.
1. Pour **Nom**, tapez **VN-Spoke**.
1. Pour **Région**, sélectionnez la région que vous avez utilisée précédemment.
1. Sélectionnez **Suivant : Adresses IP**.
1. Pour **Espace d’adressage IPv4**, supprimez la valeur par défaut et entrez **192.168.0.0/16**.
1. Sélectionnez **Ajouter un sous-réseau**.
1. Pour **Nom du sous-réseau**, tapez **SN-Workload**.
10. Pour **Plage d’adresses de sous-réseau**, tapez **192.168.1.0/24**.
11. Sélectionnez **Ajouter**.
1. Sélectionnez **Vérifier + créer**.
1. Sélectionnez **Create** (Créer).

### <a name="peer-the-vnets"></a>Homologuer les réseaux virtuels

Maintenant, appairez les deux réseaux virtuels.

1. Sélectionnez le réseau virtuel **VN-Hub**.
2. Sous **Paramètres**, sélectionnez **Peerings**.
3. Sélectionnez **Ajouter**.
4. Sous **Ce réseau virtuel**, pour le **Nom du lien de peering**, tapez **Peer-HubSpoke**.
5. Sous **Réseau virtuel distant**, pour **Nom du lien de peering**, tapez **Peer-SpokeHub**. 
1. Sélectionnez **VN-Spoke** pour le réseau virtuel.
1. Acceptez les autres valeurs par défaut, puis sélectionnez **Ajouter**.

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Créez une machine virtuelle de charge de travail, puis placez-la dans le sous-réseau **SN-Workload**.

1. Dans le menu du Portail Azure, sélectionnez **Créer une ressource**.
2. Sous **Populaire**, sélectionnez **Windows Server 2016 Datacenter**.

**Concepts de base**

1. Pour **Abonnement**, sélectionnez votre abonnement.
1. Pour **Groupe de ressources**, sélectionnez **RG-DNAT-Test**.
1. Sous **Nom de la machine virtuelle**, tapez **Srv-Workload**.
1. Pour **Région**, sélectionnez le même emplacement que celui utilisé précédemment.
1. Entrez un nom d’utilisateur et un mot de passe.
1. Sélectionnez **Suivant : Disques**.

**Disques**
1. Sélectionnez **Suivant : Mise en réseau**.

**Mise en réseau**

1. Pour **Réseau virtuel**, sélectionnez **VN-Spoke**.
2. Pour **Sous-réseau**, sélectionnez **SN-Workload**.
3. Pour **Adresse IP publique**, sélectionnez **Aucune**.
4. Pour **Ports d’entrée publics**, sélectionnez **Aucun**. 
2. Conservez les autres paramètres par défaut, puis sélectionnez **Suivant : Gestion**.

**Gestion**

1. Pour **Diagnostics de démarrage**, sélectionnez **Désactiver**.
1. Sélectionnez **Vérifier + créer**.

**Vérifier + créer**.

Passez en revue le récapitulatif, puis sélectionnez **Créer**. L’exécution de cette opération nécessite quelques minutes.

Une fois le déploiement terminé, notez l’adresse IP privée de la machine virtuelle. Elle servira ultérieurement pour configurer le pare-feu. Sélectionnez le nom de la machine virtuelle. Sous **Paramètres**, sélectionnez **Mise en réseau** pour trouver l’adresse IP privée.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="deploy-the-firewall"></a>Déployer le pare-feu

1. Dans la page d’accueil du portail, sélectionnez **Créer une ressource**.
1. Recherchez **pare-feu**, puis sélectionnez **Pare-feu**.
1. Sélectionnez **Create** (Créer). 
1. Sur la page **Créer un pare-feu**, utilisez le tableau suivant pour configurer le pare-feu :

   |Paramètre  |Valeur  |
   |---------|---------|
   |Abonnement     |\<your subscription\>|
   |Resource group     |Sélectionner **RG-DNAT-test** |
   |Nom     |**FW-DNAT-test**|
   |Région     |Sélectionnez le même emplacement que celui utilisé précédemment|
   |Gestion de pare-feu|**Utiliser des règles de pare-feu (classique) pour gérer ce pare-feu**|
   |Choisir un réseau virtuel     |**Utiliser l’existant** : VN-Hub|
   |Adresse IP publique     |**Ajouter nouveau**, Nom : **fw-pip**.|

5. Acceptez les autres valeurs par défaut, puis sélectionnez **Vérifier + créer**.
6. Passez en revue le récapitulatif, puis sélectionnez **Créer** pour créer le pare-feu.

   Le déploiement nécessite quelques minutes.
7. Une fois le déploiement terminé, accédez au groupe de ressources **RG-DNAT-Test**, puis sélectionnez le pare-feu **FW-DNAT-test**.
8. Notez les adresses IP privée et publique du pare-feu. Vous les utiliserez ultérieurement lors de la création de l’itinéraire par défaut et de la règle NAT.

## <a name="create-a-default-route"></a>Créer un itinéraire par défaut

Pour le sous-réseau **SN-Workload**, vous devez configurer l’itinéraire sortant par défaut pour qu’il traverse le pare-feu.

1. À partir de la page d’accueil du portail Azure, sélectionnez **Tous les services**.
2. Sous **Mise en réseau**, sélectionnez **Tables d’itinéraires**.
3. Sélectionnez **Ajouter**.
5. Pour **Abonnement**, sélectionnez votre abonnement.
1. Pour **Groupe de ressources**, sélectionnez **RG-DNAT-Test**.
1. Pour **Région**, sélectionnez la même région que celle utilisée précédemment.
1. Dans le champ **Nom**, saisissez **RT-FWroute**.
1. Sélectionnez **Revoir + créer**.
1. Sélectionnez **Create** (Créer).
1. Sélectionnez **Accéder à la ressource**.
1. Sélectionnez **Sous-réseaux**, puis **Associer**.
1. Pour **Réseau virtuel**, sélectionnez **VN-Spoke**.
1. Pour **Sous-réseau**, sélectionnez **SN-Workload**.
1. Sélectionnez **OK**.
1. Sélectionnez **Routes**, puis **Ajouter**.
1. Pour **Nom de l’itinéraire**, entrez **FW-DG**.
1. Pour **Préfixe d’adresse**, entrez **0.0.0.0/0**.
1. Pour **Type de tronçon suivant**, sélectionnez **Appliance virtuelle**.

    Le Pare-feu Azure est en réalité un service managé, mais l’appliance virtuelle fonctionne dans ce cas.
18. Pour **Adresse de tronçon suivant**, entrez l’adresse IP privée pour le pare-feu que vous avez notée précédemment.
19. Sélectionnez **OK**.

## <a name="configure-a-nat-rule"></a>Configurer une règle NAT

1. Ouvrez le groupe de ressources **RG-DNAT-Test**, puis sélectionnez le pare-feu **FW-DNAT-test**. 
2. Sur la page **FW-DNAT-test**, sous **Paramètres**, sélectionnez **Règles (classique)** . 
3. Sélectionnez **Ajouter une collection de règles NAT**. 
4. Dans le champ **Nom**, saisissez **RC-DNAT-01**. 
5. Pour **Priorité**, entrez **200**. 
6. Sous **Règles**, pour **Nom**, entrez **RL-01**.
7. Pour **Protocole**, sélectionnez **TCP**.
1. Pour **Type de source**, sélectionnez **Adresse IP**.
1. Pour **Source**, tapez *. 
1. Pour **Adresses de destination**, tapez l’adresse IP publique du pare-feu. 
1. Pour **Ports de destination**, entrez **3389**. 
1. Dans le champ **Adresse traduite**, saisissez l’adresse IP privée de la machine virtuelle Srv-Workload. 
1. Dans le champ **Port traduit**, tapez **3389**. 
1. Sélectionnez **Ajouter**. L’exécution de cette opération nécessite quelques minutes.

## <a name="test-the-firewall"></a>Tester le pare-feu

1. Connectez un bureau distant à l’adresse IP publique du pare-feu. Vous devriez être connecté à la machine virtuelle **Srv-Workload**.
2. Fermez le bureau à distance.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Vous pouvez garder vos ressources de pare-feu à des fins de test complémentaire ou, si vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources **RG-DNAT-Test** pour supprimer toutes les ressources associées au pare-feu.

## <a name="next-steps"></a>Étapes suivantes

Ensuite, vous pouvez surveiller les journaux d’activité de Pare-feu Azure.

[Tutoriel : Superviser les journaux d’activité de Pare-feu Azure](./firewall-diagnostics.md)
