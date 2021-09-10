---
title: Gérer des groupes de ressources Edge sur votre appareil Azure Stack Edge Pro avec GPU
description: Découvrez comment gérer des groupes de ressources Edge sur votre appareil Azure Stack Edge Pro avec GPU, Azure Stack Edge Pro R et Azure Stack Edge Mini R via le portail Azure.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: c77fb42579af8d1e4bad7e55746ad36d16c983b3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532443"
---
# <a name="manage-edge-resource-groups-on-azure-stack-edge-pro-gpu-devices"></a>Gérer les groupes de ressources Edge sur des appareils Azure Stack Edge Pro avec GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Les groupes de ressources Edge contiennent des ressources qui sont créées sur l’appareil via l’instance locale d’Azure Resource Manager lors de la création et du déploiement de la machine virtuelle. Ces ressources locales peuvent inclure des machines virtuelles, des images de machine virtuelle, des disques, des interfaces réseau et d’autres types de ressources tels que les comptes de stockage Edge.

Cet article explique comment afficher et supprimer des groupes de ressources Edge sur un appareil Azure Stack Edge Pro avec GPU.

## <a name="view-edge-resource-groups"></a>Afficher les groupes de ressources Edge

Procédez comme suit pour afficher les groupes de ressources Edge pour l’abonnement en cours.

1. Accédez à **Machines virtuelles** sur votre appareil, puis accédez au volet **Ressources**. Sélectionnez **Groupes de ressources Edge**.

    ![Capture d’écran de la vue Ressources pour les machines virtuelles sur un appareil Azure Stack Edge. L’onglet Groupes de ressources Edge est affiché et mis en évidence.](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-01.png)

    > [!NOTE]
    > Vous pouvez obtenir la même liste en utilisant [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-6.1.0&preserve-view=true) dans Azure Powershell après avoir configuré l’environnement Azure Resource Manager sur votre appareil. Pour plus d’informations, consultez [Se connecter à Azure Resource Manager](azure-stack-edge-gpu-connect-resource-manager.md).


## <a name="delete-an-edge-resource-group"></a>Supprimer un groupe de ressources Edge

Procédez comme suit pour supprimer un groupe de ressources Edge qui n’est plus utilisé.

> [!NOTE]
> - Un groupe de ressources doit être vide pour être supprimé. 
> - Vous ne pouvez pas supprimer le groupe de ressources ASERG. Ce groupe de ressources stocke le réseau virtuel ASEVNET, qui est créé automatiquement lorsque vous activez le calcul sur votre appareil.

1. Accédez à **Machines virtuelles** sur votre appareil, puis accédez au volet **Ressources**. Sélectionnez **Groupes de ressources Edge**.

    ![Capture d’écran montrant la vue Ressources pour les machines virtuelles sur un appareil Azure Stack Edge. L’onglet Groupes de ressources Edge est affiché et mis en évidence.](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-01.png)

1. Sélectionnez le groupe de ressources que vous souhaitez supprimer. À l’extrême droite du groupe de ressources, sélectionnez l’icône de suppression (corbeille).

   L’icône de suppression s’affiche uniquement lorsqu’un groupe de ressources ne contient aucune ressource.

    ![Capture d’écran de la vue Ressources, onglet Groupes de ressources Edge, pour les machines virtuelles sur un appareil Azure Stack Edge. Une icône en forme de corbeille à côté d’un groupe de ressources indique qu’il peut être supprimé. L’icône est mise en évidence.](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-02.png)

1. Un message s’affiche pour vous demander de confirmer la suppression du groupe de ressources Edge. L’opération est définitive. Sélectionnez **Oui**.

    ![Capture d’écran de l’onglet Groupes de ressources Edge dans la vue Ressources pour les machines virtuelles. L’icône en forme de corbeille mise en évidence indique qu’un groupe de ressources peut être supprimé.](./media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-03.png)

    Une fois la suppression terminée, le groupe de ressources est supprimé de la liste.

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment administrer votre appareil Azure Stack Edge Pro GPU, consultez [Administrer un appareil Azure Stack Edge Pro GPU avec l’interface utilisateur web locale](azure-stack-edge-manage-access-power-connectivity-mode.md).

- [Configurez l’environnement Azure Resource Manager sur votre appareil](azure-stack-edge-gpu-connect-resource-manager.md).