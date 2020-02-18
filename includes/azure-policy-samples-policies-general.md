---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 8319ec2bf3965ee30db3e7d4ba1346bd7a3dd7d4
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169899"
---
|Name |Description |Effet(s) |Version |
|---|---|---|---|
|[Emplacements autorisés](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |Cette stratégie vous permet de restreindre les emplacements que votre organisation peut spécifier lors du déploiement de ressources. Utilisez-la pour appliquer vos exigences de conformité géographique. Exclut les groupes de ressources, Microsoft.azureactivedirectory/b2cdirectories et les ressources qui utilisent la région « globale ». |deny |1.0.0 |
|[Emplacements autorisés pour les groupes de ressources](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |Cette stratégie vous permet de restreindre les emplacements où votre organisation peut créer des groupes de ressources. Utilisez-la pour appliquer vos exigences de conformité géographique. |deny |1.0.0 |
|[Types de ressources autorisés](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) |Cette stratégie vous permet de spécifier les types de ressources que votre organisation peut déployer. Seuls les types de ressources prenant en charge « tags » et « location » sont affectés par cette stratégie. Pour restreindre toutes les ressources, dupliquez cette stratégie et affectez à « mode » la valeur « All ». |deny |1.0.0 |
|[Vérifier que l’emplacement de la ressource correspond à l’emplacement du groupe de ressources](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |Vérifie que l’emplacement de la ressource correspond à l’emplacement de son groupe de ressources |audit |1.0.0 |
|[Auditer l’utilisation de règles personnalisées RBAC](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |Auditer des rôles intégrés tels que « Propriétaire, contributeur, lecteur » au lieu des rôles RBAC personnalisés, qui sont susceptibles d’engendrer des erreurs. L’utilisation de rôles personnalisés est traitée comme une exception et nécessite un contrôle rigoureux et la modélisation des menaces |Audit, Désactivé |1.0.0 |
|[Les rôles de propriétaire d’abonnement personnalisé ne doivent pas exister](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |Cette stratégie garantit qu’il n’existe aucun rôle de propriétaire d’abonnement personnalisé. |Audit, Désactivé |1.0.0 |
|[Types de ressources non autorisés](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) |Cette stratégie vous permet de spécifier les types de ressources que votre organisation ne peut pas déployer. |Deny |1.0.0 |
