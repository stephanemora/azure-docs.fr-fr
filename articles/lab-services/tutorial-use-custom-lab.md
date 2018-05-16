---
title: Accéder à un laboratoire personnalisé dans Azure DevTest Labs | Microsoft Docs
description: Dans ce didacticiel, vous accédez au laboratoire créé à l’aide d’Azure DevTest Labs, revendiquez des machines virtuelles, vous les utilisez, puis vous cessez de les revendiquer.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/09/2018
ms.author: spelluru
ms.openlocfilehash: ce4522673bac56f73944413d102b7cb36cf93f30
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-access-a-custom-lab-in-azure-devtest-labs"></a>Didacticiel : accéder à un laboratoire personnalisé dans Azure DevTest Labs
Dans ce didacticiel, vous utilisez le laboratoire personnalisé créé dans le [didacticiel : création d’un atelier personnalisé](tutorial-create-custom-lab.md).

Dans ce didacticiel, vous effectuez les actions suivantes :

> [!div class="checklist"]
> * Revendiquer une machine virtuelle dans un laboratoire personnalisé
> * Connexion à la machine virtuelle
> * Cesser de revendiquer la machine virtuelle

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="access-the-lab"></a>Accéder à l’atelier

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Toutes les ressources** dans le menu de gauche. 
3. Sélectionnez **DevTest Labs** comme type de ressource. 
4. Sélectionnez le laboratoire. 

    ![Sélectionner le laboratoire](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>Revendiquer une machine virtuelle

1. Dans la liste des **machines virtuelles pouvant être réclamées**, sélectionnez **...** (points de suspension), puis sélectionnez **Revendiquer la machine**.

    ![Revendiquer une machine virtuelle](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. Vérifiez que la présence de la machine virtuelle dans la liste **Mes machines virtuelles**.

    ![Ma machine virtuelle](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>Connexion à la machine virtuelle

1. Sélectionnez votre machine virtuelle dans la liste. Vous voyez la **page de la machine virtuelle** pour votre machine virtuelle. Sélectionnez **Se connecter** sur la barre d’outils.

    ![Connexion à la machine virtuelle](./media/tutorial-use-custom-lab/connect-button.png)
2. Enregistrer le fichier **RDP** téléchargé sur votre disque dur et utilisez-le pour vous connecter à la machine virtuelle. Spécifiez le nom d’utilisateur et le mot de passe que vous avez indiqués en créant la machine virtuelle à la section précédente. 

## <a name="unclaim-the-vm"></a>Cesser de revendiquer la machine virtuelle
Une fois que vous avez fini d’utiliser la machine virtuelle, cessez de la revendiquer en suivant ces étapes : 

1. Sur la page de la machine virtuelle, sélectionnez **Cesser de revendiquer** sur la barre d’outils. 

    ![Cesser de revendiquer une machine virtuelle](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. La machine virtuelle est arrêtée avant que la revendication ne cesse. 

    ![État de fin de revendication](./media/tutorial-use-custom-lab/unclaim-status.png) 
1. Une fois l’opération de fin de revendication terminée, vous voyez la machine virtuelle dans la liste des **machines virtuelles pouvant être réclamées** en bas. 
    
## <a name="next-steps"></a>Étapes suivantes
Ce didacticiel vous a montré comment accéder et utiliser un laboratoire personnalisé créé à l’aide d’Azure DevTest Labs. Pour plus d’informations sur l’accès et l’utilisation des machines virtuelles dans un laboratoire personnalisé, consultez 

> [!div class="nextstepaction"]
> [Procédure : utiliser des machines virtuelles dans un laboratoire personnalisé](devtest-lab-add-vm.md)

