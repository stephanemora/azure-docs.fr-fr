---
title: Vue d’ensemble de la suppression de Start/Stop VMs v2 (préversion)
description: Cet article explique comment supprimer la fonctionnalité Start/Stop VMs v2 (préversion).
services: azure-functions
ms.subservice: ''
ms.date: 03/30/2021
ms.topic: conceptual
ms.openlocfilehash: b4308be8f7494c1cb6f6b4839fc5a2e9717668e3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110942"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>Comment supprimer Start/Stop VMs v2 (préversion)

Après avoir activé la fonctionnalité Start/Stop VMs v2 (préversion) pour gérer l’état d’exécution de vos machines virtuelles Azure, vous pouvez décider de ne plus l’utiliser. La suppression de cette fonctionnalité peut être effectuée en supprimant le groupe de ressources dédié pour stocker les ressources suivantes pour la prise en charge de Start/Stop VMs v2 (préversion) :

- Les application Azure Functions
- Les planifications dans Azure Logic Apps
- L’instance Application Insights
- Compte de Stockage Azure

## <a name="delete-the-dedicated-resource-group"></a>Supprimer le groupe de ressources dédié

Pour supprimer le groupe de ressources, procédez de la manière décrite dans l’article [Suppression d’un groupe de ressources et de ressources Azure Resource Manager](../../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Étapes suivantes

Pour redéployer cette fonctionnalité, consultez [Déployer Start/Stop v2](deploy.md) (préversion).