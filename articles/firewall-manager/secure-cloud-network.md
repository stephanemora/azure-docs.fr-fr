---
title: 'Tutoriel : Sécuriser votre hub virtuel avec Azure Firewall Manager'
description: Dans ce tutoriel, vous allez apprendre à sécuriser votre hub virtuel avec Azure Firewall Manager à l’aide du portail Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: victorh
ms.openlocfilehash: 14ec6fafbbadff2ecc37b229270c269f068a666f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670458"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Tutoriel : Sécuriser votre hub virtuel avec Azure Firewall Manager

À l’aide d’Azure Firewall Manager, vous pouvez créer des hubs virtuels sécurisés pour sécuriser le trafic de votre réseau cloud à destination d’adresses IP privées, d’Azure PaaS et d’Internet. Le routage du trafic vers le pare-feu étant automatique, vous n’avez pas besoin de créer des itinéraires définis par l’utilisateur.

![sécuriser le réseau cloud](media/secure-cloud-network/secure-cloud-network.png)

Firewall Manager prend également en charge une architecture de réseau virtuel hub. Pour obtenir une comparaison des types d’architectures de hub virtuel sécurisé et de réseau virtuel hub, consultez [Quelles sont les options d’architecture d’Azure Firewall Manager ?](vhubs-and-vnets.md)

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer le réseau virtuel spoke
> * Créer un hub virtuel sécurisé
> * Connecter les réseaux virtuels en étoile
> * Acheminer le trafic vers votre hub
> * Déployer les serveurs
> * Créer une stratégie de pare-feu et sécuriser votre hub
> * Tester le pare-feu

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-hub-and-spoke-architecture"></a>Créer une architecture hub and spoke

Commencez par créer un réseau virtuel en étoile où placer vos serveurs.

### <a name="create-two-spoke-virtual-networks-and-subnets"></a>Créer deux réseaux virtuels en étoile et des sous-réseaux

Les deux réseaux virtuels comporteront chacun un serveur de charge de travail et seront protégés par le pare-feu.

1. Dans la page d’accueil du portail Azure, sélectionnez **Créer une ressource**.
2. Recherchez **Réseau virtuel**, puis sélectionnez **Créer**.
2. Pour **Abonnement**, sélectionnez votre abonnement.
1. Pour **Groupe de ressources**, sélectionnez **Créer** et entrez **fw-manager-rg** comme nom, puis sélectionnez **OK**.
2. Pour **Nom**, entrez **Spoke-01**.
3. Pour **Région**, sélectionnez **(États-Unis) USA Est**.
4. Sélectionnez **Suivant : Adresses IP**.
1. Pour **Espace d’adressage**, entrez **10.0.0.0/16**.
3. Sous **Nom du sous-réseau**, sélectionnez **Par défaut**.
4. Pour **Nom du sous-réseau**, entrez **Workload-01-SN**.
5. Pour **Plage d’adresses de sous-réseau**, tapez **10.0.1.0/24**.
6. Sélectionnez **Enregistrer**.
1. Sélectionnez **Revoir + créer**.
2. Sélectionnez **Créer**.

Répétez cette procédure pour créer un autre réseau virtuel similaire :

Nom : **Spoke-02**<br>
Espace d’adressage : **10.1.0.0/16**<br>
Nom du sous-réseau : **Charge de travail-02-SN**<br>
Plage d’adresses de sous-réseau : **10.1.1.0/24**

### <a name="create-the-secured-virtual-hub"></a>Créer le hub virtuel sécurisé

Créez votre hub virtuel sécurisé à l’aide de Firewall Manager.

1. À partir de la page d’accueil du portail Azure, sélectionnez **Tous les services**.
2. Dans la zone de recherche, entrez **Firewall Manager** et sélectionnez **Firewall Manager**.
3. Dans la page **Firewall Manager**, sélectionnez **Voir les hubs virtuels sécurisés**.
4. Dans la page **Firewall Manager | Hubs virtuels sécurisés**, sélectionnez **Créer un hub virtuel sécurisé**.
5. Pour **Groupe de ressources**, sélectionnez **fw-manager-rg**.
7. Pour **Région**, sélectionnez **USA Est**.
1. Pour le **nom du hub virtuel sécurisé**, entrez **Hub-01**.
2. Pour **Espace d’adressage du hub**, entrez **10.2.0.0/16**.
3. Pour le nom du nouveau WAN virtuel, entrez **Vwan-01**.
4. Laissez la case à cocher **Inclure la passerelle VPN pour activer les Partenaires de sécurité de confiance** désactivée.
5. Sélectionnez **Suivant : Pare-feu Azure**.
6. Acceptez le paramètre **Pare-feu Azure** **Activé** par défaut, puis sélectionnez **Suivant : Partenaires de sécurité de confiance**.
7. Acceptez le paramètre **Partenaire de sécurité de confiance** **Désactivé** par défaut, puis sélectionnez **Suivant : Vérifier + créer**.
8. Sélectionnez **Create** (Créer). 

   Le déploiement prend environ 30 minutes.

Vous pouvez obtenir l’adresse IP publique du pare-feu une fois le déploiement terminé.

1. Ouvrez **Firewall Manager**.
2. Sélectionnez **Hubs virtuels**.
3. Sélectionnez **hub-01**.
7. Sélectionnez **Configuration d’adresse IP publique**.
8. Notez l’adresse IP publique à utiliser ultérieurement.

### <a name="connect-the-hub-and-spoke-virtual-networks"></a>Connecter les réseaux virtuels en étoile

À présent, vous pouvez appairer les réseaux virtuels en étoile.

1. Sélectionnez le groupe de ressources **fw-manager-rg**, puis le WAN virtuel **Vwan-01**.
2. Sous **Connectivité**, sélectionnez **Connexions de réseau virtuel**.
3. Sélectionnez **Ajouter une connexion**.
4. Pour **Nom de la connexion**, entrez **hub-spoke-01**.
5. Pour **Hubs**, sélectionnez **Hub-01**.
6. Pour **Groupe de ressources**, sélectionnez **fw-manager-rg**.
7. Pour **Réseau virtuel**, sélectionnez **Spoke-01**.
8. Sélectionnez **Create** (Créer).

Répétez la procédure pour connecter le réseau virtuel **spoke-02** : nom de la connexion -**hub-spoke-02**

## <a name="deploy-the-servers"></a>Déployer les serveurs

1. Dans le portail Azure, sélectionnez **Créer une ressource**.
2. Sélectionnez **Windows Server 2016 Datacenter** dans la liste **Populaire**.
3. Entrez ces valeurs pour la machine virtuelle :

   |Paramètre  |Valeur  |
   |---------|---------|
   |Resource group     |**fw-manager-rg**|
   |Nom de la machine virtuelle     |**Srv-workload-01**|
   |Région     |**(États-Unis) USA Est**|
   |Nom d’utilisateur de l’administrateur     |Entrez un nom d’utilisateur|
   |Mot de passe     |Entrez un mot de passe|

4. Sous **Règles des ports d’entrée**, pour **Ports d’entrée publics**, sélectionnez **Aucun**.
6. Acceptez les autres valeurs par défaut, puis sélectionnez **Suivant : Disques**.
7. Acceptez les disques par défaut, puis sélectionnez **Suivant : Mise en réseau**.
8. Sélectionnez **Spoke-01** pour le réseau virtuel, puis **Workload-01-SN** pour le sous-réseau.
9. Pour **Adresse IP publique**, sélectionnez **Aucune**.
11. Acceptez les autres valeurs par défaut, puis sélectionnez **Suivant : Gestion**.
12. Sélectionnez **Désactiver** pour désactiver les diagnostics de démarrage. Acceptez les autres valeurs par défaut, puis sélectionnez **Vérifier + créer**.
13. Vérifiez les paramètres sur la page de récapitulatif, puis sélectionnez **Créer**.

Utilisez les informations du tableau suivant pour configurer une autre machine virtuelle nommée **Srv-Workload-02**. Le reste de la configuration est le même que pour la machine virtuelle **Srv-workload-01**.

|Paramètre  |Valeur  |
|---------|---------|
|Réseau virtuel|**Spoke-02**|
|Subnet|**Charge de travail-02-SN**|

Une fois les serveurs déployés, sélectionnez une ressource de serveur, puis, dans **Mise en réseau**, notez l’adresse IP privée de chaque serveur.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Créer une stratégie de pare-feu et sécuriser votre hub

Une stratégie de pare-feu définit des collections de règles pour diriger le trafic sur un ou plusieurs hubs virtuels sécurisés. Vous allez créer votre stratégie de pare-feu, puis sécuriser votre hub.

1. Dans Firewall Manager, sélectionnez **Voir les stratégies de Pare-feu Azure**.
2. Sélectionnez **Créer une stratégie de pare-feu Azure**.
1. Pour **Groupe de ressources**, sélectionnez **fw-manager-rg**.
1. Sous **Détails de la stratégie**, pour **Nom**, entrez **Policy-01** et pour **Région**, sélectionnez **USA Est**.
1. Sélectionnez **Suivant : Paramètres DNS**.
1. Sélectionnez **Suivant : Inspection TLS (préversion)**
1. Sélectionnez **Suivant : Règles**.
1. Dans l’onglet **Règles**, sélectionnez **Ajouter une collection de règles**.
1. Dans la page **Ajouter une collection de règles**, entrez **App-RC-01** pour **Nom**.
1. Pour **Type de collection de règles**, sélectionnez **Application**.
1. Pour **Priorité**, tapez **100**.
1. Vérifiez que **Action de collection de règles** est défini sur **Autoriser**.
1. Pour **Nom**, entrez **Allow-msft**.
1. Pour **Type de source**, sélectionnez **Adresse IP**.
1. Pour **Source**, tapez **\*** .
1. Pour **Protocole**, entrez **http,https**.
1. Vérifiez que **Type de destination** est défini sur **FQDN**.
1. Pour **Destination**, entrez **\*.microsoft.com**.
1. Sélectionnez **Ajouter**.

Ajoutez une règle DNAT pour pouvoir connecter un bureau à distance à la machine virtuelle **Srv-Workload-01**.

1. Sélectionnez **Ajouter une collection de règles**.
1. Pour **Nom**, tapez **dnat-rdp**.
1. Comme **Type de collection de règles**, sélectionnez **DNAT**.
1. Pour **Priorité**, tapez **100**.
1. Pour **Nom**, entrez **Allow-rdp**.
1. Pour **Type de source**, sélectionnez **Adresse IP**.
1. Pour **Source**, tapez **\*** .
1. Pour **Protocole**, sélectionnez **TCP**.
1. Pour **Ports de destination**, tapez **3389**.
1. Pour **Type de destination**, sélectionnez **Adresse IP**.
1. Pour **Destination**, tapez l’adresse IP publique du pare-feu que vous avez notée précédemment.
1. Pour **Adresses traduites**, tapez l’adresse IP privée de  **Srv-Workload-01** que vous avez notée précédemment.
1. Dans le champ **Port traduit**, tapez **3389**.
1. Sélectionnez **Ajouter**.

Ajoutez une règle de réseau afin de pouvoir connecter un bureau à distance de **Srv-Workload-01** à **Srv-Workload-02**.

1. Sélectionnez **Ajouter une collection de règles**.
2. Dans le champ **Nom**, tapez **vnet-hub**.
3. Comme **Type de collection de règles**, sélectionnez **Réseau**.
4. Pour **Priorité**, tapez **100**.
1. Pour **Action de collection de règles**, sélectionnez **Autoriser**.
1. Pour le **Nom** de la règle, tapez **Allow-vnet**.
1. Pour **Type de source**, sélectionnez **Adresse IP**.
1. Pour **Source**, tapez **\*** .
1. Pour **Protocole**, sélectionnez **TCP**.
1. Pour **Ports de destination**, tapez **3389**.
1. Pour **Type de destination**, sélectionnez **Adresse IP**.
1. Pour **Destination**, tapez l’adresse IP privée **Srv-Workload-02** que vous avez notée précédemment.
1. Sélectionnez **Ajouter**.
1. Sélectionnez **Vérifier + créer**.
1. Sélectionnez **Create** (Créer).

## <a name="associate-policy"></a>Associer une stratégie

Associez la stratégie de pare-feu au hub.

1. Dans Firewall Manager, sélectionnez **Stratégies de Pare-feu Azure**.
1. Cochez la case **Policy-01**.
1. Sélectionnez **Gérer les associations/associer des hubs**.
1. Sélectionnez **hub-01**.
1. Sélectionnez **Ajouter**.

## <a name="route-traffic-to-your-hub"></a>Acheminer le trafic vers votre hub

Vous devez maintenant vérifier que le trafic réseau est acheminé via votre pare-feu.

1. Dans Firewall Manager, sélectionnez **Hubs virtuels**.
2. Sélectionnez **Hub-01**.
3. Sous **Paramètres**, sélectionnez **Configuration de la sécurité**.
4. Sous **Trafic Internet**, sélectionnez **Pare-feu Azure**.
5. Sous **Trafic privé**, sélectionnez **Envoyer via le Pare-feu Azure**.
1. Sélectionnez **Enregistrer**.

   La mise à jour des tables de routage prend quelques minutes.
1. Vérifiez que les deux connexions indiquent que le Pare-feu Azure sécurise le trafic Internet et privé.

## <a name="test-the-firewall"></a>Tester le pare-feu

Pour tester les règles de pare-feu, vous allez connecter un bureau à distance à l’aide de l’adresse IP publique du pare-feu, qui est traduite en **Srv-Workload-01**. À partir de là, vous allez utiliser un navigateur pour tester la règle d’application et connecter un bureau à distance à **Srv-Workload-02** pour tester la règle de réseau.

### <a name="test-the-application-rule"></a>Tester la règle d’application

À présent, testez les règles de pare-feu pour vérifier qu’elles fonctionnent comme prévu.

1. Connectez un bureau à distance à l’adresse IP publique du pare-feu, puis connectez-vous.

3. Ouvrez Internet Explorer et accédez à `https://www.microsoft.com`.
4. Sélectionnez **OK** > **Fermer** sur les alertes de sécurité d’Internet Explorer.

   La page d’accueil de Microsoft doit s’afficher.

5. Accédez à `https://www.google.com`.

   Vous devriez être bloqué par le pare-feu.

À présent que vous avez vérifié que la règle d’application de pare-feu fonctionne :

* Vous pouvez accéder au nom de domaine complet autorisé, mais pas à d’autres.

### <a name="test-the-network-rule"></a>Tester la stratégie réseau

Testez à présent la règle de réseau.

- À partir de Srv-Workload-01, ouvrez un bureau à distance à l’adresse IP privée Srv-Workload-02.

   Un bureau à distance doit se connecter à Srv-Workload-02.

Maintenant que vous avez vérifié que la règle de réseau du pare-feu fonctionne :
* Vous pouvez connecter un bureau à distance à un serveur situé dans un autre réseau virtuel.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous avez terminé de tester vos ressources de pare-feu, supprimez le groupe de ressources **fw-manager-rg** afin de supprimer toutes les ressources associées au pare-feu.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les partenaires de sécurité de confiance](trusted-security-partners.md)
