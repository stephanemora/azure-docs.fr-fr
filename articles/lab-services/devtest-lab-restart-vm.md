---
title: Redémarrer une machine virtuelle dans un laboratoire dans Azure DevTest Labs | Microsoft Docs
description: Cet article décrit les étapes permettant de redémarrer rapidement et facilement des machines virtuelles dans Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 52d3b92909483a99eb82c86b727261bbeb5f8d46
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759992"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Redémarrer une machine virtuelle dans un laboratoire dans Azure DevTest Labs
Vous pouvez rapidement et facilement redémarrer une machine virtuelle dans DevTest Labs en suivant les étapes décrites dans cet article. Prenez en considération les éléments suivants avant de redémarrer une machine virtuelle :

- La machine virtuelle doit être en cours d’exécution pour que la fonctionnalité de redémarrage soit activée.
- Si l’utilisateur est connecté à une machine virtuelle en cours d’exécution lorsqu’il effectue un redémarrage, il doit se reconnecter à la machine virtuelle une fois la sauvegarde démarrée.
- Si un artefact est en cours d’application lors du redémarrage de la machine virtuelle, vous recevez un avertissement indiquant que l’artefact ne sera peut-être pas appliqué.

    ![Avertissement lors du redémarrage en cas d’application d’artefacts en cours](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Si la machine virtuelle est bloquée lors de l’application d’un artefact, vous pouvez utiliser la fonctionnalité de redémarrage de la machine virtuelle afin de tenter de résoudre le problème.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Étapes de redémarrage d’une machine virtuelle dans un laboratoire dans Azure DevTest Labs
1. Connectez-vous au [portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
1. Dans la liste des laboratoires, sélectionnez celui dans lequel se trouve la machine virtuelle que vous souhaitez redémarrer.
1. Dans le panneau de gauche, sélectionnez **Mes machines virtuelles**.
1. Dans la liste des machines virtuelles, sélectionnez une machine virtuelle en cours d’exécution.
1. En haut du volet de gestion des machines virtuelles, sélectionnez **Redémarrer**.

    ![Bouton de redémarrage de la machine virtuelle](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Surveillez l’état du redémarrage en cliquant sur l’icône **Notifications** en haut à droite de la fenêtre.

    ![Affichage de l’état de redémarrage de la machine virtuelle](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Vous pouvez également redémarrer une machine virtuelle en cours d’exécution en cliquant sur les points de suspension (...) dans la liste **Mes machines virtuelles**.

![Redémarrage de la machine virtuelle via les points de suspension](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Étapes suivantes
* Une fois le redémarrage effectué, vous pouvez vous reconnecter à la machine virtuelle en sélectionnant **Se connecter** dans son volet de gestion.
* Explorez la [Galerie de modèles de démarrage rapide d’Azure Resource Manager DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
