---
title: Réparations automatiques d’instances avec les groupes de machines virtuelles identiques Azure
description: Découvrez comment configurer la stratégie de réparation automatique pour les instances de machine virtuelle dans un groupe identique
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: f335b0fb3396103c321d740bcf6d125e60e95086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274577"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Aperçu : Réparations automatiques d’instances pour les groupes de machines virtuelles identiques Azure

L’activation des réparations automatiques d’instance pour les groupes de machines virtuelles identiques Azure permet d’obtenir une haute disponibilité pour les applications en conservant un ensemble d’instances saines. Si une instance du groupe identique n’est pas saine, comme indiqué par l’[extension d’intégrité de l’application](./virtual-machine-scale-sets-health-extension.md) ou les [sondes d’intégrité de l’équilibreur de charge](../load-balancer/load-balancer-custom-probe-overview.md), cette fonctionnalité effectue automatiquement une réparation de l’instance en supprimant l’instance non saine et en en créant une nouvelle pour la remplacer.

> [!NOTE]
> Cette fonctionnalité d’évaluation est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Conditions requises pour l’utilisation de réparations automatiques d’instances

**S’abonner à la fonctionnalité d’évaluation Réparations automatiques d’instances**

Utilisez l’API REST ou Azure PowerShell pour vous abonner à la fonctionnalité d’évaluation des réparations automatiques d’instances. Cette procédure permet d’inscrire votre abonnement pour la fonctionnalité d’évaluation. Notez qu’il ne s’agit que d’une configuration unique requise pour l’utilisation de cette fonctionnalité. Si votre abonnement est déjà inscrit pour la fonctionnalité d’évaluation des réparations automatiques d’instances, vous n’avez pas besoin de vous inscrire à nouveau. 

Utilisation de l'API REST 

1. S’inscrire à la fonctionnalité en utilisant [Features - Register](/rest/api/resources/features/register) 

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview/register?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registering"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

2. Patientez quelques minutes pour que l’*State* (État) se change en *Registered* (Inscrit). Vous pouvez utiliser l’API suivante pour le confirmer.

```
GET on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registered"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

3. Une fois que l’*State* (État) s’est changé en *Registered* (Inscrit), exécutez la commande suivante.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Utilisation de Microsoft Azure PowerShell

1. Inscrivez-vous à la fonctionnalité à l’aide de la cmdlet [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) suivie de [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. Patientez quelques minutes pour que *RegistrationState* (État d’inscription) se change en *Registered* (Inscrit). Vous pouvez utiliser la cmdlet suivante pour le confirmer.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 La réponse doit être comme suit.

| FeatureName                           | ProviderName            | RegistrationState       |
|---------------------------------------|-------------------------|-------------------------|
| RepairVMScaleSetInstancesPreview      | Microsoft.Compute       | Registered              |

3. Une fois que le *RegistrationState* se change en *Registered*, exécutez la cmdlet suivante.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**Activer l’analyse de l’intégrité des applications pour le groupe identique**

Le groupe identique doit permettre l’analyse de l’intégrité des applications pour les instances activées. Pour ce faire, vous pouvez utiliser l’[extension d’intégrité d’application](./virtual-machine-scale-sets-health-extension.md) ou les [sondes d’intégrité de l’équilibreur de charge](../load-balancer/load-balancer-custom-probe-overview.md). Un seul de ces outils peut être activé à la fois. L’extension d’intégrité d’application ou les sondes de l’équilibreur de charge effectuent un test ping sur le point de terminaison d’application configuré sur les instances de machine virtuelle pour déterminer l’état d’intégrité de l’application. Cet état d’intégrité est utilisé par l’orchestrateur du groupe identique pour analyser l’intégrité de l’instance et effectuer des réparations quand cela est nécessaire.

**Configurer un point de terminaison pour fournir l’état d’intégrité**

Avant d’activer la stratégie de réparation automatique d’instances, vérifiez que le point de terminaison d’application est configuré pour les instances du groupe identique afin d’émettre l’état d’intégrité de l’application. Lorsqu’une instance retourne l’état 200 (OK) sur ce point de terminaison d’application, l’instance est marquée comme « saine ». Dans tous les autres cas, l’instance est marquée comme « non saine », y compris dans les scénarios suivants :

- Lorsqu’aucun point de terminaison d’application n’est configuré à l’intérieur des instances de machine virtuelle pour fournir l’état d’intégrité de l’application
- Lorsque le point de terminaison d’application n’est pas configuré correctement
- Lorsque le point de terminaison d’application n’est pas accessible

Pour les instances marquées comme non saines, des réparations automatiques sont déclenchées par le groupe identique. Assurez-vous que le point de terminaison d’application est correctement configuré avant d’activer la stratégie de réparation automatique afin d’éviter les réparations d’instances involontaires pendant la configuration du point de terminaison.

**Activer le groupe à placement unique**

Cette préversion est actuellement disponible uniquement pour les groupes identiques déployés en tant que groupe à placement unique. La propriété *singlePlacementGroup* doit avoir la valeur *true* pour que votre groupe identique utilise la fonctionnalité de réparation automatique d’instances. En savoir plus sur les [groupes de placement](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**Version d’API**

La stratégie de réparation automatique est prise en charge pour l’API de calcul version 2018-10-01 ou ultérieure.

**Restrictions relatives aux déplacements de ressources ou d’abonnements**

Dans le cadre de cette préversion, les déplacements de ressources ou d’abonnements ne sont actuellement pas pris en charge pour les groupes identiques lorsque la stratégie de réparation automatique est activée.

**Restriction pour les groupes identiques Service Fabric**

Cette fonctionnalité d’évaluation n’est actuellement pas prise en charge pour les groupes identiques Service Fabric.

## <a name="how-do-automatic-instance-repairs-work"></a>Comment fonctionnent les réparations automatiques d’instances ?

La fonctionnalité de réparation automatique d’instances s’appuie sur l’analyse de l’intégrité d’instances individuelles dans un groupe identique. Les instances de machine virtuelle dans un groupe identique peuvent être configurées de façon à émettre l’état d’intégrité de l’application à l’aide de l’[extension d’intégrité d’application](./virtual-machine-scale-sets-health-extension.md) ou des [sondes d’intégrité de l’équilibreur de charge](../load-balancer/load-balancer-custom-probe-overview.md). Si une instance est détectée non saine, le groupe identique effectue une action de réparation en supprimant l’instance non saine et en en créant une nouvelle pour la remplacer. Cette fonctionnalité peut être activée dans le modèle de groupe de machines virtuelles identiques à l’aide de l’objet *automaticRepairsPolicy*.

### <a name="batching"></a>Traitement par lot

Les opérations de réparation automatique d’instances sont effectuées par lots. À un moment donné, pas plus de 5 % des instances du groupe identique sont réparées par la stratégie de réparation automatique. Cela permet d’éviter la suppression et la recréation simultanées d’un grand nombre d’instances si elles sont détectées non saines en même temps.

### <a name="grace-period"></a>Période de grâce

Lorsqu’une instance passe par une opération de changement d’état en raison d’une action PUT, PATCH ou POST effectuée sur le groupe identique (par exemple, une réinitialisation, un redéploiement, une mise à jour, etc.), toute action de réparation sur cette instance est exécutée uniquement après l’attente de la période de grâce. La période de grâce correspond à la durée nécessaire pour permettre à l’instance de retrouver son état d’intégrité. La période de grâce commence une fois que le changement d’état est terminé. Cela permet d’éviter toute opération de réparation prématurée ou accidentelle. La période de grâce est honorée pour toute instance nouvellement créée dans le groupe identique (y compris celle créée à la suite d’une opération de réparation). La période de grâce est spécifiée en minutes au format ISO 8601 et peut être définie à l’aide de la propriété *automaticRepairsPolicy.gracePeriod*. La période de grâce peut être comprise entre 30 minutes et 90 minutes, 30 minutes étant la valeur par défaut.

Le processus de réparation automatique d’instances fonctionne comme suit :

1. L’[extension d’intégrité d’application](./virtual-machine-scale-sets-health-extension.md) ou les [sondes d’intégrité de l’équilibreur de charge](../load-balancer/load-balancer-custom-probe-overview.md) effectuent un test ping sur le point de terminaison d’application à l’intérieur de chaque machine virtuelle du groupe identique pour obtenir l’état d’intégrité d’application de chaque instance.
2. Si le point de terminaison répond avec l’état 200 (OK), l’instance est marquée comme « saine ». Dans tous les autres cas (y compris si le point de terminaison est inaccessible), l’instance est marquée comme « non saine ».
3. Lorsqu’une instance est détectée non saine, le groupe identique effectue une action de réparation en supprimant l’instance non saine et en en créant une nouvelle pour la remplacer.
4. Les réparations d’instances sont effectuées par lots. À un moment donné, pas plus de 5 % du nombre total d’instances du groupe identique sont réparées. Si un groupe identique comporte moins de 20 instances, les réparations sont effectuées sur une instance non saine à la fois.
5. Le processus ci-dessus se poursuit jusqu’à ce que toutes les instances non saines dans le groupe identique soient réparées.

## <a name="instance-protection-and-automatic-repairs"></a>Protection d’instance et réparations automatiques

Si l’instance d’un groupe identique est protégée par la *[stratégie de protection contre les actions de groupe identique](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* , les réparations automatiques ne sont pas effectuées sur cette instance.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Activation de la stratégie de réparation automatique lors de la création d’un groupe identique

Pour activer la stratégie de réparation automatique lors de la création d’un groupe identique, assurez-vous que toutes les [conditions requises](#requirements-for-using-automatic-instance-repairs) pour l’adhésion à cette fonctionnalité sont remplies. Le point de terminaison d’application doit être correctement configuré pour les instances de groupe identique afin d’éviter de déclencher des réparations involontaires pendant la configuration du point de terminaison. Pour les groupes identiques nouvellement créés, toute réparation d’instance est effectuée uniquement après l’expiration de la période de grâce. Pour activer la réparation automatique d’instances dans un groupe identique, utilisez l’objet *automaticRepairsPolicy* dans le modèle de groupe de machines virtuelles identiques.

### <a name="rest-api"></a>API REST

L’exemple suivant montre comment activer la réparation automatique d’instances dans un modèle de groupe identique. Utilisez la version d’API 2018-10-01 ou ultérieure.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

La fonctionnalité de réparation automatique d’instances peut être activée lors de la création d’un groupe identique à l’aide de la cmdlet [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig). Cet exemple de script présente la création d’un groupe identique et les ressources associées à l’aide du fichier config : [Créez un groupe de machines virtuelles identiques complet](./scripts/powershell-sample-create-complete-scale-set.md). Vous pouvez configurer la stratégie de réparation automatique des instances en ajoutant les paramètres *EnableAutomaticRepair* et *AutomaticRepairGracePeriod* à l’objet de configuration servant à la création du groupe identique. L’exemple suivant active la fonctionnalité avec une période de grâce de 30 minutes.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Activation de la stratégie de réparation automatique lors de la mise à jour d’un groupe identique existant

Avant d’activer la stratégie de réparation automatique dans un groupe identique existant, assurez-vous que toutes les [conditions requises](#requirements-for-using-automatic-instance-repairs) pour l’adhésion à cette fonctionnalité sont remplies. Le point de terminaison d’application doit être correctement configuré pour les instances de groupe identique afin d’éviter de déclencher des réparations involontaires pendant la configuration du point de terminaison. Pour activer la réparation automatique d’instances dans un groupe identique, utilisez l’objet *automaticRepairsPolicy* dans le modèle de groupe de machines virtuelles identiques.

### <a name="rest-api"></a>API REST

L’exemple suivant active la stratégie avec une période de grâce de 40 minutes. Utilisez la version d’API 2018-10-01 ou ultérieure.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Utilisez la cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) pour modifier la configuration de la fonctionnalité de réparation automatique d’instances dans un groupe identique existant. L’exemple suivant met à jour la période de grâce à 40 minutes.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>Dépanner

**Échec de l’activation de la stratégie de réparation automatique**

Si vous obtenez une erreur « BadRequest » avec un message indiquant que le membre « automaticRepairsPolicy » est introuvable sur l’objet de type « properties », vérifiez la version de l’API utilisée pour le groupe de machines virtuelles identiques. La version d’API 2018-10-01 ou ultérieure est requise pour cette fonctionnalité.

**L’instance n’est pas réparée même lorsque la stratégie est activée**

L’instance peut être en période de grâce. Il s’agit de la durée d’attente après toute modification d’état sur l’instance avant d’effectuer des réparations. Cela permet d’éviter toute réparation prématurée ou accidentelle. L’action de réparation doit se produire une fois que la période de grâce est terminée pour l’instance.

**Affichage de l’état d’intégrité d’application pour les instances de groupe identique**

Vous pouvez utiliser l’[API Get Instance View](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) pour les instances d’un groupe de machines virtuelles identiques pour afficher l’état d’intégrité de l’application. Avec Azure PowerShell, vous pouvez utiliser la cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) avec l’indicateur *-InstanceView*. L’état d’intégrité de l’application est fourni sous la propriété *vmHealth*.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer l’[extension d’intégrité d’application](./virtual-machine-scale-sets-health-extension.md) ou les [sondes d’intégrité de l’équilibreur de charge](../load-balancer/load-balancer-custom-probe-overview.md) pour vos groupes identiques.
