---
title: Protection de l’instance pour la mise à l’échelle de machine virtuelle Azure définie instances | Microsoft Docs
description: Découvrez comment protéger les instances de machine virtuelle Azure identiques à partir d’opérations de mise à l’échelle et de jeu de mise à l’échelle.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: manayar
ms.openlocfilehash: 61430f5a43a04fa0e5b2f0c79ff03419c73aaf28
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416553"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances-preview"></a>Protection de l’instance pour la mise à l’échelle de machine virtuelle Azure définie instances (version préliminaire)
Les machines virtuelles Azure identiques activer élasticité mieux pour vos charges de travail via [mise à l’échelle](virtual-machine-scale-sets-autoscale-overview.md), vous pouvez configurer lors de votre infrastructure échelles-out et quand elle s’adapte. Les groupes identiques vous permettent également de gérer, configurer et mettre à jour un grand nombre de machines virtuelles via différents de manière centralisée [mise à niveau de la stratégie](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) paramètres. Vous pouvez configurer une mise à jour sur le modèle de jeu de mise à l’échelle et la nouvelle configuration est appliquée automatiquement à chaque instance de groupe identique, si vous avez défini la stratégie de mise à niveau automatique ou enchaînées.

Comme votre application traite le trafic, il peut y avoir définir le cas où vous souhaiteriez être traités différemment du reste de la mise à l’échelle des instances spécifiques instance. Par exemple, certaines instances dans le groupe identique peuvent effectuer des opérations de longue, et vous ne souhaitez pas ces instances pour être mis à l’échelle jusqu'à ce que les opérations se terminent. Vous également des spécifiques plusieurs instances dans le groupe identique pour effectuer des tâches supplémentaires ou différents que les autres membres du groupe identique. Vous avez besoin de ces machines virtuelles « spéciales » ne pas à être modifié avec les autres instances dans le jeu de mise à l’échelle. Protection de l’instance fournit les contrôles supplémentaires pour activer ces scénarios et autres pour votre application.

Cet article décrit comment vous pouvez appliquer et utiliser les fonctionnalités de protection autre instance avec des instances de jeu de mise à l’échelle.

> [!NOTE]
>Protection de l’instance est actuellement en version préliminaire publique. Aucune procédure participer n’est nécessaire pour utiliser la fonctionnalité de la version préliminaire publique décrite ci-dessous. Aperçu protection de l’instance est uniquement pris en charge avec la version 2019-03-01 API sur les jeux de mise à l’échelle à l’aide de disques gérés.

## <a name="types-of-instance-protection"></a>Types de protection de l’instance
Scale sets offre deux types de fonctionnalités de protection d’instance :

-   **Protéger à partir de l’échelle**
    - Activée par le biais **protectFromScaleIn** propriété sur l’échelle la valeur instance
    - Protège l’instance à partir de l’échelle dans l’échelle automatique initiée par
    - Opérations d’instance initiée par l’utilisateur (y compris la suppression de l’instance) sont **ne pas bloquée**
    - Opérations lancées sur l’ensemble d’échelle (mise à niveau, réinitialiser, libérer, etc.) sont **ne pas bloquée**

-   **Protéger contre les actions de jeu de mise à l’échelle**
    - Activée par le biais **protectFromScaleSetActions** propriété sur l’échelle la valeur instance
    - Protège l’instance à partir de l’échelle dans l’échelle automatique initiée par
    - Protège l’instance à partir des opérations initiées sur le groupe identique (telles que la mise à niveau, réinitialiser, libérer, etc.)
    - Opérations d’instance initiée par l’utilisateur (y compris la suppression de l’instance) sont **ne pas bloquée**
    - La suppression du groupe identique complet est **ne pas bloquée**

## <a name="protect-from-scale-in"></a>Protéger à partir de l’échelle
Protection de l’instance peut être appliquée pour les instances de groupe identique, une fois que les instances sont créées. La protection est appliquée et modifiée uniquement sur le [modèle d’instance](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) et non pas sur le [identiques modèle](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Il existe plusieurs façons de l’application de mise à l’échelle dans la protection sur vos instances de mise à l’échelle comme indiqué dans les exemples ci-dessous.

### <a name="rest-api"></a>API REST

L’exemple suivant applique une protection en mise à l’échelle à une instance dans le jeu de mise à l’échelle.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>Instance protection est uniquement prise en charge avec l’API version 2019-03-01 et versions ultérieures

### <a name="azure-powershell"></a>Azure PowerShell

Utilisez le [AzVmssVM de mise à jour](/powershell/module/az.compute/update-azvmssvm) applet de commande de mise à l’échelle de protection pour votre groupe identique définie l’instance.

L’exemple suivant applique une protection en mise à l’échelle à une instance dans le jeu de mise à l’échelle ayant l’ID d’instance 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Utilisez [az vmss mettre à jour](/cli/azure/vmss#az-vmss-update) de mise à l’échelle de protection pour votre instance de groupe identique.

L’exemple suivant applique une protection en mise à l’échelle à une instance dans le jeu de mise à l’échelle ayant l’ID d’instance 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Protéger contre les actions de jeu de mise à l’échelle
Protection de l’instance peut être appliquée pour les instances de groupe identique, une fois que les instances sont créées. La protection est appliquée et modifiée uniquement sur le [modèle d’instance](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) et non pas sur le [identiques modèle](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Protection d’une instance à partir d’actions de jeu de mise à l’échelle protège également l’instance à partir de l’échelle dans l’échelle automatique initiée par.

Plusieurs méthodes pour appliquer la mise à l’échelle est défini protection des actions sur votre mise à l’échelle les instances de groupe comme indiqué dans les exemples ci-dessous.

### <a name="rest-api"></a>API REST

L’exemple suivant applique une protection à partir d’actions de jeu de mise à l’échelle à une instance dans le jeu de mise à l’échelle.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>Instance protection est uniquement prise en charge avec l’API version 2019-03-01 et versions ultérieures.</br>
Protection d’une instance à partir d’actions de jeu de mise à l’échelle protège également l’instance à partir de l’échelle dans l’échelle automatique initiée par. Vous ne pouvez pas spécifier « protectFromScaleIn » : false lors de la définition de « protectFromScaleSetActions » : true

### <a name="azure-powershell"></a>Azure PowerShell

Utilisez le [AzVmssVM de mise à jour](/powershell/module/az.compute/update-azvmssvm) applet de commande pour appliquer la protection à partir de la mise à l’échelle définie des actions à votre instance de groupe identique.

L’exemple suivant applique une protection à partir d’actions de jeu de mise à l’échelle à une instance dans le jeu de mise à l’échelle ayant l’ID d’instance 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Utilisez [az vmss mettre à jour](/cli/azure/vmss#az-vmss-update) pour appliquer la protection à partir d’actions de jeu de mise à l’échelle votre instance de groupe identique.

L’exemple suivant applique une protection à partir d’actions de jeu de mise à l’échelle à une instance dans le jeu de mise à l’échelle ayant l’ID d’instance 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Résolution des problèmes
### <a name="no-protectionpolicy-on-scale-set-model"></a>Aucun protectionPolicy sur le modèle du groupe identique
Protection de l’instance est uniquement applicable sur les instances de groupe identique et non sur le modèle du groupe identique.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Aucun protectionPolicy sur le modèle d’instance de jeu de mise à l’échelle
Par défaut, la stratégie de protection n’est pas appliquée à une instance lors de sa création.

Vous pouvez appliquer la protection de l’instance pour les instances de groupe identique, une fois que les instances sont créées.

### <a name="not-able-to-apply-instance-protection"></a>Impossible d’appliquer la protection de l’instance
Instance protection est uniquement prise en charge avec l’API version 2019-03-01 et versions ultérieures. Vérifier la version d’API utilisée et mettre à jour en fonction des besoins. Vous devrez peut-être également mettre à jour votre interface CLI ou PowerShell vers la dernière version.

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [déployer votre application](virtual-machine-scale-sets-deploy-app.md) sur des groupes de machines virtuelles identiques.
