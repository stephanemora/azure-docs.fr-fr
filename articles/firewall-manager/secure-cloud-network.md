---
title: 'Tutoriel : Sécuriser votre hub virtuel avec Azure Firewall Manager'
description: Dans ce tutoriel, vous allez apprendre à sécuriser votre hub virtuel avec Azure Firewall Manager à l’aide du portail Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: c44daa67b4029c73c57ca82d72ee0a9759dd4c2d
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563651"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Tutoriel : Sécuriser votre hub virtuel avec Azure Firewall Manager

À l’aide d’Azure Firewall Manager, vous pouvez créer des hubs virtuels sécurisés pour sécuriser le trafic de votre réseau cloud à destination d’adresses IP privées, d’Azure PaaS et d’Internet. Le routage du trafic vers le pare-feu étant automatique, vous n’avez pas besoin de créer des itinéraires définis par l’utilisateur.

![sécuriser le réseau cloud](media/secure-cloud-network/secure-cloud-network.png)

Firewall Manager prend également en charge une architecture de réseau virtuel hub. Pour obtenir une comparaison des types d’architectures de hub virtuel sécurisé et de réseau virtuel hub, consultez [Quelles sont les options d’architecture d’Azure Firewall Manager ?](vhubs-and-vnets.md)

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer le réseau virtuel spoke
> * Créer un hub virtuel sécurisé
> * Connecter les réseaux virtuels hub et spoke
> * Créer une stratégie de pare-feu et sécuriser votre hub
> * Acheminer le trafic vers votre hub
> * Tester le pare-feu

## <a name="create-a-hub-and-spoke-architecture"></a>Créer une architecture hub and spoke

Commencez par créer un réseau virtuel en étoile où vous pouvez placer vos serveurs.

### <a name="create-a-spoke-virtual-network-and-subnets"></a>Créer un réseau virtuel et des sous-réseaux en étoile

1. Dans la page d’accueil du portail Azure, sélectionnez **Créer une ressource**.
2. Sous **Mise en réseau**, sélectionnez **Réseau virtuel**.
2. Pour **Abonnement**, sélectionnez votre abonnement.
1. Pour **Groupe de ressources**, sélectionnez **Créer** et entrez **FW-Manager** comme nom, puis sélectionnez **OK**.
2. Pour **Nom**, entrez **Spoke-01**.
3. Pour **Région**, sélectionnez **(États-Unis) USA Est**.
4. Sélectionnez **Suivant : Adresses IP**.
1. Pour **Espace d’adressage**, acceptez la valeur par défaut **10.0.0.0/16**.
3. Sous **Nom du sous-réseau**, sélectionnez **Par défaut**.
4. Changez le nom du sous-réseau en **Workload-SN**.
5. Pour **Plage d’adresses de sous-réseau**, tapez **10.0.1.0/24**.
6. Sélectionnez **Enregistrer**.

Créez ensuite un sous-réseau pour un serveur intermédiaire.

1. Sélectionnez **Ajouter un sous-réseau**.
4. Pour **Nom du sous-réseau**, tapez **Jump-SN**.
5. Pour **Plage d’adresses du sous-réseau**, entrez **10.0.2.0/24**.
6. Sélectionnez **Ajouter**.

Maintenant, créez le réseau virtuel.

1. Sélectionnez **Revoir + créer**.
2. Sélectionnez **Create** (Créer).

### <a name="create-the-secured-virtual-hub"></a>Créer le hub virtuel sécurisé

Créez votre hub virtuel sécurisé à l’aide de Firewall Manager.

1. À partir de la page d’accueil du portail Azure, sélectionnez **Tous les services**.
2. Dans la zone de recherche, entrez **Firewall Manager** et sélectionnez **Firewall Manager**.
3. Dans la page **Firewall Manager**, sélectionnez **Voir les hubs virtuels sécurisés**.
4. Dans la page **Firewall Manager | Hubs virtuels sécurisés**, sélectionnez **Créer un hub virtuel sécurisé**.
5. Pour **Groupe de ressources**, sélectionnez **FW-Manager**.
7. Pour **Région**, sélectionnez **USA Est**.
1. Pour le **nom du hub virtuel sécurisé**, entrez **Hub-01**.
2. Pour **Espace d’adressage du hub**, entrez **10.1.0.0/16**.
3. Pour le nom du nouveau WAN virtuel, entrez **Vwan-01**.
4. Laissez la case à cocher **Inclure la passerelle VPN pour activer les Partenaires de sécurité de confiance** désactivée.
5. Sélectionnez **Suivant : Pare-feu Azure**.
6. Acceptez le paramètre **Pare-feu Azure** **Activé** par défaut, puis sélectionnez **Suivant : Partenaires de sécurité de confiance**.
7. Acceptez le paramètre **Partenaire de sécurité de confiance** **Désactivé** par défaut, puis sélectionnez **Suivant : Vérifier + créer**.
8. Sélectionnez **Create** (Créer). Le déploiement prend environ 30 minutes.

### <a name="connect-the-hub-and-spoke-vnets"></a>Connecter les réseaux virtuels hub et spoke

Vous pouvez maintenant appairer les réseaux virtuels hub et spoke.

1. Sélectionnez le groupe de ressources **FW-Manager**, puis le WAN virtuel **Vwan-01**.
2. Sous **Connectivité**, sélectionnez **Connexions de réseau virtuel**.
3. Sélectionnez **Ajouter une connexion**.
4. Pour **Nom de la connexion**, entrez **hub-spoke**.
5. Pour **Hubs**, sélectionnez **Hub-01**.
6. Pour **Groupe de ressources**, sélectionnez **FW-Manager**.
7. Pour **Réseau virtuel**, sélectionnez **Spoke-01**.
8. Sélectionnez **Create** (Créer).

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Créer une stratégie de pare-feu et sécuriser votre hub

Une stratégie de pare-feu définit des collections de règles pour diriger le trafic sur un ou plusieurs hubs virtuels sécurisés. Vous allez créer votre stratégie de pare-feu, puis sécuriser votre hub.

1. Dans Firewall Manager, sélectionnez **Voir les stratégies de Pare-feu Azure**.
2. Sélectionnez **Créer une stratégie de pare-feu Azure**.
3. Sous **Détails de la stratégie**, pour **Nom**, entrez **Policy-01** et pour **Région**, sélectionnez **USA Est**.
4. Sélectionnez **Suivant : Règles**.
5. Dans l’onglet **Règles**, sélectionnez **Ajouter une collection de règles**.
6. Dans la page **Ajouter une collection de règles**, entrez **RC-01** pour **Nom**.
7. Pour **Type de collection de règles**, sélectionnez **Application**.
8. Pour **Priorité**, tapez **100**.
9. Vérifiez que **Action de collection de règles** est défini sur **Autoriser**.
10. Pour **Nom**, entrez **Allow-msft**.
11. Pour **Type de source**, sélectionnez **Adresse IP**.
12. Pour **Source**, tapez **\*** .
13. Pour **Protocole**, entrez **http,https**.
14. Vérifiez que **Type de destination** est défini sur **FQDN**.
15. Pour **Destination**, entrez **\*.microsoft.com**.
16. Sélectionnez **Ajouter**.
17. Sélectionnez **Suivant : Renseignement sur les menaces**.
18. Sélectionnez **Suivant : Hubs**.
19. Sous l’onglet **Hubs**, sélectionnez **Associer des hubs virtuels**.
20. Sélectionnez **Hub-01**, puis **Ajouter**.
21. Sélectionnez **Revoir + créer**.
22. Sélectionnez **Create** (Créer).

Cette opération peut prendre environ cinq minutes voire plus.

## <a name="route-traffic-to-your-hub"></a>Acheminer le trafic vers votre hub

Vous devez maintenant vérifier que le trafic réseau est acheminé via votre pare-feu.

1. Dans Firewall Manager, sélectionnez **Hubs virtuels sécurisés**.
2. Sélectionnez **Hub-01**.
3. Sous **Paramètres**, sélectionnez **Configuration de la sécurité**.
4. Sous **Trafic Internet**, sélectionnez **Pare-feu Azure**.
5. Sous **Trafic privé**, sélectionnez **Envoyer via le Pare-feu Azure**.
10. Vérifiez que la connexion **hub-spoke** indique **Trafic Internet** comme étant **Sécurisé**.
11. Sélectionnez **Enregistrer**.


## <a name="test-your-firewall"></a>Tester votre pare-feu

Pour tester vos règles de pare-feu, vous devez déployer deux serveurs. Vous allez déployer Workload-Srv dans le sous-réseau Workload-SN pour tester les règles de pare-feu, et Jump-Srv pour pouvoir utiliser Bureau à distance pour vous connecter à partir d’Internet puis vous connecter à Workload-Srv.

### <a name="deploy-the-servers"></a>Déployer les serveurs

1. Dans le portail Azure, sélectionnez **Créer une ressource**.
2. Sélectionnez **Windows Server 2016 Datacenter** dans la liste **Populaire**.
3. Entrez ces valeurs pour la machine virtuelle :

   |Paramètre  |Valeur  |
   |---------|---------|
   |Resource group     |**FW-Manager**|
   |Nom de la machine virtuelle     |**Jump-Srv**|
   |Région     |**(États-Unis) USA Est**|
   |Nom d’utilisateur de l’administrateur     |Entrez un nom d’utilisateur|
   |Mot de passe     |Entrez un mot de passe|

4. Sous **Règles des ports d’entrée**, pour **Ports d’entrée publics**, sélectionnez **Autoriser les ports sélectionnés**.
5. Pour **Sélectionner des ports d’entrée**, sélectionnez **RDP (3389)** .
6. Acceptez les autres valeurs par défaut, puis sélectionnez **Suivant : Disques**.
7. Acceptez les disques par défaut, puis sélectionnez **Suivant : Mise en réseau**.
8. Vérifiez que **Spoke-01** est sélectionné pour le réseau virtuel et que le sous-réseau est **Jump-SN**.
9. Pour **Adresse IP publique**, acceptez le nom par défaut de la nouvelle adresse IP publique (Jump-Srv-ip).
11. Acceptez les autres valeurs par défaut, puis sélectionnez **Suivant : Gestion**.
12. Sélectionnez **Désactivé** pour désactiver les diagnostics de démarrage. Acceptez les autres valeurs par défaut, puis sélectionnez **Vérifier + créer**.
13. Vérifiez les paramètres sur la page de récapitulatif, puis sélectionnez **Créer**.

Utilisez les informations du tableau suivant pour configurer une autre machine virtuelle appelée **Workload-Srv**. Le reste de la configuration est identique à celle de la machine virtuelle Srv-Jump.

|Paramètre  |Valeur  |
|---------|---------|
|Subnet|**Workload-SN**|
|Adresse IP publique|**Aucun**|
|Aucun port d’entrée public|**Aucun**|

### <a name="add-a-route-table-and-default-route"></a>Ajouter une table de route et un itinéraire par défaut

Pour autoriser une connexion Internet sur Jump-Srv, vous devez créer une table de route et un itinéraire de passerelle par défaut vers Internet depuis le sous-réseau **Jump-SN**.

1. Dans le portail Azure, sélectionnez **Créer une ressource**.
2. Entrez **table de route** dans la zone de recherche, puis sélectionnez **Table de route**.
3. Sélectionnez **Create** (Créer).
4. Entrez **RT-01** pour **Nom**.
5. Sélectionnez votre abonnement, **FW-Manager** pour le groupe de ressources et **(États-Unis) USA Est** pour la région.
6. Sélectionnez **Create** (Créer).
7. Une fois le déploiement terminé, sélectionnez la table de route **RT-01**.
8. Sélectionnez **Itinéraires**, puis **Ajouter**.
9. Entrez **jump-to-inet** pour **Nom de l’itinéraire**.
10. Entrez **0.0.0.0/0** pour **Préfixe d’adresse**.
11. Sélectionnez **Internet** pour **Type de tronçon suivant**.
12. Sélectionnez **OK**.
13. Une fois le déploiement terminé, sélectionnez **Sous-réseaux**, puis **Associer**.
14. Sélectionnez **Spoke-01** pour **Réseau virtuel**.
15. Sélectionnez **Jump-SN** pour **Sous-réseau**.
16. Sélectionnez **OK**.

### <a name="test-the-rules"></a>Tester les règles

À présent, testez les règles de pare-feu pour vérifier qu’elles fonctionnent comme prévu.

1. Dans le portail Azure, passez en revue les paramètres réseau pour la machine virtuelle **Workload-Srv** et notez l’adresse IP privée.
2. Connectez un bureau à distance à la machine virtuelle **Jump-Srv**, puis connectez-vous. À partir de là, ouvrez une connexion de bureau distant à l’adresse IP privée **Workload-Srv**.

3. Ouvrez Internet Explorer et accédez à https://www.microsoft.com.
4. Sélectionnez **OK** > **Fermer** sur les alertes de sécurité d’Internet Explorer.

   La page d’accueil de Microsoft doit s’afficher.

5. Accédez à https://www.google.com.

   Vous devriez être bloqué par le pare-feu.

Maintenant que vous avez vérifié que les règles de pare-feu fonctionnent :

* Vous pouvez accéder au nom de domaine complet autorisé, mais pas à d’autres.



## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les partenaires de sécurité de confiance](trusted-security-partners.md)
