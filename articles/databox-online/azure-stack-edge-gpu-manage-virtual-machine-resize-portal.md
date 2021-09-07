---
title: Redimensionnement des machines virtuelles sur GPU Azure Stack Edge Pro, Pro R, mini R via le Portail Azure
description: Découvrez comment redimensionner les machines virtuelles exécutées sur votre GPU Azure Stack Edge Pro, Azure Stack Edge Pro R, Azure Stack Edge Mini R via le Portail Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/08/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to resize VMs running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: 5e41edf0cf080d06a0829e31e7105a13a0035f93
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532033"
---
# <a name="use-the-azure-portal-to-resize-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Utiliser le Portail Azure pour redimensionner les machines virtuelles sur votre GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment redimensionner les machines virtuelles déployées sur votre appareil GPU Azure Stack Edge Pro.

       
## <a name="about-vm-sizing"></a>À propos du dimensionnement des machines virtuelles

La taille de machine virtuelle détermine la quantité de ressources de calcul (comme l’UC, le GPU et la mémoire) qui sont mises à la disposition de la machine virtuelle. Vous devez créer des machines virtuelles en utilisant une taille de machine virtuelle adaptée à la charge de travail de votre application. 

Même si toutes les machines vont s’exécuter sur le même matériel, leur taille ont des limites différentes pour l’accès au disque. Cela peut vous aider à gérer l’accès global au disque sur toutes vos machines virtuelles. Si une charge de travail augmente, vous pouvez également redimensionner une machine virtuelle existante.

Pour plus d’informations, consultez [Tailles de machine virtuelle prises en charge pour votre appareil](azure-stack-edge-gpu-virtual-machine-sizes.md).


## <a name="prerequisites"></a>Prérequis

Avant de redimensionner une machine virtuelle en cours d’exécution sur votre appareil via le Portail Azure, assurez-vous que :

1. Au moins une machine virtuelle est déployée sur votre appareil. Pour créer cette machine virtuelle, consultez les instructions fournies dans [Déployer une machine virtuelle sur Azure Stack Edge Pro via le Portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. Votre machine virtuelle être à l’état **Arrêté**. Pour arrêter votre machine virtuelle, accédez à **Machines virtuelles > Vue d’ensemble** et sélectionnez la machine virtuelle que vous souhaitez arrêter. Dans la page Vue d’ensemble, sélectionnez **Arrêter**, puis sélectionnez **Oui** lorsque vous êtes invité à confirmer. Avant de redimensionner votre machine virtuelle, vous devez arrêter la machine virtuelle.

    ![Capture d’écran de l’écran d’arrêt d’une machine virtuelle à partir de la vue d’ensemble des machines virtuelles. Le bouton Oui est mis en surbrillance.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="resize-a-vm"></a>Redimensionner une machine virtuelle

Procédez comme suit pour redimensionner une machine virtuelle déployée sur votre appareil. 

1. Accédez à la machine virtuelle que vous avez arrêtée et sélectionnez **Taille de VM (changer)** dans le panneau **Détails** de la machine virtuelle.
    
    ![Capture d’écran montrant l’onglet Détails pour une machine virtuelle. L’onglet Détails et l’option Taille de VM sont mis en surbrillance.](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-1.png)

2. Dans le panneau **Modifier la taille de la machine virtuelle**, à partir de la barre de commandes, sélectionnez la **Taille de la machine virtuelle**, puis sélectionnez **Modifier**.

    ![Capture d’écran de l’écran Changer la taille de machine virtuelle. Une taille de machine virtuelle est mise en surbrillance, tout comme le bouton Changer.](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-2.png)

3. Vous verrez une notification indiquant que la machine virtuelle est en cours de mise à jour. Une fois la machine virtuelle correctement mise à jour, la page **Vue d’ensemble** est actualisée pour afficher la machine virtuelle redimensionnée.

    ![Capture d’écran de la page de présentation d’une VM La valeur Taille de VM pour la machine virtuelle redimensionnée est mise en surbrillance.](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-3.png)


## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment déployer des machines virtuelles sur votre appareil Azure Stack Edge Pro, consultez [Déployer des machines virtuelles via le portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
