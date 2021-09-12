---
title: Gérer des disques de machines virtuelles sur GPU Azure Stack Edge Pro, Pro R, mini R via le Portail Azure
description: Découvrez comment gérer les disques, y compris ajouter, redimensionner ou supprimer des disques de données pour des machines virtuelles déployées sur vos Azure Stack Edge Pro GPU, Azure Stack Edge Pro R et Azure Stack Edge Mini R via le portail Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage disks on a VM running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: 8b9be47fb87c626bb5e6f2f20054f6cf249a57d0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562489"
---
# <a name="use-the-azure-portal-to-manage-disks-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Utiliser le Portail Azure pour gérer des disques sur les machines virtuelles sur votre GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Vous pouvez approvisionner des disques sur les machines virtuelles déployées sur votre appareil Azure Stack Edge Pro à l’aide du Portail Azure. Les disques sont approvisionnés sur l’appareil via l’Azure Resource Manager local et consomment la capacité de l’appareil. Les opérations telles que l’ajout, le détachement et la suppression d’un disque peuvent être effectuées via le portail Azure, qui à son tour fait appel à l’Azure Resource Manager local pour approvisionner le stockage. 

Cet article explique comment ajouter, détacher et supprimer un disque de données pour une machine virtuelle existante, ainsi que redimensionner celle-ci via le portail Azure.

        
## <a name="about-disks-on-vms"></a>À propos des disques sur les machines virtuelles

Votre machine virtuelle peut disposer d’un disque de système d’exploitation et d’un disque de données. Chaque machine virtuelle déployée sur votre appareil dispose d’un disque de système d’exploitation attaché. Ce disque de système d’exploitation est doté d’un système d’exploitation préinstallé qui a été sélectionnée lors de la création de la machine virtuelle. Ce disque contient le volume de démarrage.

> [!NOTE]
> Vous ne pouvez pas modifier la taille du disque de système d’exploitation pour une machine virtuelle déployée sur votre appareil. La taille du disque de système d’exploitation est déterminée par la taille de machine virtuelle que vous avez sélectionnée.

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

1. Accédez à la machine virtuelle à laquelle vous souhaitez ajouter un disque de données, puis, dans les **Détails** de la machine virtuelle, sélectionnez **Disques**.
    
    ![Capture d’écran montrant les paramètres Disques sous l’onglet Détails d’une machine virtuelle Azure Stack Edge. L’étiquette Disques est mise en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-1.png)

1. Dans le panneau **Disques**, sous **Disques de données**, sélectionnez **Créer et attacher un nouveau disque**.

    ![Capture d’écran montrant le panneau Disques en mode Vue d’ensemble pour des machines virtuelles. L’option Créer et attacher un nouveau disque, sous Disques de données, est mise en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-2.png)

1. Dans le panneau **Créer un disque**, entrez les paramètres suivants :

    
    |Champ  |Description  |
    |---------|---------|
    |Nom     | Nom unique dans le groupe de ressources. Il est impossible de modifier le nom une fois que le disque de données a été créé.     |
    |Groupe de ressources Edge |Entrez le groupe de ressources Edge dans lequel stocker le nouveau disque.|
    |Taille| Taille de votre disque de données en Gio. La taille maximale d’un disque de données est déterminée par la taille de machine virtuelle que vous avez sélectionnée. Lors de la configuration d’un disque, vous devez également prendre en compte l’espace réel sur votre appareil et les autres charges de travail de la machine virtuelle qui s’exécutent et qui consomment la capacité.  |         

    ![Capture d’écran montrant le panneau Créer un disque pour une machine virtuelle. Le bouton OK est mis en surbrillance.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-3.png)

    Sélectionnez **OK** et continuez.

1. Dans l’affichage **Disques**, vous voyez une entrée correspondant au nouveau disque. Acceptez la valeur par défaut ou attribuez un numéro d’unité logique (LUN) valide au disque, puis sélectionnez **Enregistrer**. Un numéro d’unité logique (LUN) est un identificateur unique pour un disque SCSI. Pour plus d’informations, consultez [Qu’est-ce qu’un numéro d’unité logique ?](../virtual-machines/linux/azure-to-guest-disk-mapping.md#what-is-a-lun).

    ![Capture d’écran montrant l’écran Disques pour des machines virtuelles après l’ajout d’un nouveau disque. Le numéro de disque pour le nouveau disque et le bouton Enregistrer sont mis en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-4.png)

1. Un message de notification s’affichera et indiquera que le disque est en cours de création. Une fois le disque correctement créé, la machine virtuelle est mise à jour. 

    ![Capture d’écran montrant la notification de création de disque en cours.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-5.png)

1. Revenez à la page **Détails** de la machine virtuelle. La liste des disques est mise à jour pour afficher le disque de données que vous venez de créer.

    ![Capture d’écran montrant l’onglet Détails pour une machine virtuelle. Un disque de machine virtuelle nouvellement créé est mis en évidence dans la zone Disques.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-6.png)


## <a name="change-a-data-disk"></a>Modifier un disque de données

Procédez comme suit pour modifier un disque associé à une machine virtuelle déployée sur votre appareil.

1. Accédez à la machine virtuelle à laquelle est associé le disque de données à modifier, puis, dans le panneau **Détails** de la machine virtuelle, sélectionnez **Disques**.

1. Dans la liste des disques de données, sélectionnez le disque que vous souhaitez modifier. À l’extrême droite du disque sélectionné, sélectionnez l’icône de modification (crayon).  

    ![Capture d’écran montrant Disques sur une machine virtuelle. La section Disques de données et l’icône de crayon utilisée pour modifier un disque de données sont mis en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-1.png)

1. Dans le panneau **Modifier le disque**, vous pouvez uniquement modifier la taille du disque. Vous ne pouvez pas modifier le nom d’un disque une fois celui-ci créé. Modifiez la **Taille** du disque, puis enregistrez les modifications.

    ![Capture d’écran montrant le panneau Modifier le disque pour une machine virtuelle. L’option Taille et le bouton OK sont mis en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-2.png)

    > [!NOTE]
    > Vous pouvez uniquement développer un disque de données. Vous ne pouvez pas le réduire.

1. Dans l’affichage **Disques**, la liste des disques est actualisée pour afficher le disque mis à jour.


## <a name="attach-an-existing-disk"></a>Association d'un disque existant

Procédez comme suit pour attacher un disque existant à la machine virtuelle déployée sur votre appareil.

1. Accédez à la machine virtuelle à laquelle vous souhaitez attacher le disque existant, puis, dans les **Détails** de la machine virtuelle, sélectionnez **Disques**.

1. Dans le panneau **Disques**, sous **Disques de données**, sélectionnez **Attacher un disque existant**.

    ![Capture d’écran du panneau Disques pour une machine virtuelle Azure Stack Edge. L’option « Attacher un disque existant » est mise en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-1.png)

1. Acceptez le numéro d’unité logique par défaut ou attribuez un numéro d’unité logique valide. Choisissez un disque de données existant dans la liste déroulante. Sélectionnez **Enregistrer**.

    ![Capture d’écran montrant le panneau Disques pour une machine virtuelle Azure Stack Edge après l’ajout d’un disque existant. Le numéro d’unité logique et le nom du disque sont mis en évidence, tout comme le bouton Enregistrer.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-2.png)

    Sélectionnez **Enregistrer** et continuez.

1. Vous verrez une notification indiquant que la machine virtuelle est mise à jour. Une fois la machine virtuelle mise à jour, revenez à la page **Détails** de la machine virtuelle. Actualisez la page pour afficher le disque qui vient d’être attaché dans la liste des disques de données.

    ![Capture d’écran montrant la section Disques de données du volet Détails pour une machine virtuelle. Une entrée de disque de données est mise en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="detach-a-data-disk"></a>Détachement d'un disque de données

Procédez comme suit pour détacher ou supprimer un disque de données associé à une machine virtuelle déployée sur votre appareil.

> [!NOTE]
> - Vous pouvez supprimer un disque de données pendant que la machine virtuelle est en cours d’exécution. Vérifiez que rien n’utilise activement le disque avant de le détacher de la machine virtuelle.
> - Si vous détachez un disque, il n’est pas supprimé automatiquement. Suivez les étapes décrites dans [Supprimer un disque](#delete-a-data-disk) ci-dessous.

1. Accédez à la machine virtuelle dont vous souhaitez détacher un disque de données, puis, dans les **Détails** de la machine virtuelle, sélectionnez **Disques**.

    ![Capture d’écran montrant la section Disques de données du volet Détails pour une machine virtuelle. Les entrées de disque sous Disques de données sont mises en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. Dans la liste des disques, sélectionnez le disque que vous souhaitez détacher. Tout à fait à droite du disque sélectionné, sélectionnez l’icône Détacher (« X »). Le disque sélectionné sera détaché. Sélectionnez **Enregistrer**.

    ![Capture d’écran de l’affichage Disques pour une machine virtuelle. L’icône X pour un disque non attaché qui peut être détaché et le bouton Enregistrer sont mis en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/detach-data-disk-1.png)

1. Une fois le disque détaché, la machine virtuelle est mise à jour. Actualisez la page pour afficher la liste mise à jour des disques de données.

    ![Capture d’écran de l’affichage Disques actualisé pour une machine virtuelle après le détachement d’un disque de données. La liste Disques de données est mise en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="delete-a-data-disk"></a>Supprimer un disque de données

Pour supprimer un disque de données qui n’est pas attaché à une machine virtuelle déployée sur votre appareil, procédez comme suit.

> [!NOTE]
> Avant de supprimer un disque de données, vous devez [détacher le disque de données de la machine virtuelle](#detach-a-data-disk) s’il est en cours d’utilisation.

1. Accédez à **Machines virtuelles** sur votre appareil, puis accédez au volet **Ressources**. Sélectionnez **Disques**.
 
    ![Capture d’écran montrant l’onglet Disques sur le panneau Ressources pour les machines virtuelles. Les onglet Ressources et Disques sont mis en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/delete-disk-1.png)

1. Dans la liste des disques, sélectionnez le disque que vous souhaitez supprimer. Tout à fait à droite du disque sélectionné, sélectionnez l’icône Supprimer (Corbeille).

    ![Capture d’écran montrant l’onglet Disques sur le panneau Ressources pour les machines virtuelles. Un disque de données avec l’icône Corbeille indiquant qu’il peut être supprimé est mis en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/delete-disk-2.png)

    Si vous ne voyez pas l’icône Supprimer, vous pouvez sélectionner le nom de la machine virtuelle dans la colonne **Machine virtuelle attachée** et [détacher le disque de la machine virtuelle](#detach-a-data-disk).

1. Un message s’affiche, vous invitant à confirmer que vous souhaitez supprimer le disque. L’opération est irréversible. Sélectionnez **Oui**.

    ![Capture d’écran montrant la notification vous invitant à confirmer que vous souhaitez supprimer un disque de données.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/delete-disk-3.png)

    Une fois la suppression effectuée, le disque est supprimé de la liste.


## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment déployer des machines virtuelles sur votre appareil Azure Stack Edge Pro, consultez [Déployer des machines virtuelles via le portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
