---
title: Gérer des disques de machines virtuelles sur GPU Azure Stack Edge Pro, Pro R, mini R via le Portail Azure
description: Découvrez comment gérer les disques, y compris ajouter ou détacher un disque de données, sur des machines virtuelles déployées sur votre GPU Azure Stack Edge Pro, Azure Stack Edge Pro R et Azure Stack Edge Mini R de périphérie via le Portail Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage disks on a VM running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: dff3f4bafdb35d89e8f1792c1aca68824a9bc685
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079895"
---
# <a name="use-the-azure-portal-to-manage-disks-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Utiliser le Portail Azure pour gérer des disques sur les machines virtuelles sur votre GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Vous pouvez approvisionner des disques sur les machines virtuelles déployées sur votre appareil Azure Stack Edge Pro à l’aide du Portail Azure. Les disques sont approvisionnés sur l’appareil via l’Azure Resource Manager local et consomment la capacité de l’appareil. Les opérations telles que l’ajout d’un disque et le détachement d’un disque peuvent être effectuées via le Portail Azure, qui à son tour effectue des appels au Azure Resource Manager local pour approvisionner le stockage. 

Cet article explique comment ajouter un disque de données à une machine virtuelle existante, détacher un disque de données et enfin redimensionner la machine virtuelle avec le Portail Azure. 

        
## <a name="about-disks-on-vms"></a>À propos des disques sur les machines virtuelles

Votre machine virtuelle peut disposer d’un disque de système d’exploitation et d’un disque de données. Chaque machine virtuelle déployée sur votre appareil dispose d’un disque de système d’exploitation attaché. Ce disque de système d’exploitation est doté d’un système d’exploitation préinstallé qui a été sélectionnée lors de la création de la machine virtuelle. Ce disque contient le volume de démarrage.

> [!NOTE]
> Vous ne pouvez pas modifier la taille du disque de système d’exploitation pour la machine virtuelle sur votre appareil. La taille du disque de système d’exploitation est déterminée par la taille de la machine virtuelle que vous avez sélectionnée. 


En revanche, un disque de données est un disque managé attaché à la machine virtuelle en cours d’exécution sur votre appareil. Un disque de données est utilisé pour stocker les données d’application. Les disques de données sont généralement des lecteurs SCSI. La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher à une machine virtuelle. Par défaut, le stockage Premium est utilisé pour héberger les disques.

Une machine virtuelle déployée sur votre appareil peut parfois contenir un disque temporaire. Il fournit un stockage à court terme pour les applications et les processus, et est destiné à stocker seulement des données comme les fichiers de pagination ou d’échange. Les données présentes sur le disque temporaire peuvent être perdues lors d’un événement de maintenance ou quand vous redéployez une machine virtuelle. Lors d’un redémarrage standard réussi de la machine virtuelle, les données présentes sur le disque temporaire sont conservées. 


## <a name="prerequisites"></a>Prérequis

Avant de commencer à gérer des disques sur les machines virtuelles en cours d’exécution sur votre appareil via le Portail Azure, assurez-vous de ce qui suit :


1. Vous avez accès à un appareil GPU Azure Stack Edge Pro activé. Vous avez également activé une interface réseau pour le calcul sur votre appareil. Cette action crée un commutateur virtuel sur cette interface réseau sur votre machine virtuelle. 
    1. Dans l’interface utilisateur locale de votre appareil, accédez à **Calcul**. Sélectionnez l’interface réseau que vous utiliserez pour créer un commutateur virtuel.

        > [!IMPORTANT] 
        > Vous ne pouvez configurer qu’un seul port pour le calcul.

    1. Activez le calcul sur l’interface réseau. Le GPU Azure Stack Edge Pro crée et gère un commutateur virtuel correspondant à cette interface réseau.

1. Au moins une machine virtuelle est déployée sur votre appareil. Pour créer cette machine virtuelle, consultez les instructions fournies dans [Déployer une machine virtuelle sur Azure Stack Edge Pro via le Portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).



## <a name="add-a-data-disk"></a>Ajouter un disque de données

Procédez comme suit pour ajouter un disque à une machine virtuelle déployée sur votre appareil. 

1. Accédez à la machine virtuelle à laquelle vous souhaitez ajouter un disque de données, puis accédez à la page **Vue d’ensemble**. Sélectionnez **Disques**.
    
    ![Sélectionner des disques sur la page Vue d’ensemble](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-1.png)

1. Dans le panneau **Disques**, sous **Disques de données**, sélectionnez **Créer et attacher un nouveau disque**.

    ![Créer et attacher un nouveau disque](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-2.png)

1. Dans le panneau **Créer un disque**, entrez les paramètres suivants :

    
    |Champ  |Description  |
    |---------|---------|
    |Nom     | Nom unique dans le groupe de ressources. Il est impossible de modifier le nom une fois que le disque de données a été créé.     |
    |Taille| Taille de votre disque de données en Gio. La taille maximale d’un disque de données est déterminée par la taille de machine virtuelle que vous avez sélectionnée. Lors de la configuration d’un disque, vous devez également prendre en compte l’espace réel sur votre appareil et les autres charges de travail de la machine virtuelle qui s’exécutent et qui consomment la capacité.  |         

    ![Créer un panneau de disque](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-3.png)

    Sélectionnez **OK** et continuez.

1. Dans la page **Vue d’ensemble**, sous **Disques**, vous verrez une entrée correspondant au nouveau disque. Acceptez la valeur par défaut ou affectez un numéro d’unité logique (LUN) valide au disque et sélectionnez **Enregistrer**. Un numéro d’unité logique (LUN) est un identificateur unique pour un disque SCSI. Pour plus d’informations, consultez [Qu’est-ce qu’un numéro d’unité logique ?](../virtual-machines/linux/azure-to-guest-disk-mapping.md#what-is-a-lun).

    ![Nouveau disque sur la page Vue d’ensemble](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-4.png)

1. Un message de notification s’affichera et indiquera que le disque est en cours de création. Une fois le disque correctement créé, la machine virtuelle est mise à jour. 

    ![Notification de création de disque](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-5.png)

1. Revenez à la page **Vue d’ensemble**. La liste des disques est mise à jour pour afficher le disque de données que vous venez de créer.

    ![Liste mise à jour des disques de données](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-6.png)


## <a name="change-a-data-disk"></a>Modifier un disque de données

Procédez comme suit pour modifier un disque associé à une machine virtuelle déployée sur votre appareil.

1. Accédez à la machine virtuelle qui contient le disque de données à modifier et accédez à la page **Vue d’ensemble**. Sélectionnez **Disques**.

1. Dans la liste des disques de données, sélectionnez le disque que vous souhaitez modifier. À l’extrême droite du disque sélectionné, sélectionnez l’icône de modification (crayon).  

    ![Sélectionner un disque à modifier](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-1.png)

1. Dans le panneau **Modifier le disque**, vous pouvez uniquement modifier la taille du disque. Le nom associé au disque ne peut pas être modifié une fois qu’il a été créé. Modifiez la **Taille** et enregistrez les modifications.

    ![Modifier la taille du disque de données](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-2.png)

    > [!NOTE]
    > Vous ne pouvez que développer un disque de données, vous ne pouvez pas réduire le disque.

1. Sur la page **Vue d’ensemble**, la liste des disques est actualisée pour afficher le disque mis à jour.


## <a name="attach-an-existing-disk"></a>Association d'un disque existant

Procédez comme suit pour attacher un disque existant à la machine virtuelle déployée sur votre appareil.

1. Accédez à la machine virtuelle à laquelle vous souhaitez attacher le disque existant, puis accédez à la page **Vue d’ensemble**. Sélectionnez **Disques**.
    
    ![Sélectionner des disques ](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. Dans le panneau **Disques**, sous **Disques de données**, sélectionnez **Attacher un disque existant**.

    ![Sélectionner Attacher un disque existant](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-1.png)

1. Acceptez le numéro d’unité logique par défaut ou attribuez un numéro d’unité logique valide. Choisissez un disque de données existant dans la liste déroulante. Sélectionnez Enregistrer.

    ![Sélectionner un disque existant](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-2.png)

    Sélectionnez **Enregistrer** et continuez.

1. Vous verrez une notification indiquant que la machine virtuelle est mise à jour. Une fois la machine virtuelle mise à jour, revenez à la page **Vue d’ensemble**. Actualisez la page pour afficher le disque qui vient d’être attaché dans la liste des disques de données.

    ![Afficher la liste des disques de données mis à jour sur la page Vue d’ensemble](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="detach-a-data-disk"></a>Détachement d'un disque de données

Procédez comme suit pour détacher ou supprimer un disque de données associé à une machine virtuelle déployée sur votre appareil.

> [!NOTE]
> - Vous pouvez supprimer un disque de données pendant que la machine virtuelle est en cours d’exécution. Vérifiez que rien n’utilise activement le disque avant de le détacher de la machine virtuelle.
> - Si vous détachez un disque, il n’est pas supprimé automatiquement.

1. Accédez à la machine virtuelle à partir de laquelle vous souhaitez détacher un disque de données et accédez à la page **Vue d’ensemble**. Sélectionnez **Disques**.

    ![Sélectionner des disques](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. Dans la liste des disques, sélectionnez le disque que vous souhaitez détacher. À l’extrême droite du disque sélectionné, sélectionnez l’icône de détachement (croix). L’entrée sélectionnée sera détachée. Sélectionnez **Enregistrer**. 

    ![Sélectionner un disque à détacher](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/detach-data-disk-1.png)

1. Une fois le disque détaché, la machine virtuelle est mise à jour. Actualisez la page **Vue d’ensemble** pour afficher la liste mise à jour des disques de données.

    ![Sélectionner Enregistrer](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment déployer des machines virtuelles sur votre appareil Azure Stack Edge Pro, consultez [Déployer des machines virtuelles via le Portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
