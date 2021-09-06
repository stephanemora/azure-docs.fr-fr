---
title: Vue d’ensemble de la suppression de Start/Stop VMs v2 (préversion)
description: Cet article explique comment supprimer la fonctionnalité Start/Stop VMs v2 (préversion).
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.topic: conceptual
ms.openlocfilehash: 1a22faba480eb9c130cc7c883abfc3e53ef58fe5
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970154"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>Comment supprimer Start/Stop VMs v2 (préversion)

Après avoir activé la fonctionnalité Start/Stop VMs v2 (préversion) pour gérer l’état d’exécution de vos machines virtuelles Azure, vous pouvez décider de ne plus l’utiliser. La suppression de cette fonctionnalité peut être effectuée en supprimant le groupe de ressources dédié pour stocker les ressources suivantes pour la prise en charge de Start/Stop VMs v2 (préversion) :

- Les application Azure Functions
- Les planifications dans Azure Logic Apps
- L’instance Application Insights
- Compte de Stockage Azure

> [!NOTE]
> Si vous rencontrez des problèmes lors du déploiement, que vous rencontrez un problème lors de l’utilisation de Start/Stop VMs V2 (préversion) ou si vous avez une question connexe, vous pouvez envoyer un problème sur [GitHub](https://github.com/microsoft/startstopv2-deployments/issues). L’envoi d’un incident au support Azure à partir du [site de support Azure](https://azure.microsoft.com/support/options/) n’est pas disponible pour cette préversion. 

## <a name="delete-the-dedicated-resource-group"></a>Supprimer le groupe de ressources dédié

Pour supprimer le groupe de ressources, procédez de la manière décrite dans l’article [Suppression d’un groupe de ressources et de ressources Azure Resource Manager](../../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Étapes suivantes

Pour redéployer cette fonctionnalité, consultez [Déployer Start/Stop v2](deploy.md) (préversion).