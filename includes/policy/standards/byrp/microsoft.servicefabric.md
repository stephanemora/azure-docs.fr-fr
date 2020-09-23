---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2215a3a4aa2a77e17b8f2c56cad42f8c15fb9ba7
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90014976"
---
## <a name="azure-security-benchmark"></a>Benchmark de sécurité Azure

Le [benchmark de sécurité Azure](../../../../articles/security/benchmarks/overview.md) fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure. Pour voir comment ce service correspond totalement au benchmark de sécurité Azure, consultez les [fichiers de correspondance du benchmark de sécurité Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Pour passer en revue la façon dont les composants intégrés Azure Policy disponibles pour tous les services Azure correspondent à ce standard de conformité, consultez [Conformité réglementaire Azure Policy – Benchmark de sécurité Azure](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |ID du contrôle |Titre du contrôle |Policy<br /><sub>(Portail Azure)</sub> |Version de la stratégie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Contrôle des accès et des identités |3.9 |Utiliser Azure Active Directory |[Les clusters Service Fabric ne doivent utiliser Azure Active Directory que pour l’authentification client](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Protection des données |4.8 |Chiffrer des informations sensibles au repos |[La propriété ClusterProtectionLevel doit être définie sur EncryptAndSign pour les clusters Service Fabric](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Pour voir comment les composants intégrés Azure Policy disponibles pour tous les services Azure correspondent à ce standard de conformité, consultez [Conformité réglementaire Azure Policy – NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Pour plus d’informations sur cette norme de conformité, consultez [NIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Domain |ID du contrôle |Titre du contrôle |Policy<br /><sub>(Portail Azure)</sub> |Version de la stratégie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Contrôle d’accès |AC-2 (7) |Gestion des comptes \| Schémas basés sur des rôles |[Les clusters Service Fabric ne doivent utiliser Azure Active Directory que pour l’authentification client](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

