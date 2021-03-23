---
title: 'Démarrage rapide : Créer une machine virtuelle Windows dans le portail Azure'
description: Dans ce guide de démarrage rapide, vous allez apprendre à utiliser le Portail Azure pour créer une machine virtuelle Windows.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 03/15/2021
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0ba28d003f359af12de6242c6d2444fb8adab0d7
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103562752"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Démarrage rapide : Créer une machine virtuelle Windows dans le portail Azure

Le Portail Azure peut être utilisé pour créer des machines virtuelles Azure. Cette méthode fournit une interface utilisateur sur navigateur permettant de créer les machines virtuelles et les ressources associées. Ce guide de démarrage rapide explique comment utiliser le portail Azure pour déployer dans Azure une machine virtuelle qui fonctionne avec Windows Server 2019. Pour voir votre machine virtuelle en action, vous établirez une connexion RDP à la machine virtuelle et installerez le serveur web IIS.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-virtual-machine"></a>Créer une machine virtuelle

1. Tapez **machines virtuelles** dans la zone de recherche.
1. Sous **Services**, sélectionnez **Machines virtuelles**.
1. Sur la page **Machines virtuelles**, sélectionnez **Ajouter**, puis **Machine virtuelle**. 
1. Sous l’onglet **De base**, sous **Détails du projet**, vérifiez que l’abonnement approprié est sélectionné, puis choisissez **Créer** pour créer un groupe de ressources. Tapez *myResourceGroup* comme nom. 

    ![Capture d’écran de la section Détails du projet montrant où vous sélectionnez l’abonnement Azure et le groupe de ressources pour la machine virtuelle](./media/quick-create-portal/project-details.png)

1. Sous **Détails de l’instance**, tapez *myVM* comme **Nom de la machine virtuelle**, puis choisissez *USA Est* comme **Région**. Choisissez *Windows Server 2019 Datacenter* pour l’**Image** et *Standard_DS1_v2* pour la **Taille**. Conservez les autres valeurs par défaut.

    ![Capture d’écran de la section Détails de l’instance où vous spécifiez un nom pour la machine virtuelle et où vous sélectionnez sa région, son image et sa taille](./media/quick-create-portal/instance-details.png)

1. Sous **Compte d’administrateur**, indiquez un nom d’utilisateur (par exemple, *azureuser*) et un mot de passe. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Capture d’écran de la section Compte d’administrateur où vous fournissez le nom d’utilisateur et le mot de passe de l’administrateur](./media/quick-create-portal/administrator-account.png)

1. Sous **Règles des ports d’entrée**, choisissez **Autoriser les ports sélectionnés**, puis sélectionnez **RDP (3389)** et **HTTP (80)** dans la liste déroulante.

    ![Capture d’écran de la section Règles des ports d’entrée où vous sélectionnez les ports sur lesquels les connexions entrantes sont autorisées](./media/quick-create-portal/inbound-port-rules.png)

1. Conservez les valeurs par défaut restantes, puis sélectionnez le bouton **Vérifier + créer** en bas de la page.

    ![Capture d’écran montrant le bouton Vérifier + créer en bas de la page](./media/quick-create-portal/review-create.png)

1. Une fois la validation exécutez, sélectionnez le bouton **Créer** en bas de la page.

1. Une fois le déploiement effectué, sélectionnez **Accéder à la ressource**.

    ![Capture d’écran montrant l’étape suivante d’accès à la ressource](./media/quick-create-portal/next-steps.png)

## <a name="connect-to-virtual-machine"></a>Connexion à la machine virtuelle

Créez une connexion Bureau à distance à la machine virtuelle. Ces instructions expliquent comment se connecter à la machine virtuelle à partir d’un ordinateur Windows. Sur un Mac, vous avez besoin d’un client RDP similaire à ce [Client Bureau à distance](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) disponible sur le Mac App Store.

1. Sélectionnez le bouton **Se connecter** sur la page de vue d’ensemble de votre machine virtuelle, puis **RDP**. 

    ![Capture d’écran de la page de vue d’ensemble de la machine virtuelle montrant l’emplacement du bouton Se connecter](./media/quick-create-portal/portal-quick-start-9.png)
    
2. Sur la page **Se connecter avec RDP**, conservez les options par défaut pour vous connecter par adresse IP sur le port 3389 et cliquez sur **Télécharger le fichier RDP**.

2. Ouvrez le fichier RDP téléchargé et, à l’invite, cliquez sur **Se connecter**. 

3. Dans la fenêtre **Sécurité Windows**, sélectionnez **Plus de choix**, puis **Utiliser un autre compte**. Tapez le nom d’utilisateur sous la forme **localhost**\\*username*, entrez le mot de passe créé pour la machine virtuelle, puis cliquez sur **OK**.

4. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Cliquez sur **Oui** ou **Continuer** pour créer la connexion.

## <a name="install-web-server"></a>Installer le serveur web

Pour voir votre machine virtuelle en action, installez le serveur web IIS. Ouvrez une invite PowerShell sur la machine virtuelle et exécutez la commande suivante :

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Une fois terminé, fermez la connexion RDP à la machine virtuelle.


## <a name="view-the-iis-welcome-page"></a>Afficher la page d’accueil IIS

Dans le portail, sélectionnez la machine virtuelle puis, dans la vue d’ensemble de la machine virtuelle, pointez sur l’adresse IP pour afficher **Copier dans le presse-papiers**. Copiez l’adresse IP et collez-la dans un onglet de navigateur. La page d’accueil IIS par défaut s’ouvre et devrait ressembler à ceci :

![Capture d’écran du site IIS par défaut dans un navigateur](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Dès que vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées. 

Accédez au groupe de ressources de la machine virtuelle, puis sélectionnez **Supprimer le groupe de ressources**. Confirmez le nom du groupe de ressources pour terminer la suppression des ressources.

## <a name="next-steps"></a>Étapes suivantes

Avec ce guide de démarrage rapide, vous avez déployé une machine virtuelle simple, ouvert un port réseau pour le trafic web et installé un serveur web de base. Pour en savoir plus sur les machines virtuelles Azure, suivez le didacticiel pour les machines virtuelles Windows.

> [!div class="nextstepaction"]
> [Didacticiels sur les machines virtuelles Azure Windows](./tutorial-manage-vm.md)
