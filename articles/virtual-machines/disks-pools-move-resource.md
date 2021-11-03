---
title: Déplacer un pool de disques Azure (version préliminaire) vers un autre abonnement
description: Découvrez comment déplacer un pool de disques Azure vers un autre abonnement.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 71b0fd25412f7269e7de717ee2b8f1b4df056011
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082775"
---
# <a name="move-a-disk-pool-preview-to-a-different-subscription"></a>Déplacer un pool de disques (version préliminaire) vers un autre abonnement

Le déplacement d’un pool de disques Azure (version préliminaire) implique le déplacement du pool de disques lui-même, les disques contenus dans le pool de disques, le groupe de ressources managées du pool de disques et toutes les ressources contenues dans le groupe de ressources gérées. Actuellement, Azure ne prend pas en charge le déplacement de plusieurs groupes de ressources vers un autre abonnement à la fois. 

- Exportez le modèle de votre pool de disques existant.
- Supprimez l’ancien pool de disques.
- Déplacez les ressources Azure nécessaires pour créer un pool de disques.
- Redéployez le pool de disques.

## <a name="export-your-existing-disk-pool-template"></a>Exporter votre modèle de pool de disques existant

Pour simplifier le processus de redéploiement, exportez le modèle à partir de votre pool de disques existant. Vous pouvez utiliser ce modèle pour redéployer le pool de disques dans un abonnement de votre choix, avec la même configuration. Consultez [cet article](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource) pour en savoir plus sur l’exportation d’un modèle à partir d’une ressource.

## <a name="delete-the-old-disk-pool"></a>Supprimer l’ancien pool de disques

Maintenant que vous avez exporté le modèle, supprimez l’ancien pool de disques. La suppression du pool de disques supprime la ressource de pool de disques et son groupe de ressources managées. Consultez [cet article](disks-pools-deprovision.md) pour obtenir de l’aide sur la suppression d’un pool de disques.

## <a name="move-your-disks-and-virtual-network"></a>Déplacer vos disques et votre réseau virtuel

Maintenant que le pool de disques est supprimé, vous pouvez déplacer le réseau virtuel et vos disques, et éventuellement vos clients, vers l’abonnement que vous souhaitez modifier. Consultez [cet article](../azure-resource-manager/management/move-resource-group-and-subscription.md) pour en savoir plus sur le déplacement des ressources Azure vers un autre abonnement.

## <a name="redeploy-your-disk-pool"></a>Redéployer votre pool de disques

Une fois que vous avez déplacé vos autres ressources dans l’abonnement, mettez à jour le modèle de votre ancien pool de disques afin que toutes les références à vos disques, au réseau virtuel, au sous-réseau et aux clients, pointent maintenant toutes vers leurs nouveaux URI de ressource. Une fois que vous avez terminé, redéployez le modèle dans le nouvel abonnement. Pour savoir comment modifier et déployer un modèle, consultez [cet article](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md#edit-and-deploy-the-template).

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment gérer votre pool de disques, consultez[Gérer un pool de disques](disks-pools-manage.md).
