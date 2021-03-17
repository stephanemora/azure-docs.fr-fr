---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c495e2f25f9e55fc3147ca4c8bcddf51288865ea
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021844"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditer les machines Windows qui ont un ou plusieurs membres spécifiés manquants dans le groupe Administrateurs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Nécessite le déploiement des prérequis dans l’étendue de l’attribution de stratégie. Pour plus d’informations, consultez [https://aka.ms/gcpol](https://aka.ms/gcpol). Les machines ne sont pas conformes si le groupe Administrateurs local ne contient pas un ou plusieurs membres listés dans le paramètre de stratégie. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[Auditer les machines Windows qui ont des comptes supplémentaires dans le groupe Administrateurs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Nécessite le déploiement des prérequis dans l’étendue de l’attribution de stratégie. Pour plus d’informations, consultez [https://aka.ms/gcpol](https://aka.ms/gcpol). Les machines ne sont pas conformes si le groupe Administrateurs local contient des membres qui ne sont pas listés dans le paramètre de stratégie. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |
|[Auditer les machines Windows qui ont les membres spécifiés dans le groupe Administrateurs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Nécessite le déploiement des prérequis dans l’étendue de l’attribution de stratégie. Pour plus d’informations, consultez [https://aka.ms/gcpol](https://aka.ms/gcpol). Les machines ne sont pas conformes si le groupe Administrateurs local contient un ou plusieurs des membres listés dans le paramètre de stratégie. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
