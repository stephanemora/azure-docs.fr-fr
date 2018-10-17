---
title: Dans ce didacticiel, vous allez apprendre à vous abonner à une offre Azure Stack | Microsoft Docs
description: Ce didacticiel explique comment créer un nouvel abonnement dans les services Azure Stack et comment tester l’offre en créant une machine virtuelle de test.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/05/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: aff20cba3bc07924f669420a8c367613b41111a1
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49076098"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Didacticiel : créer et tester un abonnement
Ce didacticiel vous montre comment créer un abonnement qui contient une offre et comment la tester. Pour le test, vous allez vous connecter au portail utilisateur Azure Stack en tant qu’administrateur cloud, vous abonner à l’offre, puis créer une machine virtuelle.

> [!TIP]
> Pour une expérience d’évaluation d’expérience avancée, vous pouvez [créer un abonnement pour un utilisateur particulier](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator) puis vous connecter en tant que cet utilisateur au portail utilisateur. 

Dans ce didacticiel, vous allez apprendre à vous abonner à une offre Azure Stack.

Contenu :

> [!div class="checklist"]
> * S’abonner à une offre 
> * Tester l’offre

## <a name="subscribe-to-an-offer"></a>S’abonner à une offre
Pour vous abonner à une offre en tant qu’utilisateur, vous devez vous connecter au portail utilisateur Azure Stack pour découvrir les services offerts par l’opérateur Azure Stack.

1. Connectez-vous au portail utilisateur et cliquez sur **Obtenir un abonnement**.

   ![Prendre un abonnement](media/azure-stack-subscribe-services/get-subscription.png)

2. Dans le champ **Nom d’affichage** , tapez un nom pour votre abonnement. Ensuite, cliquez sur **Offre** pour sélectionner l’une des offres disponibles dans la section **Choisir une offre**, puis cliquez sur **Créer**.

   ![Créer une offre](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Vous devez maintenant actualiser le portail utilisateur pour commencer à utiliser votre abonnement.

3. Pour afficher l’abonnement que vous avez créé, cliquez sur **Tous les services**.  Ensuite, dans la catégorie **GÉNÉRAL**, sélectionnez **Abonnements**, puis sélectionnez votre nouvel abonnement. Une fois que vous êtes abonné à une offre, actualisez le portail pour voir si les nouveaux services ont été ajoutés à votre nouvel abonnement. Dan cet exemple, **Machines virtuelles** a été ajouté.

   ![Afficher l’abonnement](media/azure-stack-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>Tester l’offre
En étant connecté au portail utilisateur, vous pouvez tester l’offre en approvisionnant une machine virtuelle à l’aide des fonctionnalités du nouvel abonnement. 

> [!NOTE]
> Ce test nécessite d’avoir ajouté une machine virtuelle Windows Server 2016 Datacenter à la place de marché Azure Stack. 

1. Connectez-vous au portail utilisateur.

2. Dans le portail utilisateur, cliquez sur **Machines virtuelles** > **Ajouter** > **Windows Server 2016 Datacenter**, puis sur **Créer**.

3. Dans la section **Bases**, tapez un **Nom**, un **Nom d’utilisateur** et un **Mot de passe**, choisissez un **Abonnement**, créez un **Groupe de ressources** (ou sélectionnez un groupe existant), puis cliquez sur **OK**.

4. Dans la section **Choisir une taille**, cliquez sur **A1 Standard**, puis cliquez sur **Sélectionner**.  

5. Dans le panneau Paramètres, acceptez les valeurs par défaut et cliquez sur **OK**.

6. Dans la section **Résumé**, cliquez sur **OK** pour créer la machine virtuelle.  

7. Pour voir votre nouvelle machine virtuelle, cliquez sur **Machines virtuelles**, puis recherchez la nouvelle machine virtuelle et cliquez sur son nom.

    ![Toutes les ressources](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> Le déploiement de la machine virtuelle prend quelques minutes.


## <a name="next-steps"></a>Étapes suivantes

Ce que vous avez appris dans ce didacticiel :

> [!div class="checklist"]
> * S’abonner à une offre 
> * Tester l’offre


> [!div class="nextstepaction"]
> [Créer une machine virtuelle à partir d’un modèle de communauté](azure-stack-create-vm-template.md)