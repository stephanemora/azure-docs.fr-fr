---
title: Exemples Azure PowerShell - Réinitialiser Azure Cloud Services (support étendu)
description: Exemples de scripts pour la réinitialisation d’un déploiement de service cloud Azure (support étendu)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 40b44fd277eac14a5bf2c15f58fccfd9d5b156c4
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881483"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Réinitialiser un service cloud Azure (support étendu) 
Ces exemples couvrent différentes façons de réinitialiser un déploiement de service cloud Azure (support étendu) existant.

## <a name="reimage-role-instances-of-cloud-service"></a>Réinitialiser des instances de rôle de service cloud
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances -Reimage
```
Cette commande réinitialise 2 instances de rôle, **ContosoFrontEnd\_IN\_0** et **ContosoBackEnd\_IN\_1**, du service cloud nommé ContosoCS qui appartient au groupe de ressources nommé ContosOrg.

## <a name="reimage-all-roles-of-cloud-service"></a>Réinitialiser tous les rôles de service cloud
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Reimage
```

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Réinitialiser une seule instance de rôle d’un service cloud
```powershell
Reset-AzCloudServiceRoleInstance -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0" -Reimage
```

## <a name="restart-a-single-role-instance-of-a-cloud-service"></a>Redémarrer une seule instance de rôle d’un service cloud
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Restart
```

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur Azure Cloud Services (support étendu), consultez [Vue d’ensemble d’Azure Cloud Services (support étendu)](overview.md).
- Visitez le [dépôt d’exemples de Cloud Services (support étendu)](https://github.com/Azure-Samples/cloud-services-extended-support).