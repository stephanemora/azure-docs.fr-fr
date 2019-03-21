---
title: Créer un environnement avec un cluster Service Fabric dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment créer un environnement avec un cluster Service Fabric autonome et démarrer et arrêter le cluster à l’aide de planifications.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: enewman
ms.openlocfilehash: 9848f197800c391285c4065685b910685f0ac64b
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57319064"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Créer un environnement avec un cluster autonome Service Fabric dans Azure DevTest Labs
Cet article fournit des informations sur la façon de créer un environnement avec un cluster Service Fabric autonome dans Azure DevTest Labs. 

## <a name="overview"></a>Présentation
DevTest Labs peuvent créer des environnements de test autonome tel que défini par les modèles Azure Resource Manager. Ces environnements contiennent les deux ressources IaaS, comme les machines virtuelles et les ressources PaaS, comme Service Fabric. DevTest Labs vous permet de gérer les machines virtuelles dans un environnement en fournissant des commandes permettant de contrôler les machines virtuelles. Ces commandes vous permettent de démarrer ou arrêter une machine virtuelle sur une planification. De même, DevTest Labs peut également vous aider à gérer des clusters Service Fabric dans un environnement. Vous pouvez démarrer ou arrêter un cluster Service Fabric dans un environnement manuellement ou via une planification.

## <a name="create-a-service-fabric-cluster"></a>Créer un cluster Service Fabric
Clusters service Fabric sont créés à l’aide d’environnements dans DevTest Labs. Chaque environnement est définie par un modèle Azure Resource Manager dans un référentiel Git. Le [référentiel Git public](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) pour dev/test contient le modèle Resource Manager pour créer un cluster Service Fabric dans le [service Fabric-Cluster](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) dossier. 

1. Commencez par créer un laboratoire dans Azure DevTest Labs à l’aide des instructions dans l’article suivant : [Créer un laboratoire](devtest-lab-create-lab.md). Notez que le **des environnements publics** option est **sur** par défaut. 
2. Vérifiez que le fournisseur de Service Fabric est inscrit pour votre abonnement en suivant ces étapes :
    1. Sélectionnez **abonnements** sur le menu de navigation gauche, puis sélectionnez votre **abonnement**
    2. Sur le **abonnement** page, sélectionnez **fournisseurs de ressources** dans le **paramètres** section dans le menu de gauche. 
    3. Si **Microsoft.ServiecFabric** n’est pas inscrit, sélectionnez **inscrire**. 
3. Dans la page **DevTest Lab** pour votre labo, sélectionnez **+ Ajouter** dans la barre d’outils. 
    
    ![Ajouter un bouton sur la barre d’outils](./media/create-environment-service-fabric-cluster/add-button.png)
3. Sur le **choisir une base** page, sélectionnez **Cluster Service Fabric Lab** dans la liste. 

    ![Sélectionnez le Cluster Service Fabric Lab dans la liste](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Sur le **configurer les paramètres** , effectuez les étapes suivantes : 
    1. Spécifiez un **nom** pour votre cluster **environnement**. Il s’agit du nom du groupe de ressources dans Azure dans lequel le cluster Service Fabric va être créé. 
    2. Sélectionnez le **système d’exploitation (se)** pour les machines virtuelles de cluster. La valeur par défaut est : **Windows**.
    3. Spécifiez un nom pour le **administrateur** pour le cluster. 
    4. Spécifiez un **mot de passe** pour l’administrateur. 
    5. Pour le **certificat**, entrez vos informations de certificat sous forme de chaîne codée en Base64. Pour créer un certificat, procédez comme suit :
        1. Téléchargez le **ClusterCertificate.ps1 de créer** de fichiers à partir de la [référentiel Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). Vous pouvez également cloner le référentiel sur votre ordinateur. 
        2. Lancez **PowerShell**. 
        3. Exécutez le **ps1** fichier à l’aide de la commande `.\Create-ClusterCertificate.ps1`. Vous consultez un fichier texte ouvert dans le bloc-notes avec les informations que nécessaires pour remplir les champs de certificat sur cette page. . 
    6. Entrez le **mot de passe pour le certificat**.
    7. Spécifiez le **empreinte** pour votre certificat.
    8. Sélectionnez **ajouter** sur le **configurer les paramètres** page. 

        ![Configurer les paramètres de cluster](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Une fois que le cluster est créé, vous consultez un groupe de ressources portant le nom de l’environnement que vous avez fourni à l’étape précédente. Lorsque vous développez, vous consultez le cluster Service Fabric qu’il contient. Si l’état du groupe de ressources est bloqué au niveau **création**, sélectionnez **Actualiser** sur la barre d’outils. Le **cluster Service Fabric** environment crée un cluster à 5 nœuds 1-nodetype sur Linux ou Windows.

    Dans l’exemple suivant, **mysfabricclusterrg** est le nom du groupe de ressources qui est créé spécifiquement pour le cluster Service Fabric. Il est important de noter que les environnements lab sont autonomes dans le groupe de ressources dans lequel ils sont créés. Cela signifie que le modèle qui définit l’environnement, qui peut accéder uniquement aux ressources au sein du groupe de ressources nouvellement créé ou [des réseaux virtuels configurés pour être utilisés par le laboratoire](devtest-lab-configure-vnet.md). Cet exemple ci-dessus crée toutes les ressources requises dans le même groupe de ressources.

    ![Cluster créé](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Démarrer ou arrêter le cluster
Vous pouvez démarrer ou arrêter le cluster à partir de la page dev/Test Lab lui-même ou de la page de Cluster Service Fabric fourni par dev/test. 

### <a name="from-the-devtest-lab-page"></a>À partir de la page de dev/Test Lab
Vous pouvez démarrer ou arrêter le cluster sur la page de dev/Test Lab pour votre laboratoire. 

1. Sélectionnez **points de suspension (...)**  pour le cluster Service Fabric, comme illustré dans l’image suivante : 

    ![Démarrer et arrêter des commandes pour le cluster](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Vous voyez deux commandes dans le menu contextuel pour **Démarrer** et **arrêter** le cluster. La commande start démarre tous les nœuds dans un cluster. La commande stop arrête tous les nœuds dans un cluster. Une fois un cluster est arrêté, le cluster Service Fabric reste dans un état prêt, mais aucun nœud n’est disponibles jusqu'à ce que la commande de démarrage est réémise sur le cluster dans le laboratoire.

    Il existe quelques points à noter lorsque vous utilisez un cluster Service Fabric dans un environnement de test. Il peut prendre un certain temps pour le cluster Service Fabric rafraîchir entièrement une fois que les nœuds ont été redémarrés. Pour conserver les données à partir de l’arrêt du démarrage, les données doivent être sauvegardées sur un disque managé est attaché à la machine virtuelle. Il existe des conséquences sur les performances lors de l’utilisation d’un disque géré connecté, il est donc recommandé pour les environnements de test uniquement. Si le disque utilisé pour le stockage de données n’est pas sauvegardé, données sont perdues lors de la commande d’arrêt est émise sur le cluster.

### <a name="from-the-service-fabric-cluster-page"></a>À partir de la page de Cluster Service Fabric 
Une autre méthode consiste à démarrer ou arrêter le cluster. 

1. Dans l’arborescence sur la page de dev/Test Lab, sélectionnez votre cluster Service Fabric. 

    ![Sélectionnez votre cluster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Sur le **Cluster Service Fabric** page pour le cluster, vous consultez les commandes sur la barre d’outils pour démarrer ou arrêter le cluster. 

    ![Démarrer ou arrêter des commandes dans la page de Cluster Service Fabric](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Configurer le démarrage automatique et planification d’arrêt automatique
Clusters service Fabric peuvent également être démarrés ou arrêtés selon une planification. Cette expérience est similaire à l’expérience des machines virtuelles dans un laboratoire. Pour réaliser des économies, par défaut, chaque cluster créé automatiquement dans un laboratoire s’est arrêtée au moment défini par le laboratoire [stratégie d’arrêt](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown). Vous pouvez remplacer en spécifiant si le cluster doit être arrêté ou en spécifiant le temps que le cluster est arrêté. 

![Planifications existantes pour le démarrage automatique et d’arrêt automatique](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>S’abonner à la planification du démarrage automatique
Pour vous abonner à la planification de démarrage, procédez comme suit :

1. Sélectionnez **démarrage automatique** sur le menu de gauche
2. Sélectionnez **sur** pour **autoriser ce cluster service fabric est planifié pour le démarrage automatique**. Cette page est activée uniquement si le propriétaire de laboratoire a autorisé les utilisateurs de démarrer automatiquement leurs ordinateurs virtuels ou les clusters Service Fabric.
3. Sélectionnez **Enregistrer** dans la barre d’outils. 

    ![Page en étoile automatiques](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Configurer les paramètres d’arrêt automatique 
Pour modifier les paramètres d’arrêt, procédez comme suit :

1. Sélectionnez **arrêt automatique** sur le menu de gauche. 
2. Dans cette page, vous pouvez refuser l’arrêt automatique en sélectionnant **hors** pour **activé**. 
3. Si vous avez sélectionné **sur** pour **activé**, procédez comme suit :
    1. Spécifiez le **temps** d’arrêt.
    2. Spécifiez le **fuseau horaire** pour le moment. 
    3. Spécifiez si vous souhaitez dev/test pour envoyer **notifications** avant l’arrêt automatique. 
    4. Si vous avez sélectionné **Oui** pour l’option de notification, spécifiez la **URL du Webhook** et/ou **adresse de messagerie** pour envoyer des notifications. 
    5. Sélectionnez **Enregistrer** dans la barre d’outils.

        ![Auto arrêter de page](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Pour afficher les nœuds du cluster Service Fabric
La page du cluster Service Fabric que vous avez vu précédemment dans la procédure est spécifique à la page de dev/test. Il ne vous montre les nœuds du cluster. Pour afficher plus d’informations sur le cluster, procédez comme suit :

1. Sur le **dev/Test Lab** page pour votre laboratoire, sélectionnez le **groupe de ressources** dans l’arborescence dans le **mes machines virtuelles** section.

    ![Sélection du groupe de ressources](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Sur le **groupe de ressources** page, vous voyez toutes les ressources du groupe de ressources dans une liste. Sélectionnez votre **cluster Service Fabric** dans la liste. 

    ![Sélectionnez votre cluster dans la liste](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Vous voyez la **Cluster Service Fabric** page de votre cluster. Il s’agit de la page qui fournit le Service Fabric. Affiche toutes les informations sur les clusters de nœuds, les types de nœuds, etc.

    ![Page d’accueil de Cluster service Fabric](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants pour plus d’informations sur les environnements : 

- [Créer des environnements de plusieurs machines virtuelles et des ressources PaaS avec les modèles Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurer et utiliser des environnements publics dans Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
