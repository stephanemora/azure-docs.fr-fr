---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7d7d83e9d1be508ac03860a444df3388310428b9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654949"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La propriété ClusterProtectionLevel doit être définie sur EncryptAndSign pour les clusters Service Fabric](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric fournit trois niveaux de protection (None, Sign et EncryptAndSign) pour la communication nœud à nœud à l’aide d’un certificat de cluster principal. Définissez le niveau de protection pour vous assurer que tous les messages de nœud à nœud sont chiffrés et signés numériquement |Audit, Refuser, Désactivé |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[Les clusters Service Fabric ne doivent utiliser Azure Active Directory que pour l’authentification client](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Auditer l'utilisation de l'authentification client uniquement par le biais d'Azure Active Directory dans Service Fabric |Audit, Refuser, Désactivé |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
