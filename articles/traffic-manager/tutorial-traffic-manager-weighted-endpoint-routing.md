---
title: 'Didacticiel : Acheminer le trafic vers des points de terminaison pondérés à l’aide d’Azure Traffic Manager | Microsoft Docs'
description: Ce didacticiel explique comment acheminer le trafic vers des points de terminaison pondérés à l’aide de Traffic Manager.
services: traffic-manager
author: KumudD
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: kumud
ms.openlocfilehash: 171a9cfe5b5b63efde31c37cd49ef548f1e4ebcc
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649432"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-using-traffic-manager"></a>Didacticiel : Contrôler l’acheminement du trafic avec des points de terminaison pondérés à l’aide de Traffic Manager 

Ce didacticiel explique comment utiliser Traffic Manager pour contrôler l’acheminement du trafic utilisateur entre des points de terminaison à l’aide de la méthode de routage pondéré. Dans cette méthode de routage, vous devez affecter des coefficients de pondération à chaque point de terminaison figurant dans la configuration du profil Traffic Manager. Le trafic utilisateur sera alors acheminé en fonction du coefficient de pondération attribué à chaque point de terminaison. La pondération est un entier compris entre 1 et 1000. Plus la valeur de pondération affectée à un point de terminaison est élevée, plus celui-ci est prioritaire.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer deux machines virtuelles exécutant un site web de base sur IIS
> * Créer deux machines virtuelles de test pour afficher Traffic Manager en action
> * Configurer le nom DNS des machines virtuelles exécutant IIS
> * Créer un profil Traffic Manager
> * Ajouter des points de terminaison de machine virtuelle dans le profil Traffic Manager
> * Afficher Traffic Manager en action

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis
Pour afficher Traffic Manager en action, ce didacticiel requiert que vous déployiez les éléments suivants :
- Deux instances de sites web de base exécutées dans différentes régions Azure : **USA Est** et **Europe Ouest**.
- Deux machines virtuelles de test pour tester Traffic Manager : une machine virtuelle dans la région **USA Est** et la seconde dans la région **Europe Ouest**. Les machines virtuelles de test servent à illustrer la manière dont Traffic Manager achemine le trafic utilisateur vers un site web dont le point de terminaison s’est vu affecter la valeur de pondération la plus élevée.

### <a name="sign-in-to-azure"></a>Connexion à Azure 

Connectez-vous au portail Azure sur https://portal.azure.com.

### <a name="create-websites"></a>Créer des sites web

Dans cette section, vous allez créer deux instances de site web qui fournissent les deux points de terminaison de service pour le profil Traffic Manager dans deux régions Azure. Pour créer les deux sites web, exécutez les étapes suivantes :
1. La création de deux machines virtuelles pour l’exécution d’un site web de base, l’une située dans la région **USA Est** et l’autre dans la région **Europe Ouest**.
2. L’installation d’un serveur IIS sur chaque machine virtuelle, et la mise à jour de la page de site web par défaut qui décrit le nom de la machine virtuelle à laquelle un utilisateur est connecté lorsqu’il visite le site web.

#### <a name="create-vms-for-running-websites"></a>Créer des machines virtuelles pour exécuter des sites web
Dans cette section, vous allez créer deux machines virtuelles *myIISVMEastUS* et *myIISVMWEurope* dans les régions Azure **USA Est** et **Europe Ouest**.

1. En haut à gauche du portail Azure, sélectionnez **Créer une ressource** > **Compute** > **Machine virtuelle Windows Server 2016**.
2. Entrez ou sélectionnez les informations suivantes pour **De base**, acceptez les valeurs par défaut pour les autres paramètres, puis choisissez **Créer** :

    |Paramètre|Valeur|
    |---|---|
    |NOM|myIISVMEastUS|
    |Nom d'utilisateur| Entrez un nom d’utilisateur de votre choix.|
    |Mot de passe| Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Groupe de ressources| Sélectionnez **Nouveau**, puis tapez *myResourceGroupTM1*.|
    |Lieu| Sélectionnez **USA Est**.|
    |||
4. Sélectionnez une taille de machine virtuelle sous **Choisir une taille**.
5. Dans **Paramètres**, sélectionnez les valeurs suivantes, puis sélectionnez **OK** :
    
    |Paramètre|Valeur|
    |---|---|
    |Réseau virtuel| Sélectionnez **Réseau virtuel**, puis, dans **Créer un réseau virtuel**, pour le champ **Nom**, entrez *myVNet1*, et pour le sous-réseau, entrez *mySubnet*.|
    |Groupe de sécurité réseau|Sélectionnez **De base**, puis, dans la liste déroulante **Sélectionner des ports d’entrée publics**, sélectionnez **HTTP** et **RDP**. |
    |Diagnostics de démarrage|Sélectionnez **Désactivé**.|
    |||
6. Sous **Créer** dans **Résumé**, sélectionnez **Créer** pour démarrer le déploiement de la machine virtuelle.

7. Effectuez à nouveau les étapes 1 à 6, avec les modifications suivantes :

    |Paramètre|Valeur|
    |---|---|
    |Groupe de ressources | Sélectionnez **Nouveau**, puis tapez *myResourceGroupTM2*|
    |Lieu|Europe Ouest|
    |Nom de la machine virtuelle | myIISVMWEurope|
    |Réseau virtuel | Sélectionnez **Réseau virtuel**, puis, dans **Créer un réseau virtuel**, pour le champ **Nom**, entrez *myVNet2*, et pour le sous-réseau, entrez *mySubnet*.|
    |||
8. La création des machines virtuelles peut prendre plusieurs minutes. Attendez que les deux machines virtuelles aient été créées avant de passer aux étapes restantes.

![Créer une machine virtuelle](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Installer IIS et personnaliser la page web par défaut

Dans cette section, vous allez installer le serveur IIS sur les deux machines virtuelles, *myIISVMEastUS*  & *myIISVMWEurope*, puis mettre à jour la page de site web par défaut. La page de site web personnalisée affiche le nom de la machine virtuelle à laquelle vous êtes connecté lorsque vous visitez le site web à partir d’un navigateur web.

1. Sélectionnez **Toutes les ressources** dans le menu de gauche, puis, dans la liste de ressources, cliquez sur *myIISVMEastUS* qui se trouve dans le groupe de ressources *myResourceGroupTM1*.
2. Dans la page **Vue d’ensemble**, cliquez sur **Se connecter**, puis sur **Se connecter à la machine virtuelle**, et sélectionnez **Télécharger le fichier RDP**. 
3. Ouvrez le fichier .rdp téléchargé. Si vous y êtes invité, sélectionnez **Connexion**. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle. Vous devrez peut-être sélectionner **Plus de choix**, puis **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle. 
4. Sélectionnez **OK**.
5. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez l’avertissement, sélectionnez **Oui** ou **Continuer** pour poursuivre le processus de connexion.
6. Sur le bureau du serveur, accédez à **Outils d’administration Windows**>**Gestionnaire de serveur**.
7. Lancez Windows PowerShell sur VM1 et utilisez les commandes suivantes pour installer le serveur IIS et mettre à jour le fichier htm par défaut.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![Installer IIS et personnaliser la page web](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. Fermez la connexion RDP établie avec la machine virtuelle *myIISVMEastUS*.
9. Répétez les étapes 1 à 8 en créant une connexion RDP avec la machine virtuelle *myIISVMWEurope* au sein du groupe de ressources *myResourceGroupTM2* pour installer IIS et personnaliser sa page web par défaut.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Configurer les noms DNS des machines virtuelles exécutant IIS

Traffic Manager achemine le trafic utilisateur en fonction du nom DNS des points de terminaison de service. Dans cette section, vous allez configurer les noms DNS des serveurs IIS : *myIISVMEastUS* et *myIISVMWEurope*.

1. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis, dans la liste de ressources, sélectionnez *myIISVMEastUS* qui se trouve dans le groupe de ressources *myResourceGroupTM1*.
2. Dans la page **Vue d’ensemble**, sous **Nom DNS**, sélectionnez **Configurer**.
3. Dans la page **Configuration**, sous l’étiquette du nom DNS, ajoutez un nom unique, puis sélectionnez **Enregistrer**.
4. Répétez les étapes 1 à 3 pour la machine virtuelle nommée *myIISVMWEurope* qui se trouve dans le groupe de ressources *myResourceGroupTM1*.

### <a name="create-a-test-vm"></a>Créer une machine virtuelle de test

Dans cette section, vous allez créer une machine virtuelle appelée *mVMEastUS*. Vous allez utiliser cette machine virtuelle pour tester la manière dont Traffic Manager achemine le trafic vers le point de terminaison du site web à qui est affectée la valeur de pondération la plus élevée.

1. En haut à gauche du portail Azure, sélectionnez **Créer une ressource** > **Compute** > **Machine virtuelle Windows Server 2016**.
2. Entrez ou sélectionnez les informations suivantes pour **De base**, acceptez les valeurs par défaut pour les autres paramètres, puis choisissez **Créer** :

    |Paramètre|Valeur|
    |---|---|
    |NOM|myVMEastUS|
    |Nom d'utilisateur| Entrez un nom d’utilisateur de votre choix.|
    |Mot de passe| Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Groupe de ressources| Sélectionnez **Existant**, puis *myResourceGroupTM1*.|
    |||

4. Sélectionnez une taille de machine virtuelle sous **Choisir une taille**.
5. Dans **Paramètres**, sélectionnez les valeurs suivantes, puis sélectionnez **OK** :
    |Paramètre|Valeur|
    |---|---|
    |Réseau virtuel| Sélectionnez **Réseau virtuel**, puis, dans **Créer un réseau virtuel**, pour le champ **Nom**, entrez *myVNet3*, et pour le sous-réseau, entrez *mySubnet*.|
    |Groupe de sécurité réseau|Sélectionnez **De base**, puis, dans la liste déroulante **Sélectionner des ports d’entrée publics**, sélectionnez **HTTP** et **RDP**. |
    |Diagnostics de démarrage|Sélectionnez **Désactivé**.|
    |||

6. Sous **Créer** dans **Résumé**, sélectionnez **Créer** pour démarrer le déploiement de la machine virtuelle.
8. La création de la machine virtuelle ne nécessite que quelques minutes. Attendez que la machine virtuelle ait été créée avant de passer aux étapes restantes.

## <a name="create-a-traffic-manager-profile"></a>Créer un profil Traffic Manager
Créez un profil Traffic Manager basé sur la méthode de routage **Pondéré**.

1. Dans l’angle supérieur gauche de l’écran, cliquez sur **Créer une ressource** > **Mise en réseau** > **Profil Traffic Manager** > **Créer**.
2. Dans **Créer un profil Traffic Manager**, entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis choisissez **Créer** :

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | NOM                   | Ce nom doit être unique au sein de la zone trafficmanager.net et affiche le nom DNS, trafficmanager.net, qui est utilisé pour accéder à votre profil Traffic Manager.                                   |
    | Méthode de routage          | Sélectionnez la méthode de routage **Pondéré**.                                       |
    | Abonnement            | Sélectionnez votre abonnement.                          |
    | Groupe de ressources          | Sélectionnez **Existant**, puis *myResourceGroupTM1*. |
    |        |   |

    ![Créer un profil Traffic Manager](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Ajouter des points de terminaison Traffic Manager

Ajoutez les deux machines virtuelles exécutant les serveurs IIS, *myIISVMEastUS*  & *myIISVMWEurope*, pour y acheminer le trafic utilisateur.

1. Dans la barre de recherche du portail, recherchez le nom du profil Traffic Manager que vous avez créé dans la section précédente et sélectionnez le profil dans les résultats affichés.
2. Dans **Profil Traffic Manager**, dans la section **Paramètres**, cliquez sur **Points de terminaison**, puis sur **Ajouter**.
3. Entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **OK** :

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | type                    | Point de terminaison Azure                                   |
    | NOM           | myEastUSEndpoint                                        |
    | Type de ressource cible           | Adresse IP publique                          |
    | Ressource cible          | Sélectionnez **Choisir une adresse IP publique** pour afficher la liste des ressources pourvues d’adresses IP publiques dans le même abonnement. Dans **Ressource**, sélectionnez l’adresse IP publique nommée *myIISVMEastUS-ip*. Il s’agit de l’adresse IP publique de la machine virtuelle serveur IIS qui se trouve dans la région USA Est.|
    |  Poids      | Entrez **100**.        |
    |        |           |

4. Répétez les étapes 2 et 3 pour ajouter un autre point de terminaison nommé *myWestEuropeEndpoint* pour l’adresse IP publique *myIISVMWEurope-ip* qui est associée à la machine virtuelle serveur IIS nommée *myIISVMWEurope* , puis entrez **25** comme **Pondération**. 
5.  Lorsque l’ajout de deux points de terminaison est terminé, ceux-ci s’affichent dans **Profil Traffic Manager** avec leur état de surveillance défini sur **En ligne**.

## <a name="test-traffic-manager-profile"></a>Tester le profil Traffic Manager
Pour afficher Traffic Manager en action, procédez comme suit :
1. Déterminez le nom DNS de votre profil Traffic Manager.
2. Visualisez Traffic Manager en action.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Déterminer le nom DNS du profil Traffic Manager
Dans ce didacticiel, par souci de simplicité, vous utilisez le nom DNS du profil Traffic Manager pour visiter les sites web. 

Vous pouvez déterminer le nom DNS du profil Traffic Manager en procédant comme suit :

1.  Dans la barre de recherche du portail, recherchez le nom du **profil Traffic Manager** que vous avez créé dans la section précédente. Dans les résultats affichés, cliquez sur le profil Traffic Manager.
1. Cliquez sur **Overview**.
2. Le **profil Traffic Manager** affiche le nom DNS de votre profil Traffic Manager nouvellement créé. Dans les déploiements en environnements de production, vous configurez un nom de domaine personnalisé pour qu’il pointe vers le nom de domaine Traffic Manager, à l’aide d’un enregistrement DNS CNAME.

   ![Nom DNS Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Afficher Traffic Manager en action
Dans cette section, vous pouvez voir Traffic Manager en action. 

1. Sélectionnez **Toutes les ressources** dans le menu de gauche, puis, dans la liste de ressources, cliquez sur *myVMEastUS* qui se trouve dans le groupe de ressources *myResourceGroupTM1*.
2. Dans la page **Vue d’ensemble**, cliquez sur **Se connecter**, puis sur **Se connecter à la machine virtuelle**, et sélectionnez **Télécharger le fichier RDP**. 
3. Ouvrez le fichier .rdp téléchargé. Si vous y êtes invité, sélectionnez **Connexion**. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle. Vous devrez peut-être sélectionner **Plus de choix**, puis **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle. 
4. Sélectionnez **OK**.
5. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez l’avertissement, sélectionnez **Oui** ou **Continuer** pour poursuivre le processus de connexion. 
6. Dans un navigateur web sur la machine virtuelle *myVMEastUS*, tapez le nom DNS de votre profil Traffic Manager pour afficher votre site web. Vous êtes redirigé vers le site web hébergé sur le serveur IIS *myIISVMEastUS* dans la mesure où il est affecté à une valeur de pondération plus élevée (**100**) que celle du serveur IIS *myIISVMWEurope* (dont le point de terminaison est affecté à une valeur de pondération de **25**).

   ![Tester le profil Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)
   
## <a name="delete-the-traffic-manager-profile"></a>Supprimer le profil Traffic Manager
Lorsque vous n’en avez plus besoin, supprimez les groupes de ressources que vous avez créés au cours de ce tutoriel. Pour ce faire, sélectionnez le groupe de ressources (**ResourceGroupTM1** ou **ResourceGroupTM2**), puis **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Route traffic to specific endpoints based on user’s geographic location](traffic-manager-configure-geographic-routing-method.md) (Acheminer le trafic vers des points de terminaison spécifiques en fonction de l’emplacement géographique de l’utilisateur)


