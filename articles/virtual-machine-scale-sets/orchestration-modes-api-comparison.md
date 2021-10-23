---
title: Comparaison des API des modes d’orchestration
description: En savoir plus sur les différences d’API entre les modes d’orchestration Uniforme et Flexible.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: db141f863389d724cc1437beeed3b00b44020098
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161835"
---
# <a name="orchestration-modes-api-comparison"></a>Comparaison des API des modes d’orchestration 

Cet article compare les différences d’API entre les modes d'[orchestration Flexible](..\virtual-machines\flexible-virtual-machine-scale-sets.md) et Uniforme pour les groupes de machines virtuelles identiques. Pour en savoir plus sur les groupes de machines virtuelles identiques Uniformes et Flexibles, consultez [modes d’orchestration](virtual-machine-scale-sets-orchestration-modes.md).


## <a name="instance-view"></a>Vue d’instance

| API uniforme | Alternative flexible |
|-|-|
| Vue d’instance des groupes de machines virtuelles identiques | Obtenir la vue d’instance sur des machines virtuelles individuelles ; utiliser les Graphes de ressources pour interroger l’état d’alimentation |


## <a name="scale-set-lifecycle-batch-operations"></a>Opérations par lot du cycle de vie du groupe identique  

| API uniforme | Alternative flexible |
|-|-|
| Opérations par lot du cycle de vie des groupes de machines virtuelles identiques :  | Appeler une API de machine virtuelle unique sur des instances spécifiques : |
| [Libérer](/rest/api/compute/virtualmachinescalesetvms/deallocate)  | [Appeler une API de machine virtuelle unique - Libérer](/rest/api/compute/virtualmachines/deallocate)   |
| [Supprimer](/rest/api/compute/virtualmachinescalesetvms/delete)  | [Appeler une API de machine virtuelle unique - Libérer](/rest/api/compute/virtualmachines/delete)  |
| [Obtenir une vue d’instance](/rest/api/compute/virtualmachinescalesetvms/getinstanceview)  | [Appeler une API de machine virtuelle unique - Vue d’instance](/rest/api/compute/virtualmachines/instanceview)  |
| [Effectuer la maintenance](/rest/api/compute/virtualmachinescalesetvms/performmaintenance)  | [Appeler une API de machine virtuelle unique - Effectuer la maintenance](/rest/api/compute/virtualmachines/performmaintenance)  |
| [Mise hors tension](/rest/api/compute/virtualmachinescalesetvms/poweroff)  | [Appeler une API de machine virtuelle unique - Mise hors tension](/rest/api/compute/virtualmachines/poweroff)  |
| [Redeploy](/rest/api/compute/virtualmachinescalesetvms/redeploy)  | [Appeler une API de machine virtuelle unique - Redéployer](/rest/api/compute/virtualmachines/redeploy)  |
| [Réinitialiser](/rest/api/compute/virtualmachinescalesetvms/reimage)  | [Appeler une API de machine virtuelle unique - Réinitialiser](/rest/api/compute/virtualmachines/reimage)  |
| [Tout réinitialiser](/rest/api/compute/virtualmachinescalesetvms/reimageall)  | Non applicable |
| [Redémarrer](/rest/api/compute/virtualmachinescalesetvms/restart)  | [Appeler une API de machine virtuelle unique - Redémarrer](/rest/api/compute/virtualmachines/restart)  |
| [Simuler une éviction](/rest/api/compute/virtualmachinescalesetvms/simulateeviction) | [Appeler une API de machine virtuelle unique - Simuler une éviction](/rest/api/compute/virtualmachines/simulateeviction)  |
| [Start](/rest/api/compute/virtualmachinescalesetvms/start) | [Appeler une API de machine virtuelle unique - Démarrer](/rest/api/compute/virtualmachines/start) |


## <a name="get-or-update"></a>Obtenir ou Mettre à jour 

### <a name="uniform-api"></a>API uniforme
Instance Obtenir ou Mettre à jour des groupes de machines virtuelles identiques :
- [Get](/rest/api/compute/virtualmachinescalesetvms/get) 
- [Mettre à jour](/rest/api/compute/virtualmachinescalesetvms/update)

### <a name="flexible-alternative"></a>Alternative flexible 
Appeler des API de machine virtuelle uniques :
- [Ressource de verrouillage ARM](../azure-resource-manager/management/lock-resources.md?tabs=json) pour un comportement de type Protection de l’instance 


## <a name="list-instances"></a>Instances de liste 

### <a name="uniform-api"></a>API uniforme
`VMSS List Instances`: 
- Retourne l’ID du groupe identique associé à chaque instance

### <a name="flexible-alternative"></a>Alternative flexible
Graphe des ressources Azure : 

```armasm
resources 
| where type == "microsoft.compute/virtualmachines" 
| where properties.virtualMachineScaleSet.id contains "portalbb01" 
```

## <a name="scale-set-operations"></a>Opérations de groupe identique 

### <a name="uniform-api"></a>API uniforme
Opérations des groupes de machines virtuelles identiques :
- [Mettre à jour les instances](/rest/api/compute/virtual-machine-scale-sets/update-instances)
- [Libérer](/rest/api/compute/virtual-machine-scale-sets/deallocate)
- [Effectuer la maintenance](/rest/api/compute/virtual-machine-scale-sets/perform-maintenance)
- [Mise hors tension](/rest/api/compute/virtual-machine-scale-sets/power-off)
- [Redeploy](/rest/api/compute/virtual-machine-scale-sets/redeploy)
- [Réinitialiser](/rest/api/compute/virtual-machine-scale-sets/reimage)
- [Tout réinitialiser](/rest/api/compute/virtual-machine-scale-sets/reimage-all)
- [Redémarrer](/rest/api/compute/virtual-machine-scale-sets/restart)
- [Définir l’état du service d’orchestration](/rest/api/compute/virtual-machine-scale-sets/set-orchestration-service-state)
- [Start](/rest/api/compute/virtual-machine-scale-sets/start)

### <a name="flexible-alternative"></a>Alternative flexible
Appeler des opérations sur des machines virtuelles individuelles.

Opérations de machines virtuelles :
- [Réinitialisation](/rest/api/compute/virtual-machines/reimage) : Invoquer une API de machine virtuelle unique - Réinitialiser des machines virtuelles de système d’exploitation éphémère uniquement

## <a name="vm-extension"></a>Extension de machine virtuelle

### <a name="uniform-api"></a>API uniforme
Extension de groupes de machines virtuelles identiques :
- [Créer ou mettre à jour](/rest/api/compute/virtual-machine-scale-set-vm-extensions/create-or-update)
- [Supprimer](/rest/api/compute/virtual-machine-scale-set-vm-extensions/delete)
- [Get](/rest/api/compute/virtual-machine-scale-set-vm-extensions/get)
- [Liste](/rest/api/compute/virtual-machine-scale-set-vm-extensions/list)
- [Mettre à jour](/rest/api/compute/virtual-machine-scale-set-vm-extensions/update) 

### <a name="flexible-alternative"></a>Alternative flexible
Appeler des opérations sur des machines virtuelles individuelles.


## <a name="networking"></a>Réseau 

### <a name="uniform-api"></a>API uniforme
- Réacheminement de port/pool NAT 
- Pool NAT non pris en charge dans des groupes de machines virtuelles flexibles  

### <a name="flexible-alternative"></a>Alternative flexible
- Configurer des règles NAT individuelles sur chaque machine virtuelle


## <a name="scale-set-apis"></a>API de groupe identique

### <a name="uniform-api"></a>API uniforme
API de groupes de machines virtuelles identiques uniformes :
- [Convertir en Groupe de placement unique](/rest/api/compute/virtual-machine-scale-sets/convert-to-single-placement-group)
- [Etape de récupération forcée du domaine de mise à jour de la plateforme Service Fabric](/rest/api/compute/virtual-machine-scale-sets/force-recovery-service-fabric-platform-update-domain-walk)

### <a name="flexible-alternative"></a>Alternative flexible
Non prise en charge sur les groupes de machines virtuelles identiques flexibles.


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [En savoir plus sur les différents Modes d’orchestration](virtual-machine-scale-sets-orchestration-modes.md)