---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: 4a6c0cad4a93d0d99573a348070823b909c75c7e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79299411"
---
|Nom |Description |Effet(s) |Version |GitHub |
|---|---|---|---|---|
|[La sauvegarde Azure doit être activée pour les machines virtuelles](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Cette stratégie permet de vérifier si le service Sauvegarde Azure est activé pour toutes les machines virtuelles. Sauvegarde Azure est une solution de sauvegarde économique en un clic, qui simplifie la récupération des données et peut être activée plus facilement que d’autres services de sauvegarde cloud. |AuditIfNotExists, Désactivé |1.0.0 |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json)
|[Configurer la sauvegarde sur les machines virtuelles d’un emplacement dans un coffre central existant au même emplacement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Cette stratégie configure la protection de Sauvegarde Azure sur les machines virtuelles d’un emplacement donné dans un coffre central existant au même emplacement. Elle s’applique uniquement aux machines virtuelles qui ne sont pas déjà configurées pour la sauvegarde. Nous vous recommandons de ne pas attribuer cette stratégie à plus de 200 machines virtuelles. Si la stratégie est attribuée à plus de 200 machines virtuelles, la sauvegarde peut être déclenchée quelques heures après la planification définie. Cette stratégie sera améliorée pour prendre en charge davantage d’images de machine virtuelle. |deployIfNotExists, auditIfNotExists, désactivé |1.0.0 |[Lien](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json)
