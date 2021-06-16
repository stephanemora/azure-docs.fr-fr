---
title: Exemples Azure PowerShell - Réinitialiser Azure Cloud Services (support étendu)
description: Exemples de scripts pour la réinitialisation d’un déploiement de service cloud Azure (support étendu)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e571a6cfae82274c2e4f3d85540c9724edb657a5
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110705160"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Réinitialiser un service cloud Azure (support étendu) 
Ces exemples couvrent différentes façons de réinitialiser un déploiement de service cloud Azure (support étendu) existant.

## <a name="reimage-role-instances-of-cloud-service"></a>Réinitialiser des instances de rôle de service cloud
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Cette commande réinitialise deux instances de rôle, ContosoFrontEnd_IN_0 et ContosoBackEnd_IN_1, du service cloud nommé ContosoCS qui appartient au groupe de ressources nommé ContosOrg.

## <a name="reimage-all-roles-of-cloud-service"></a>Réinitialiser tous les rôles de service cloud
```powershell
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Cette commande réinitialise toutes les instances de rôle du service cloud nommé ContosoCS qui appartient au groupe de ressources nommé ContosOrg.

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Réinitialiser une seule instance de rôle d’un service cloud
```powershell
Invoke-AzCloudServiceRoleInstanceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0"
```
Cette commande réinitialise une instance de rôle nommée ContosoFrontEnd_IN_0 du service cloud nommé ContosoCS qui appartient au groupe de ressources nommé ContosOrg.

## <a name="rebuild-role-instances-of-cloud-service"></a>Regénérer des instances de rôle de service cloud
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Cette commande regénère deux instances de rôle, ContosoFrontEnd_IN_0 et ContosoBackEnd_IN_1, du service cloud nommé ContosoCS qui appartient au groupe de ressources nommé ContosOrg.

## <a name="rebuild-all-roles-of-cloud-service"></a>Regénérer tous les rôles de service cloud
```powershell
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Cette commande regénère toutes les instances de rôle du service cloud nommé ContosoCS qui appartient au groupe de ressources nommé ContosOrg.

## <a name="restart-role-instances-of-cloud-service"></a>Redémarrer des instances de rôle de service cloud
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Cette commande redémarre deux instances de rôle, ContosoFrontEnd_IN_0 et ContosoBackEnd_IN_1, du service cloud nommé ContosoCS qui appartient au groupe de ressources nommé ContosOrg.

## <a name="restart-all-roles-of-cloud-service"></a>Redémarrer tous les rôles de service cloud
```powershell
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Cette commande redémarre toutes les instances de rôle du service cloud nommé ContosoCS qui appartient au groupe de ressources nommé ContosOrg.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur Azure Cloud Services (support étendu), consultez [Vue d’ensemble d’Azure Cloud Services (support étendu)](overview.md).
- Visitez le [dépôt d’exemples de Cloud Services (support étendu)](https://github.com/Azure-Samples/cloud-services-extended-support).
