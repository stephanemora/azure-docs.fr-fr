---
title: Filtrer le trafic entrant avec pare-feu Azure DNAT via le portail Azure
description: Ce didacticiel vous apprend à déployer et configurer un pare-feu Azure DNAT à l’aide du portail Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 766ad04251fbe404d43734115e41e23ae0a4be28
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982038"
---
# <a name="tutorial-filter-inbound-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Didacticiel : Filtrer le trafic entrant avec pare-feu Azure DNAT via le portail Azure

Vous pouvez configurer Azure Firewall Destination Network Address Translation (DNAT) pour traduire et filtrer le trafic entrant vers vos sous-réseaux. Le pare-feu Azure ne comporte pas de concept de règles d’entrée et de sortie. Il existe des règles d’application et des règles de réseau, et elles s’appliquent à tout le trafic qui entre dans le pare-feu. Les règles de réseau sont appliquées en premier, puis les règles d’application. Ensuite, les règles prennent fin.

>[!NOTE]
>La fonctionnalité de pare-feu DNAT est actuellement disponible dans Azure PowerShell et REST uniquement.

Par exemple, si une règle de réseau est mise en correspondance, le paquet n’est pas évalué par les règles d’application. S’il n’y a pas de correspondance avec les règles de réseau, et si le paquet a un protocole HTTP/HTTPS, il est évalué par les règles d’application. Si aucune correspondance n’est trouvée, le paquet est alors évalué selon la [collection de règles de l’infrastructure ](infrastructure-fqdns.md). S’il n’existe toujours pas de correspondance, le paquet est refusé par défaut.

Lorsque vous configurez le DNAT, l’action de collection de règles NAT est définie sur **Destination Network Address Translation (DNAT)**. L’adresse IP et le port publics du pare-feu se traduisent par une adresse IP et un port privés. Ensuite, les règles sont appliquées normalement : d’abord les règles de réseau, puis les règles d’application. Par exemple, vous pouvez configurer une règle de réseau pour autoriser le trafic du bureau à distance sur le port TCP 3389. La traduction des adresses est effectuée en premier, puis les règles de réseau et les règles d’application sont appliquées en utilisant les adresses traduites.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer un environnement réseau de test
> * Déployer un pare-feu
> * Créer un itinéraire par défaut
> * Configurer une règle DNAT
> * Configurer une règle de réseau
> * Tester le pare-feu

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Pour ce didacticiel, vous créez deux réseaux virtuels appairés :
- **VN-Hub** : le pare-feu est dans ce réseau virtuel.
- **VN-Spoke** : le serveur de la charge de travail est dans ce réseau virtuel.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
1. Connectez-vous au portail Azure sur [http://portal.azure.com](http://portal.azure.com).
1. Dans la page d’accueil du portail Azure, cliquez sur **Groupes de ressources**, puis cliquez sur **Ajouter**.
2. Pour **Nom du groupe de ressources**, entrez **RG-DNAT-Test**.
3. Pour **Abonnement**, sélectionnez votre abonnement.
4. Pour **Emplacement du groupe de ressources**, sélectionnez un emplacement. Toutes les ressources suivantes que vous créez doivent se trouver dans le même emplacement.
5. Cliquez sur **Créer**.

## <a name="set-up-the-network-environment"></a>Configurer l’environnement réseau
Tout d’abord, créez les réseaux virtuels, puis appairez-les.

### <a name="create-the-hub-vnet"></a>Créer le réseau virtuel du hub
1. À partir de la page d’accueil du portail Azure, cliquez sur **Tous les services**.
2. Sous **Mise en réseau**, cliquez sur **Réseaux virtuels**.
3. Cliquez sur **Add**.
4. Dans le champ **Nom**, saisissez **VN-Hub**.
5. Pour **Espace d’adressage**, entrez **10.0.0.0/16**.
7. Pour **Abonnement**, sélectionnez votre abonnement.
8. Pour **Groupe de ressources**, sélectionnez **Existant** puis **RG-DNAT-Test**.
9. Pour **Emplacement**, sélectionnez le même emplacement que celui utilisé précédemment.
10. Sous **Sous-réseau**, pour **Nom**, entrez **AzureFirewallSubnet**.

     Le pare-feu se trouvera dans ce sous-réseau et le nom du sous-réseau **doit** être AzureFirewallSubnet.
     > [!NOTE]
     > La taille minimale du sous-réseau AzureFirewallSubnet est /25.
11. Pour **Plage d’adresses**, entrez **10.0.1.0/24**.
12. Utilisez les autres paramètres par défaut, puis cliquez sur **Créer**.

### <a name="create-a-spoke-vnet"></a>Créer un réseau virtuel spoke

1. À partir de la page d’accueil du portail Azure, cliquez sur **Tous les services**.
2. Sous **Mise en réseau**, cliquez sur **Réseaux virtuels**.
3. Cliquez sur **Add**.
4. Pour **Nom**, tapez **VN-Spoke**.
5. Pour **Espace d’adressage**, entrez **192.168.0.0/16**.
7. Pour **Abonnement**, sélectionnez votre abonnement.
8. Pour **Groupe de ressources**, sélectionnez **Existant** puis **RG-DNAT-Test**.
9. Pour **Emplacement**, sélectionnez le même emplacement que celui utilisé précédemment.
10. Sous **Sous-réseau**, tapez **SN-Workload** pour **Nom**.

    Le serveur se trouve dans ce sous-réseau.
1. Pour **Plage d’adresses**, entrez **192.168.1.0/24**.
2. Utilisez les autres paramètres par défaut, puis cliquez sur **Créer**.

### <a name="peer-the-vnets"></a>Homologuer les réseaux virtuels

Maintenant, appairez les deux réseaux virtuels.

#### <a name="hub-to-spoke"></a>Hub à spoke

1. Cliquez sur le réseau virtuel **VN-Hub**.
2. Sous **Paramètres**, cliquez sur **Peerings**.
3. Cliquez sur **Add**.
4. Tapez **Peer-HubSpoke** pour le nom.
5. Sélectionnez **VN-Spoke** pour le réseau virtuel.
7. Cliquez sur **OK**.

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
   |NOM     |FW-DNAT-test|
   |Abonnement     |\<votre abonnement\>|
   |Groupe de ressources     |**Utiliser l’existant** : RG-DNAT-Test |
   |Lieu     |Sélectionnez le même emplacement que celui utilisé précédemment|
   |Choisir un réseau virtuel     |**Utiliser l’existant** : VN-Hub|
   |Adresse IP publique     |**Créer un nouveau**. L’adresse IP publique doit être le type de référence (SKU) Standard.|

2. Cliquez sur **Revoir + créer**.
3. Passez en revue le résumé, puis cliquez sur **Créer** pour créer le pare-feu.

   Le déploiement nécessite quelques minutes.
4. Une fois le déploiement terminé, accédez au groupe de ressources **RG-DNAT-Test**, puis cliquez sur le pare-feu **FW-DNAT-test**.
6. Notez l’adresse IP privée. Vous l’utiliserez plus tard lors de la création de l’itinéraire par défaut.


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
1. Pour **Adresse de tronçon suivant**, entrez l’adresse IP privée pour le pare-feu que vous avez notée précédemment.
2. Cliquez sur **OK**.


## <a name="configure-a-dnat-rule"></a>Configurer une règle DNAT

```azurepowershell-interactive
 $rgName  = "RG-DNAT-Test"
 $firewallName = "FW-DNAT-test"
 $publicip = type the Firewall public ip
 $newAddress = type the private IP address for the Srv-Workload virtual machine 
 
# Get Firewall
    $firewall = Get-AzureRmFirewall -ResourceGroupName $rgName -Name $firewallName
  # Create NAT rule
    $natRule = New-AzureRmFirewallNatRule -Name RL-01 -SourceAddress * -DestinationAddress $publicip -DestinationPort 3389 -Protocol TCP -TranslatedAddress $newAddress -TranslatedPort 3389
  # Create NAT rule collection
    $natRuleCollection = New-AzureRmFirewallNatRuleCollection -Name RC-DNAT-01 -Priority 200 -Rule $natRule
  # Add NAT Rule collection to firewall:
    $firewall.AddNatRuleCollection($natRuleCollection)
  # Save:
    $firewall | Set-AzureRmFirewall
```
## <a name="configure-a-network-rule"></a>Configurer une règle de réseau

1. Ouvrez **RG-DNAT-Test**, puis cliquez sur le pare-feu **FW-DNAT-test**.
1. Sur la page **FW-DNAT-test**, sous **Paramètres**, cliquez sur **Règles**.
2. Cliquez sur **Ajouter un regroupement de règles de réseau**.

Configurez la règle à l’aide du tableau suivant, puis cliquez sur **Ajouter** :


|Paramètre  |Valeur  |
|---------|---------|
|NOM     |**RC-Net-01**|
|Priorité     |**200**|
|Action     |**Autoriser**|

Sous **Règles** :

|Paramètre  |Paramètre  |
|---------|---------|
|NOM     |**RL-RDP**|
|Protocole     |**TCP**|
|Adresses sources     |*|
|Adresses de destination     |Adresse IP privée **Srv-Workload**|
|Ports de destination|**3389**|


## <a name="test-the-firewall"></a>Tester le pare-feu

1. Connectez un bureau distant à l’adresse IP publique du pare-feu. Vous devriez être connecté à la machine virtuelle **Srv-Workload**.
3. Fermez le bureau à distance.
4. Passez l’action **RC-Net-01** de collection de règles de réseau à **Deny**.
5. Essayez à nouveau de vous connecter à l’adresse IP publique du pare-feu. Cette fois, l’opération devrait échouer en raison de la règle **Deny**.

## <a name="clean-up-resources"></a>Supprimer les ressources

Vous pouvez garder vos ressources de pare-feu pour le prochain didacticiel, ou, si vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources **RG-DNAT-Test** pour supprimer toutes les ressources associées au pare-feu.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Configurer un environnement réseau de test
> * Déployer un pare-feu
> * Créer un itinéraire par défaut
> * Configurer une règle DNAT
> * Configurer une règle de réseau
> * Tester le pare-feu

Ensuite, vous pouvez surveiller les journaux de Pare-feu Azure.

> [!div class="nextstepaction"]
> [Tutoriel : Surveiller les journaux de Pare-feu Azure](./tutorial-diagnostics.md)
