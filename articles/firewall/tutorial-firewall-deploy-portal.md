---
title: Déployer et configurer un pare-feu Azure à l’aide du portail Azure
description: Ce didacticiel vous apprend à déployer et configurer un pare-feu Azure à l’aide du portail Azure.
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: tutorial
ms.date: 7/11/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: be11ea2195705b344638b93ea2657481897d6ef7
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358944"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Tutoriel : Déployer et configurer un pare-feu Azure à l’aide du portail Azure

Le pare-feu Azure comporte deux types de règles pour contrôler l’accès sortant :

- **Règles d’application**

   Vous permet de configurer des noms de domaine pleinement qualifiés (FQDN) qui sont accessibles depuis un sous-réseau. Par exemple, vous pouvez autoriser l’accès à *github.com* depuis votre sous-réseau.
- **Règles de réseau**

   Vous permet de configurer des règles contenant l’adresse source, le protocole, le port de destination et l’adresse de destination. Par exemple, vous pouvez créer une règle pour autoriser le trafic vers le port 53 (DNS) sur l’adresse IP de votre server DNS à partir de votre sous-réseau.

Le trafic réseau est soumis aux règles de pare-feu configurées lorsque vous routez votre trafic réseau vers le pare-feu en tant que sous-réseau de passerelle par défaut.

Les règles de réseau et d’application sont stockées dans les *regroupements de règles*. Un regroupement de règles est une liste de règles qui partagent la même action et la même priorité.  Un regroupement de règles de réseau est une liste de règles de réseau, et un regroupement de règles d’application est une liste de règles d’application.

Les regroupements de règles de réseau sont toujours traités avant les regroupements de règles d’application. Toutes les règles sont en train de se terminer, donc si une correspondance est trouvée dans un regroupement de règles de réseau, les regroupements de règles d’application suivants pour la session ne sont pas traités.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer un environnement réseau de test
> * Déployer un pare-feu
> * Créer un itinéraire par défaut
> * Configurer des règles d’application
> * Configurer des règles de réseau
> * Tester le pare-feu



Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Les exemples des articles sur le service Pare-feu Azure supposent que vous avez déjà activé la préversion publique de Pare-feu Azure. Pour plus d’informations, consultez [Activer la préversion publique du Pare-feu Azure](public-preview.md).

Pour ce tutoriel, vous devez créer un seul réseau virtuel avec trois sous-réseaux :
- **FW-SN** : le pare-feu est dans ce sous-réseau.
- **Workload-SN** : le serveur de la charge de travail est dans ce sous-réseau. Le trafic réseau de ce sous-réseau traverse le pare-feu.
- **Jump-SN** : le serveur « jump » est dans ce sous-réseau. Le serveur de rebond possède une adresse IP publique à laquelle vous pouvez vous connecter à l’aide du Bureau à distance. De là, vous pouvez alors vous connecter (à l’aide d’un autre Bureau à distance) au serveur de la charge de travail.

![Tutoriel relatif à l’infrastructure réseau](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Ce tutoriel utilise une configuration réseau simplifiée pour faciliter le déploiement. Pour les déploiements de production, un [modèle Hub and Spoke](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) est recommandé, dans lequel le pare-feu est dans son propre réseau virtuel, et les serveurs de la charge de travail se trouvent dans des réseaux virtuels homologués dans la même région avec un ou plusieurs sous-réseaux.



## <a name="set-up-the-network-environment"></a>Configurer l’environnement réseau
Tout d’abord, créez un groupe de ressources qui contiendra les ressources nécessaires pour déployer le pare-feu. Ensuite, créez un réseau virtuel, des sous-réseaux et des serveurs de test.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources
1. Connectez-vous au portail Azure sur [http://portal.azure.com](http://portal.azure.com).
1. Dans la page d’accueil du portail Azure, cliquez sur **Groupes de ressources**, puis cliquez sur **Ajouter**.
2. Pour **Nom du groupe de ressources**, entrez **Test-FW-RG**.
3. Pour **Abonnement**, sélectionnez votre abonnement.
4. Pour **Emplacement du groupe de ressources**, sélectionnez un emplacement. Toutes les ressources suivantes que vous créez doivent se trouver dans le même emplacement.
5. Cliquez sur **Créer**.


### <a name="create-a-vnet"></a>Créer un réseau virtuel
1. À partir de la page d’accueil du portail Azure, cliquez sur **Tous les services**.
2. Sous **Mise en réseau**, cliquez sur **Réseaux virtuels**.
3. Cliquez sur **Add**.
4. Pour **Nom**, entrez **Test-FW-VN**.
5. Pour **Espace d’adressage**, entrez **10.0.0.0/16**.
7. Pour **Abonnement**, sélectionnez votre abonnement.
8. Pour **Groupe de ressources**, sélectionnez **Existant** puis **Test-FW-RG**.
9. Pour **Emplacement**, sélectionnez le même emplacement que celui utilisé précédemment.
10. Sous **Sous-réseau**, pour **Nom**, entrez **AzureFirewallSubnet**.

    Le pare-feu se trouvera dans ce sous-réseau et le nom du sous-réseau **doit** être AzureFirewallSubnet.
11. Pour **Plage d’adresses**, entrez **10.0.1.0/24**.
12. Utilisez les autres paramètres par défaut, puis cliquez sur **Créer**.

> [!NOTE]
> La taille minimale du sous-réseau AzureFirewallSubnet est /25.

### <a name="create-additional-subnets"></a>Créer des sous-réseaux supplémentaires

Ensuite, créez des sous-réseaux pour le serveur de rebond et un sous-réseau pour les serveurs de la charge de travail.

1. Dans la page d’accueil du portail Azure, cliquez sur **Groupes de ressources**, puis cliquez sur **Test-FW-RG**.
2. Cliquez sur le réseau virtuel **Test-FW-VN**.
3. Sélectionnez **Sous-réseaux**, puis cliquez sur **+Sous-réseau**.
4. Pour **Nom**, entrez **Workload-SN**.
5. Pour **Plage d’adresses**, entrez **10.0.2.0/24**.
6. Cliquez sur **OK**.

Créez un autre sous-réseau nommé **Jump-SN**, avec la plage d’adresses **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Maintenant créez les machines virtuelles de rebond et de charge de travail, et placez-les dans les sous-réseaux appropriés.

1. À partir de la page d’accueil du portail Azure, cliquez sur **Tous les services**.
2. Sous **Compute**, cliquez sur **Machines virtuelles**.
3. Cliquez sur **Ajouter**, puis sur **Windows Server**. Sélectionnez **Windows Server 2016 Datacenter**, puis cliquez sur **Créer**.

**Concepts de base**

1. Pour **Nom**, entrez **Srv-Jump**.
5. Entrez un nom d’utilisateur et un mot de passe.
6. Pour **Abonnement**, sélectionnez votre abonnement.
7. Pour **Groupe de ressources**, cliquez sur **Existant** puis sur **Test-FW-RG**.
8. Pour **Emplacement**, sélectionnez le même emplacement que celui utilisé précédemment.
9. Cliquez sur **OK**.

**Taille**

1. Choisissez une taille appropriée pour une machine virtuelle de test exécutant Windows Server. Par exemple, **B2ms** (8 Go de RAM, 16 Go de stockage).
2. Cliquez sur **Sélectionner**.

**Paramètres**

1. Sous **Réseau**, sélectionnez **Test-FW-VN** pour **Réseau virtuel**.
2. Pour **Sous-réseau**, sélectionnez **Jump-SN**.
3. Pour **Sélectionner des ports entrants publics**, sélectionnez **RDP (3389)**. 

    Vous devez limiter l’accès à votre adresse IP publique, mais vous avez besoin d’ouvrir le port 3389 pour connecter un bureau à distance au serveur de rebond. 
2. Laissez les autres paramètres par défaut et cliquez sur **OK**.

**Résumé**

Consultez le résumé, puis cliquez sur **Créer**. L’exécution de cette opération nécessite quelques minutes.

Répétez ce processus pour créer une autre machine virtuelle nommée **Srv-Work**.

Utilisez les informations du tableau suivant pour configurer les **Paramètres** de la machine virtuelle Srv-Work. Le reste de la configuration est identique à celle de la machine virtuelle Srv-Jump.


|Paramètre  |Valeur  |
|---------|---------|
|Sous-réseau|Workload-SN|
|Adresse IP publique|Aucun|
|Sélectionner des ports entrants publics|Aucun port entrant public|


## <a name="deploy-the-firewall"></a>Déployer le pare-feu

1. À partir de la page accueil du portail, cliquez sur **Créer une ressource**.
2. Cliquez sur **Mise en réseau**, et après **Recommandés**, cliquez sur **Afficher tout**.
3. Cliquez sur **Pare-feu**, puis sur **Créer**. 
4. Sur la page **Créer un pare-feu**, utilisez le tableau suivant pour configurer le pare-feu :
   
   |Paramètre  |Valeur  |
   |---------|---------|
   |NOM     |Test-FW01|
   |Abonnement     |\<votre abonnement\>|
   |Groupe de ressources     |**Existant** : Test-FW-RG |
   |Lieu     |Sélectionnez le même emplacement que celui utilisé précédemment|
   |Choisir un réseau virtuel     |**Existant** : Test-FW-VN|
   |Adresse IP publique     |Création|

2. Cliquez sur **Revoir + créer**.
3. Passez en revue le résumé, puis cliquez sur **Créer** pour créer le pare-feu.

   Le déploiement nécessite quelques minutes.
4. Une fois le déploiement terminé, accédez au groupe de ressources **Test-FW-RG**, puis cliquez sur le pare-feu **Test-FW01**.
6. Notez l’adresse IP privée. Vous l’utiliserez plus tard lors de la création de l’itinéraire par défaut.

> [!NOTE]
> L’adresse IP publique doit être le type de référence (SKU) Standard.

[//]: # (N’oubliez pas de noter l’adresse IP privée pour le pare-feu.)

## <a name="create-a-default-route"></a>Créer un itinéraire par défaut

Pour le sous-réseau **Workload-SN**, vous devez configurer l’itinéraire sortant par défaut pour qu’il traverse le pare-feu.

1. À partir de la page d’accueil du portail Azure, cliquez sur **Tous les services**.
2. Sous **Mise en réseau**, cliquez sur **Tables de routage**.
3. Cliquez sur **Add**.
4. Pour **Nom**, entrez **Firewall-route**.
5. Pour **Abonnement**, sélectionnez votre abonnement.
6. Pour **Groupe de ressources**, sélectionnez **Existant** puis **Test-FW-RG**.
7. Pour **Emplacement**, sélectionnez le même emplacement que celui utilisé précédemment.
8. Cliquez sur **Créer**.
9. Cliquez sur **Actualiser**, puis sur la table de routage **Firewall-route**.
10. Cliquez sur **Sous-réseaux**, puis sur **Associer**.
11. Cliquez sur **Réseau virtuel**, puis sélectionnez **Test-FW-VN**.
12. Pour **Sous-réseau**, cliquez sur **Workload-SN**.
13. Cliquez sur **OK**.
14. Cliquez sur **Itinéraires**, puis sur **Ajouter**.
15. Pour **Nom de l’itinéraire**, entrez **FW-DG**.
16. Pour **Préfixe d’adresse**, entrez **0.0.0.0/0**.
17. Pour **Type de tronçon suivant**, sélectionnez **Appliance virtuelle**.

    Le Pare-feu Azure est en réalité un service managé, mais l’appliance virtuelle fonctionne dans ce cas.
1. Pour **Adresse de tronçon suivant**, entrez l’adresse IP privée pour le pare-feu que vous avez notée précédemment.
2. Cliquez sur **OK**.


## <a name="configure-application-rules"></a>Configurer des règles d’application


1. Ouvrez **Test-FW-RG**, et cliquez sur le pare-feu **Test-FW01**.
1. Sur la page **Test-FW01**, sous **Paramètres**, cliquez sur **Règles**.
2. Cliquez sur **Ajouter un regroupement de règles d’application**.
3. Pour **Nom**, entrez **App-Coll01**.
1. Pour **Priorité**, entrez **200**.
2. Pour **Action**, sélectionnez **Autoriser**.

6. Sous **Règles**, pour **Nom**, entrez **AllowGH**.
7. Pour **Adresses sources**, entrez **10.0.2.0/24**.
8. Pour **Protocol:port**, entrez **http, https**. 
9. Pour **Noms de domaine complets (FQDN) cibles**, entrez **github.com**
10. Cliquez sur **Add**.

> [!NOTE]
> Le Pare-feu Azure comprend un regroupement de règles intégré pour les noms de domaine complets d’infrastructure qui sont autorisés par défaut. Ces noms de domaine complets sont spécifiques à la plateforme et ne peuvent pas être utilisés à d’autres fins. Les noms de domaine complets d’infrastructure autorisés incluent :
>- l’accès Compute au référentiel d’images de la plateforme (PIR) de stockage ;
>- l’accès de stockage de l’état des disques managés.
>- Diagnostics Windows
>
> Vous pouvez remplacer ce regroupement de règles d’infrastructure intégré en créant un regroupement de règles d’application *Refuser tout* qui sera traité en dernier. Il sera toujours traité avant le groupe de règles d’infrastructure. Tout ce qui ne se trouve pas dans le regroupement de règles d’infrastructure est refusé par défaut.

## <a name="configure-network-rules"></a>Configurer des règles de réseau

1. Cliquez sur **Ajouter un regroupement de règles de réseau**.
2. Pour **Nom**, entrez **Net-Coll01**.
3. Pour **Priorité**, entrez **200**.
4. Pour **Action**, sélectionnez **Autoriser**.

6. Sous **Règles**, pour **Nom**, entrez **AllowDNS**.
8. Pour **Protocole**, sélectionnez **UDP**.
9. Pour **Adresses sources**, entrez **10.0.2.0/24**.
10. Pour Adresse de destination, entrez **209.244.0.3,209.244.0.4**
11. Pour **Ports de destination**, entrez **53**.
12. Cliquez sur **Add**.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Modifier les adresses DNS principales et secondaires de l’interface réseau **Srv-Work**

À des fins de test dans ce tutoriel, vous devez configurer les adresses DNS principales et secondaires. Ceci n’est pas obligatoire pour le Pare-feu Azure. 

1. À partir du portail Azure, ouvrez le groupe de ressources **Test-FW-RG**.
2. Cliquez sur l’interface réseau de la machine virtuelle **Srv-Work**.
3. Sous **Paramètres**, cliquez sur **Serveurs DNS**.
4. Sous **Serveurs DNS**, cliquez sur **Personnaliser**.
5. Entrez **209.244.0.3** dans la zone de texte **Ajouter un serveur DNS**, et **209.244.0.4** dans la zone de texte suivante.
6. Cliquez sur **Enregistrer**. 
7. Redémarrez la machine virtuelle **Srv-Work**.


## <a name="test-the-firewall"></a>Tester le pare-feu

1. À partir du portail Azure, passez en revue les paramètres réseau de la machine virtuelle **Srv-Work** et notez l’adresse IP privée.
2. Connectez un bureau à distance à la machine virtuelle **Srv-Jump**, et à partir de là, ouvrez une connexion Bureau à distance à l’adresse IP privée **Srv-Work**.

5. Ouvrez Internet Explorer et accédez à http://github.com.
6. Cliquez sur **OK** et **Fermer** sur les alertes de sécurité.

   La page d’accueil GitHub doit s’afficher.

7. Accédez à http://www.msn.com.

   Vous devriez être bloqué par le pare-feu.

Maintenant que vous avez vérifié que les règles de pare-feu fonctionnent :

- Vous pouvez accéder au nom de domaine complet autorisé, mais pas à d’autres.
- Vous pouvez résoudre les noms DNS à l’aide du serveur DNS externe configuré.

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources **Test-FW-RG** pour supprimer toutes les ressources associées au pare-feu.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Configurer le réseau
> * Créer un pare-feu
> * Créer un itinéraire par défaut
> * Configurer les règles de réseau et d’application du pare-feu
> * Tester le pare-feu

Ensuite, vous pouvez surveiller les journaux de Pare-feu Azure.

> [!div class="nextstepaction"]
> [Tutoriel : Surveiller les journaux de Pare-feu Azure](./tutorial-diagnostics.md)
