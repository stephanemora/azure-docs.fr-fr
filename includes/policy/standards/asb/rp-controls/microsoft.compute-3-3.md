---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 118b4b6a3028456193176d5efd21bf480f4908b3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910092"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditer les machines Windows qui ont un ou plusieurs membres spécifiés manquants dans le groupe Administrateurs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Nécessite le déploiement des prérequis dans l’étendue de l’attribution de stratégie. Pour plus d’informations, consultez [https://aka.ms/gcpol](../../../../../articles/governance/policy/concepts/guest-configuration.md). Les machines ne sont pas conformes si le groupe Administrateurs local ne contient pas un ou plusieurs membres listés dans le paramètre de stratégie. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[Auditer les machines Windows qui ont des comptes supplémentaires dans le groupe Administrateurs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Nécessite le déploiement des prérequis dans l’étendue de l’attribution de stratégie. Pour plus d’informations, consultez [https://aka.ms/gcpol](../../../../../articles/governance/policy/concepts/guest-configuration.md). Les machines ne sont pas conformes si le groupe Administrateurs local contient des membres qui ne sont pas listés dans le paramètre de stratégie. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |
|[Auditer les machines Windows qui ont les membres spécifiés dans le groupe Administrateurs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Nécessite le déploiement des prérequis dans l’étendue de l’attribution de stratégie. Pour plus d’informations, consultez [https://aka.ms/gcpol](../../../../../articles/governance/policy/concepts/guest-configuration.md). Les machines ne sont pas conformes si le groupe Administrateurs local contient un ou plusieurs des membres listés dans le paramètre de stratégie. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |