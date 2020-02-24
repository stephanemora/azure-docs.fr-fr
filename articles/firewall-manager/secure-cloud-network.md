---
title: 'Tutoriel : Sécuriser votre réseau étendu virtuel à l’aide de la préversion d’Azure Firewall Manager'
description: Dans ce tutoriel, vous allez apprendre à sécuriser votre réseau étendu virtuel avec Azure Firewall Manager à l’aide du portail Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 3dc94a8be265682fbe2128f2e5870dfdf5850a2d
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443055"
---
# <a name="tutorial-secure-your-virtual-wan-using-azure-firewall-manager-preview"></a>Tutoriel : Sécuriser votre réseau étendu virtuel à l’aide de la préversion d’Azure Firewall Manager 

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

À l’aide d’Azure Firewall Manager Preview, vous pouvez créer des hubs virtuels sécurisés pour sécuriser le trafic de votre réseau cloud à destination d’adresses IP privées, d’Azure PaaS et d’Internet. Le routage du trafic vers le pare-feu étant automatique, vous n’avez pas besoin de créer des itinéraires définis par l’utilisateur.

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

Commencez par créer un réseau virtuel spoke dans lequel vous placerez vos serveurs.

### <a name="create-a-spoke-vnet-and-subnets"></a>Créer un réseau virtuel spoke et des sous-réseaux

1. Dans la page d’accueil du portail Azure, sélectionnez **Créer une ressource**.
2. Sous **Mise en réseau**, sélectionnez **Réseau virtuel**.
4. Pour **Nom**, entrez **Spoke-01**.
5. Pour **Espace d’adressage**, entrez **10.0.0.0/16**.
6. Pour **Abonnement**, sélectionnez votre abonnement.
7. Pour **Groupe de ressources**, sélectionnez **Créer** et entrez **FW-Manager** comme nom, puis sélectionnez **OK**.
8. Pour **Emplacement**, sélectionnez **(États-Unis) USA Est**.
9. Sous **Sous-réseau**, pour **Nom**, tapez **Workload-SN**.
10. Pour **Plage d’adresses**, entrez **10.0.1.0/24**.
11. Acceptez les autres paramètres par défaut, puis sélectionnez **Créer**.

Créez ensuite un sous-réseau pour un serveur intermédiaire.

1. Dans la page d’accueil du portail Azure, sélectionnez **Groupes de ressources** > **FW-Manager**.
2. Sélectionnez le réseau virtuel **Spoke-01**.
3. Sélectionnez **Sous-réseaux** >  **+Sous-réseau**.
4. Pour **Nom**, entrez **Jump-SN**.
5. Pour **Plage d’adresses**, entrez **10.0.2.0/24**.
6. Sélectionnez **OK**.

### <a name="create-the-secured-virtual-hub"></a>Créer le hub virtuel sécurisé

Créez votre hub virtuel sécurisé à l’aide de Firewall Manager.

1. À partir de la page d’accueil du portail Azure, sélectionnez **Tous les services**.
2. Dans la zone de recherche, entrez **Firewall Manager** et sélectionnez **Firewall Manager**.
3. Dans la page **Firewall Manager**, sélectionnez **Créer un hub virtuel sécurisé**.
4. Dans la page **Créer un hub virtuel sécurisé**, sélectionnez votre abonnement et le groupe de ressources **FW-Manager**.
5. Pour le **nom du hub virtuel sécurisé**, entrez **Hub-01**.
6. Pour **Emplacement**, sélectionnez **USA Est**.
7. Pour **Espace d’adressage du hub**, entrez **10.1.0.0/16**.
8. Pour le nom du nouveau vWAN, entrez **vwan-01**.
9. Laissez la case à cocher **Inclure la passerelle VPN pour activer les Partenaires de sécurité de confiance** désactivée.
10. Sélectionnez **Suivant : Pare-feu Azure**.
11. Acceptez le paramètre **Pare-feu Azure** **Activé** par défaut, puis sélectionnez **Suivant : Partenaires de sécurité de confiance**.
12. Acceptez le paramètre **Partenaire de sécurité de confiance** **Désactivé** par défaut, puis sélectionnez **Suivant : Vérifier + créer**.
13. Sélectionnez **Create** (Créer). Le déploiement prend environ 30 minutes.

### <a name="connect-the-hub-and-spoke-vnets"></a>Connecter les réseaux virtuels hub et spoke

Vous pouvez maintenant appairer les réseaux virtuels hub et spoke.

1. Sélectionnez le groupe de ressources **FW-Manager**, puis le WAN virtuel **vwan-01**.
2. Sous **Connectivité**, sélectionnez **Connexions de réseau virtuel**.
3. Sélectionnez **Ajouter une connexion**.
4. Pour **Nom de la connexion**, entrez **hub-spoke**.
5. Pour **Hubs**, sélectionnez **Hub-01**.
6. Pour **Réseau virtuel**, sélectionnez **Spoke-01**.
7. Sélectionnez **OK**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Créer une stratégie de pare-feu et sécuriser votre hub

Une stratégie de pare-feu définit des collections de règles pour diriger le trafic sur un ou plusieurs hubs virtuels sécurisés. Vous allez créer votre stratégie de pare-feu, puis sécuriser votre hub.

1. Dans Firewall Manager, sélectionnez **Créer une stratégie de pare-feu Azure**.
2. Sélectionnez votre abonnement, puis le groupe de ressources **FW-Manager**.
3. Sous **Détails de la stratégie**, pour **Nom**, entrez **Policy-01** et pour **Région**, sélectionnez **USA Est**.
4. Sélectionnez **Suivant : Règles**.
5. Dans l’onglet **Règles**, sélectionnez **Ajouter une collection de règles**.
6. Dans la page **Ajouter une collection de règles**, entrez **RC-01** pour **Nom**.
7. Pour **Type de collection de règles**, sélectionnez **Application**.
8. Pour **Priorité**, tapez **100**.
9. Vérifiez que **Action de collection de règles** est défini sur **Autoriser**.
10. Pour **Nom**, entrez **Allow-msft**.
11. Pour **Adresse source**, entrez **\*** .
12. Pour **Protocole**, entrez **http,https**.
13. Vérifiez que **Type de destination est défini sur **FQDN**.
14. Pour **Destination**, entrez **\*.microsoft.com**.
15. Sélectionnez **Ajouter**.
16. Sélectionnez **Suivant : Hubs virtuels sécurisés**.
17. Dans l’onglet **Hubs virtuels sécurisés**, sélectionnez **Hub-01**.
19. Sélectionnez **Revoir + créer**.
20. Sélectionnez **Create** (Créer).

Cette opération peut prendre environ cinq minutes voire plus.

## <a name="route-traffic-to-your-hub"></a>Acheminer le trafic vers votre hub

Vous devez maintenant vérifier que le trafic réseau est acheminé via votre pare-feu.

1. Dans Firewall Manager, sélectionnez **Hubs virtuels sécurisés**.
2. Sélectionnez **Hub-01**.
3. Sous **Paramètres**, sélectionnez **Paramètres de routage**.
4. Sous **Trafic Internet**, **Trafic provenant des réseaux virtuels**, sélectionnez **Envoyer via le Pare-feu Azure**.
5. Sous **Trafic privé Azure**, **Trafic vers les réseaux virtuels**, sélectionnez **Envoyer via le Pare-feu Azure**.
6. Sélectionnez **Modifier les préfixes d’adresse IP**.
7. Sélectionnez **Ajouter un préfixe d’adresse IP**.
8. Entrez **10.0.1.0/24** comme adresse du sous-réseau Charge de travail et sélectionnez **Enregistrer**.
9. Sous **Paramètres**, sélectionnez **Connexions**.
10. Sélectionnez la connexion **hub-spoke**, puis **Sécuriser le trafic Internet** et enfin **OK**.


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
   |Nom d’utilisateur de l’administrateur     |**azureuser**|
   |Mot de passe     |Tapez votre mot de passe|

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
