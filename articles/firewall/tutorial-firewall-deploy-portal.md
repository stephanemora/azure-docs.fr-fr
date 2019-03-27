---
title: 'Tutoriel : Déployer et configurer un pare-feu Azure à l’aide du portail Azure'
description: Ce didacticiel vous apprend à déployer et configurer un pare-feu Azure à l’aide du portail Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 3/14/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 288a6e1b1d88fcef6fbd5554ba811acc1dab776e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57994255"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Tutoriel : Déployer et configurer un pare-feu Azure à l’aide du portail Azure

Le contrôle de l’accès réseau sortant est une partie importante d’un plan de sécurité réseau global. Par exemple, vous pourriez souhaiter limiter l’accès à certains sites web, ou à certaines adresses IP et ports sortants.

Vous pouvez contrôler l’accès réseau sortant à partir d’un sous-réseau Azure à l’aide du Pare-feu Azure. Avec le Pare-feu Azure, vous pouvez configurer les éléments suivants :

* Règles d’application qui définissent des noms de domaine complets (FQDN) accessibles depuis un sous-réseau.
* Règles réseau qui définissent l’adresse source, le protocole, le port de destination et l’adresse de destination.

Le trafic réseau est soumis aux règles de pare-feu configurées lorsque vous routez votre trafic réseau vers le pare-feu en tant que sous-réseau de passerelle par défaut.

Pour ce didacticiel, vous devez créer un seul réseau virtuel simplifié avec trois sous-réseaux pour un déploiement facile. Pour les déploiements de production, un [modèle Hub and Spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) est recommandé, dans lequel le pare-feu est dans son propre réseau virtuel, et les serveurs de la charge de travail se trouvent dans des réseaux virtuels homologués dans la même région avec un ou plusieurs sous-réseaux.

- **AzureFirewallSubnet** : le pare-feu est dans ce sous-réseau.
- **Workload-SN** : le serveur de la charge de travail est dans ce sous-réseau. Le trafic réseau de ce sous-réseau traverse le pare-feu.
- **Jump-SN** : le serveur « jump » est dans ce sous-réseau. Le serveur de rebond possède une adresse IP publique à laquelle vous pouvez vous connecter à l’aide du Bureau à distance. De là, vous pouvez alors vous connecter (à l’aide d’un autre Bureau à distance) au serveur de la charge de travail.

![Tutoriel relatif à l’infrastructure réseau](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer un environnement réseau de test
> * Déployer un pare-feu
> * Créer un itinéraire par défaut
> * Configurer une application pour autoriser l’accès à msn.com
> * Configurer une règle de réseau pour autoriser l’accès aux serveurs DNS externes
> * Tester le pare-feu

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="set-up-the-network"></a>Configurer le réseau

Tout d’abord, créez un groupe de ressources qui contiendra les ressources nécessaires pour déployer le pare-feu. Ensuite, créez un réseau virtuel, des sous-réseaux et des serveurs de test.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Le groupe de ressources contient toutes les ressources utilisées dans ce didacticiel.

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).
2. Dans la page d’accueil du portail Azure, cliquez sur **Groupes de ressources** > **Ajouter**.
3. Pour **Nom du groupe de ressources**, entrez **Test-FW-RG**.
4. Pour **Abonnement**, sélectionnez votre abonnement.
5. Pour **Emplacement du groupe de ressources**, sélectionnez un emplacement. Toutes les ressources suivantes que vous créez doivent se trouver dans le même emplacement.
6. Cliquez sur **Créer**.

### <a name="create-a-vnet"></a>Créer un réseau virtuel

Ce réseau virtuel contient trois sous-réseaux.

1. À partir de la page d’accueil du portail Azure, cliquez sur **Tous les services**.
2. Sous **Mise en réseau**, cliquez sur **Réseaux virtuels**.
3. Cliquez sur **Add**.
4. Pour **Nom**, entrez **Test-FW-VN**.
5. Pour **Espace d’adressage**, entrez **10.0.0.0/16**.
6. Pour **Abonnement**, sélectionnez votre abonnement.
7. Pour **Groupe de ressources**, sélectionnez **Existant** > **Test-FW-RG**.
8. Pour **Emplacement**, sélectionnez le même emplacement que celui utilisé précédemment.
9. Sous **Sous-réseau**, pour **Nom**, entrez **AzureFirewallSubnet**. Le pare-feu se trouvera dans ce sous-réseau et le nom du sous-réseau **doit** être AzureFirewallSubnet.
10. Pour **Plage d’adresses**, entrez **10.0.1.0/24**.
11. Utilisez les autres paramètres par défaut, puis cliquez sur **Créer**.

> [!NOTE]
> La taille minimale du sous-réseau AzureFirewallSubnet est /26.

### <a name="create-additional-subnets"></a>Créer des sous-réseaux supplémentaires

Ensuite, créez des sous-réseaux pour le serveur de rebond et un sous-réseau pour les serveurs de la charge de travail.

1. Dans la page d’accueil du portail Azure, cliquez sur **Groupes de ressources** > **Test-FW-RG**.
2. Cliquez sur le réseau virtuel **Test-FW-VN**.
3. Cliquez sur **Sous-réseaux** > **+Sous-réseau**.
4. Pour **Nom**, entrez **Workload-SN**.
5. Pour **Plage d’adresses**, entrez **10.0.2.0/24**.
6. Cliquez sur **OK**.

Créez un autre sous-réseau nommé **Jump-SN**, avec la plage d’adresses **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Maintenant créez les machines virtuelles de rebond et de charge de travail, et placez-les dans les sous-réseaux appropriés.

1. Dans le portail Azure, cliquez sur **Créer une ressource**.
2. Cliquez sur **Compute**, puis sélectionnez **Windows Server 2016 Datacenter** dans la liste de suggestions.
3. Entrez ces valeurs pour la machine virtuelle :

    - *Test-FW-RG*, pour le groupe de ressources.
    - *Srv-Jump*, pour le nom de la machine virtuelle.
    - *azureuser* : pour le nom d’utilisateur administrateur.
    - *Azure123456!* pour le mot de passe.

4. Sous **Règles des ports d’entrée**, pour **Ports d’entrée publics**, cliquez sur **Autoriser les ports sélectionnés**.
5. Pour **Sélectionner des ports d’entrée**, sélectionnez **RDP (3389)**.

6. Acceptez toutes les autres valeurs par défaut et cliquez sur **Suivant : Disques**.
7. Acceptez toutes les autres valeurs par défaut et cliquez sur **Suivant : Mise en réseau**.
8. Assurez-vous que **Test-FW-VN** est sélectionné pour le réseau virtuel et que le sous-réseau est **Jump-SN**.
9. Pour **Adresse IP publique**, cliquez sur **Créer**.
10. Tapez **Srv-Jump-PIP** pour le nom de l’adresse IP publique et cliquez sur **OK**.
11. Acceptez toutes les autres valeurs par défaut et cliquez sur **Suivant : Gestion**.
12. Cliquez sur **Désactivé** pour désactiver les diagnostics de démarrage. Acceptez les autres valeurs par défaut et cliquez sur **Vérifier + créer**.
13. Vérifiez les paramètres sur la page de résumé, puis cliquez sur **Créer**.

Répétez ce processus pour créer une autre machine virtuelle nommée **Srv-Work**.

Utilisez les informations du tableau suivant pour configurer la machine virtuelle Srv-Work. Le reste de la configuration est identique à celle de la machine virtuelle Srv-Jump.

|Paramètre  |Valeur  |
|---------|---------|
|Sous-réseau|Workload-SN|
|Adresse IP publique|Aucun|
|Aucun port d’entrée public|Aucun|

## <a name="deploy-the-firewall"></a>Déployer le pare-feu

Déployez le pare-feu dans le réseau virtuel.

1. À partir de la page accueil du portail, cliquez sur **Créer une ressource**.
2. Cliquez sur **Mise en réseau**, et après **Recommandés**, cliquez sur **Afficher tout**.
3. Cliquez sur **Pare-feu** > **Créer**. 
4. Sur la page **Créer un pare-feu**, utilisez le tableau suivant pour configurer le pare-feu :

   |Paramètre  |Valeur  |
   |---------|---------|
   |Nom     |Test-FW01|
   |Abonnement     |\<votre abonnement\>|
   |Groupe de ressources     |**Utiliser l’existant** : Test-FW-RG |
   |Lieu     |Sélectionnez le même emplacement que celui utilisé précédemment|
   |Choisir un réseau virtuel     |**Utiliser l’existant** : Test-FW-VN|
   |Adresse IP publique     |**Créer un nouveau**. L’adresse IP publique doit être le type de référence (SKU) Standard.|

5. Cliquez sur **Revoir + créer**.
6. Passez en revue le résumé, puis cliquez sur **Créer** pour créer le pare-feu.

   Le déploiement nécessite quelques minutes.
7. Une fois le déploiement terminé, accédez au groupe de ressources **Test-FW-RG**, puis cliquez sur le pare-feu **Test-FW01**.
8. Notez l’adresse IP privée. Vous l’utiliserez plus tard lors de la création de l’itinéraire par défaut.

## <a name="create-a-default-route"></a>Créer un itinéraire par défaut

Pour le sous-réseau **Workload-SN**, configurez l’itinéraire sortant par défaut pour qu’il traverse le pare-feu.

1. À partir de la page d’accueil du portail Azure, cliquez sur **Tous les services**.
2. Sous **Mise en réseau**, cliquez sur **Tables de routage**.
3. Cliquez sur **Add**.
4. Pour **Nom**, entrez **Firewall-route**.
5. Pour **Abonnement**, sélectionnez votre abonnement.
6. Pour **Groupe de ressources**, sélectionnez **Existant** puis **Test-FW-RG**.
7. Pour **Emplacement**, sélectionnez le même emplacement que celui utilisé précédemment.
8. Cliquez sur **Créer**.
9. Cliquez sur **Actualiser**, puis sur la table de routage **Firewall-route**.
10. Cliquez sur **Sous-réseaux** > **Associer**.
11. Cliquez sur **Réseau virtuel** > **Test-FW-VN**.
12. Pour **Sous-réseau**, cliquez sur **Workload-SN**. Veillez à ne sélectionner que le sous-réseau **Workload-SN** pour cette route, sinon votre pare-feu ne fonctionnera pas correctement.

13. Cliquez sur **OK**.
14. Cliquez sur **Itinéraires** > **Ajouter**.
15. Pour **Nom de l’itinéraire**, entrez **FW-DG**.
16. Pour **Préfixe d’adresse**, entrez **0.0.0.0/0**.
17. Pour **Type de tronçon suivant**, sélectionnez **Appliance virtuelle**.

    Le Pare-feu Azure est en réalité un service managé, mais l’appliance virtuelle fonctionne dans ce cas.
18. Pour **Adresse de tronçon suivant**, entrez l’adresse IP privée pour le pare-feu que vous avez notée précédemment.
19. Cliquez sur **OK**.

## <a name="configure-an-application-rule"></a>Configurer une règle d’application

Il s’agit de la règle d’application qui autorise un accès sortant à msn.com.

1. Ouvrez **Test-FW-RG**, et cliquez sur le pare-feu **Test-FW01**.
2. Sur la page **Test-FW01**, sous **Paramètres**, cliquez sur **Règles**.
3. Cliquez sur l’onglet **Collection de règles d’application**.
4. Cliquez sur **Ajouter un regroupement de règles d’application**.
5. Pour **Nom**, entrez **App-Coll01**.
6. Pour **Priorité**, entrez **200**.
7. Pour **Action**, sélectionnez **Autoriser**.
8. Sous **Règles**, **Noms de domaine complets cibles**, pour **Nom**, entrez **AllowGH**.
9. Pour **Adresses sources**, entrez **10.0.2.0/24**.
10. Pour **Protocol:port**, entrez **http, https**.
11. Pour **Noms de domaine complets cibles**, entrez **msn.com**
12. Cliquez sur **Add**.

Le Pare-feu Azure comprend un regroupement de règles intégré pour les noms de domaine complets d’infrastructure qui sont autorisés par défaut. Ces noms de domaine complets sont spécifiques à la plateforme et ne peuvent pas être utilisés à d’autres fins. Pour plus d’informations, consultez [Noms de domaine complets d’infrastructure](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Configurer une règle de réseau

Il s’agit de la règle de réseau qui autorise un accès sortant à deux adresses IP sur le port 53 (DNS).

1. Cliquez sur l’onglet **Collection de règles de réseau**.
2. Cliquez sur **Ajouter un regroupement de règles de réseau**.
3. Pour **Nom**, entrez **Net-Coll01**.
4. Pour **Priorité**, entrez **200**.
5. Pour **Action**, sélectionnez **Autoriser**.

6. Sous **Règles**, pour **Nom**, entrez **AllowDNS**.
7. Pour **Protocole**, sélectionnez **UDP**.
8. Pour **Adresses sources**, entrez **10.0.2.0/24**.
9. Pour Adresse de destination, entrez **209.244.0.3,209.244.0.4**
10. Pour **Ports de destination**, entrez **53**.
11. Cliquez sur **Add**.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Modifier les adresses DNS principales et secondaires de l’interface réseau **Srv-Work**

À des fins de test dans ce tutoriel, vous devez configurer les adresses DNS principales et secondaires. Ceci n’est pas obligatoire pour le pare-feu Azure.

1. À partir du portail Azure, ouvrez le groupe de ressources **Test-FW-RG**.
2. Cliquez sur l’interface réseau de la machine virtuelle **Srv-Work**.
3. Sous **Paramètres**, cliquez sur **Serveurs DNS**.
4. Sous **Serveurs DNS**, cliquez sur **Personnaliser**.
5. Entrez **209.244.0.3** dans la zone de texte **Ajouter un serveur DNS**, et **209.244.0.4** dans la zone de texte suivante.
6. Cliquez sur **Enregistrer**. 
7. Redémarrez la machine virtuelle **Srv-Work**.

## <a name="test-the-firewall"></a>Tester le pare-feu

Testez maintenant le pare-feu pour confirmer qu’il fonctionne comme prévu.

1. À partir du portail Azure, passez en revue les paramètres réseau de la machine virtuelle **Srv-Work** et notez l’adresse IP privée.
2. Connectez un bureau à distance à la machine virtuelle **Srv-Jump**, et à partir de là, ouvrez une connexion Bureau à distance à l’adresse IP privée **Srv-Work**.

3. Ouvrez Internet Explorer et accédez à https://msn.com.
4. Cliquez sur **OK** > **Fermer** sur les alertes de sécurité.

   La page d’accueil MSN doit s’afficher.

5. Accédez à https://www.msn.com.

   Vous devriez être bloqué par le pare-feu.

Maintenant que vous avez vérifié que les règles de pare-feu fonctionnent :

- Vous pouvez accéder au nom de domaine complet autorisé, mais pas à d’autres.
- Vous pouvez résoudre les noms DNS à l’aide du serveur DNS externe configuré.

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous pouvez garder vos ressources de pare-feu pour le prochain didacticiel, ou, si vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources **Test-FW-RG** pour supprimer toutes les ressources associées au pare-feu.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Superviser les journaux de Pare-feu Azure](./tutorial-diagnostics.md)
