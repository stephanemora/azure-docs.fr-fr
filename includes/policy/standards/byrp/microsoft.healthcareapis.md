---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f561747ff530a92103cffc7b7b11a228c0b47311
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105036342"
---
## <a name="cmmc-level-3"></a>CMMC niveau 3

Pour voir comment les composants intégrés Azure Policy de tous les services Azure répondent à ce standard de conformité, consultez [Conformité réglementaire Azure Policy – CMMC niveau 3](../../../../articles/governance/policy/samples/cmmc-l3.md).
Pour plus d’informations sur ce standard de conformité, consultez [Cybersecurity Maturity Model Certification (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Domain |ID du contrôle |Titre du contrôle |Policy<br /><sub>(Portail Azure)</sub> |Version de la stratégie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Contrôle d’accès |AC.1.001 |Restreindre l’accès au système d’information aux utilisateurs autorisés, aux processus agissant au nom des utilisateurs autorisés, et aux appareils (y compris d’autres systèmes d’information). |[CORS ne doit pas autoriser tous les domaines à accéder à votre API pour FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Contrôle d’accès |AC.1.002 |Restreindre l’accès au système d’information aux types de transactions et de fonctions que seuls les utilisateurs autorisés peuvent exécuter. |[CORS ne doit pas autoriser tous les domaines à accéder à votre API pour FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Contrôle d’accès |AC.2.016 |Contrôler le flux de CUI conformément aux autorisations approuvées. |[CORS ne doit pas autoriser tous les domaines à accéder à votre API pour FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Gestion de la configuration |CM.3.068 |Limiter, désactiver ou empêcher l’utilisation de programmes, fonctions, ports, protocoles et services non essentiels. |[CORS ne doit pas autoriser tous les domaines à accéder à votre API pour FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Protection du système et des communications |SC.3.177 |Utiliser le chiffrement validé FIPS lorsqu’il est utilisé pour protéger la confidentialité d’informations CUI. |[API Azure pour FHIR doit utiliser une clé gérée par le client pour chiffrer les données au repos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|Protection du système et des communications |SC.3.183 |Refuser le trafic des communications réseau par défaut et autoriser le trafic des communications réseau par exception (c’est-à-dire, tout refuser, autoriser par exception). |[CORS ne doit pas autoriser tous les domaines à accéder à votre API pour FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |

