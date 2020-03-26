---
title: 'Démarrage rapide : Créer une machine virtuelle Windows dans le portail Azure'
description: Dans ce guide de démarrage rapide, vous allez apprendre à utiliser le Portail Azure pour créer une machine virtuelle Windows.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 002d374f5be606688121ef4a3952383567c43e85
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79214747"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Démarrage rapide : Créer une machine virtuelle Windows dans le portail Azure

Le Portail Azure peut être utilisé pour créer des machines virtuelles Azure. Cette méthode fournit une interface utilisateur sur navigateur permettant de créer les machines virtuelles et les ressources associées. Ce guide de démarrage rapide explique comment utiliser le portail Azure pour déployer dans Azure une machine virtuelle qui fonctionne avec Windows Server 2019. Pour voir votre machine virtuelle en action, vous établirez une connexion RDP à la machine virtuelle et installerez le serveur web IIS.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-virtual-machine"></a>Créer une machine virtuelle

1. Tapez **machines virtuelles** dans la zone de recherche.
1. Sous **Services**, sélectionnez **Machines virtuelles**.
1. Sur la page **Machines virtuelles**, sélectionnez **Ajouter**. 
1. Sous l’onglet **De base**, sous **Détails du projet**, vérifiez que l’abonnement approprié est sélectionné, puis choisissez **Créer** pour créer un groupe de ressources. Tapez *myResourceGroup* comme nom. 

    ![Créer un groupe de ressources pour votre machine virtuelle](./media/quick-create-portal/project-details.png)

1. Sous **Détails de l’instance**, tapez *myVM* comme **Nom de la machine virtuelle**, puis choisissez *USA Est* comme **Région** et *Centre de données Windows Server 2019* pour **Image**. Conservez les autres valeurs par défaut.

    ![Section Détails de l’instance](./media/quick-create-portal/instance-details.png)

1. Sous **Compte d’administrateur**, indiquez un nom d’utilisateur (par exemple, *azureuser*) et un mot de passe. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Entrez votre nom d’utilisateur et votre mot de passe.](./media/quick-create-portal/administrator-account.png)

1. Sous **Règles des ports d’entrée**, choisissez **Autoriser les ports sélectionnés**, puis sélectionnez **RDP (3389)** et **HTTP (80)** dans la liste déroulante.

    ![Ports ouverts pour RDP et HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Conservez les valeurs par défaut restantes, puis sélectionnez le bouton **Vérifier + créer** en bas de la page.

    ![Examiner et créer](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Connexion à la machine virtuelle

Créez une connexion Bureau à distance à la machine virtuelle. Ces instructions expliquent comment se connecter à la machine virtuelle à partir d’un ordinateur Windows. Sur un Mac, vous avez besoin d’un client RDP similaire à ce [Client Bureau à distance](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) disponible sur le Mac App Store.

1. Cliquez sur le bouton **Se connecter** sur la page de vue d’ensemble de votre machine virtuelle. 

    ![Se connecter à une machine virtuelle Azure à partir du portail](./media/quick-create-portal/portal-quick-start-9.png)
    
2. Sur la page **Se connecter à la machine virtuelle**, conservez les options par défaut pour vous connecter par adresse IP sur le port 3389 et cliquez sur **Télécharger le fichier RDP**.

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

Dans le portail, sélectionnez la machine virtuelle et dans la vue d’ensemble de la machine virtuelle, utilisez le bouton **Cliquer pour copier** à droite de l’adresse IP pour la copier-coller dans un onglet de navigateur. La page d’accueil IIS par défaut s’ouvre et devrait ressembler à ceci :

![Site IIS par défaut](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Dès que vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées. 

Sélectionnez le groupe de ressources de la machine virtuelle, puis sélectionnez **Supprimer**. Confirmez le nom du groupe de ressources pour terminer la suppression des ressources.

## <a name="next-steps"></a>Étapes suivantes

Avec ce guide de démarrage rapide, vous avez déployé une machine virtuelle simple, ouvert un port réseau pour le trafic web et installé un serveur web de base. Pour en savoir plus sur les machines virtuelles Azure, suivez le didacticiel pour les machines virtuelles Windows.

> [!div class="nextstepaction"]
> [Didacticiels sur les machines virtuelles Azure Windows](./tutorial-manage-vm.md)