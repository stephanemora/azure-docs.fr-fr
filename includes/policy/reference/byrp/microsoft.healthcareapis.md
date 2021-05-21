---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 41429ce15d3db58cf15103dc2cd01f132c612d57
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110087852"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API Azure pour FHIR doit utiliser une clé gérée par le client pour chiffrer les données au repos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |Utilisez une clé gérée par le client pour contrôler le chiffrement au repos des données stockées dans API Azure pour FHIR lorsqu’il s’agit d’une exigence réglementaire ou de conformité. Les clés gérées par le client fournissent également un double chiffrement en ajoutant une deuxième couche de chiffrement en plus du chiffrement par défaut effectué avec les clés gérées par le service. |Audit, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[API Azure pour FHIR doit utiliser une liaison privée](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |API Azure pour FHIR doit avoir au moins une connexion de point de terminaison privé approuvée. Les clients d’un réseau virtuel peuvent accéder de façon sécurisée aux ressources qui ont des connexions de point de terminaison privés via des liens privés. Pour plus d’informations, consultez [https://aka.ms/fhir-privatelink](https://aka.ms/fhir-privatelink). |Audit, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[CORS ne doit pas autoriser tous les domaines à accéder à votre API pour FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |Le partage des ressources cross-origin (CORS) ne doit pas autoriser tous les domaines à accéder à votre API pour FHIR. Pour protéger votre API pour FHIR, supprimez l’accès pour tous les domaines et définissez explicitement les domaines autorisés à se connecter. |Audit, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
