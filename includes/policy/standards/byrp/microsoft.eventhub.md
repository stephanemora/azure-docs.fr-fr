---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/22/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e83eb97445a304a81ab1193a6cdf505450fd24b6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093501"
---
## <a name="azure-security-benchmark"></a>Benchmark de sécurité Azure

Le [benchmark de sécurité Azure](../../../../articles/security/benchmarks/overview.md) fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure. Pour voir comment ce service correspond totalement au benchmark de sécurité Azure, consultez les [fichiers de correspondance du benchmark de sécurité Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Pour passer en revue la façon dont les composants intégrés Azure Policy disponibles pour tous les services Azure correspondent à ce standard de conformité, consultez [Conformité réglementaire Azure Policy – Benchmark de sécurité Azure](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |ID du contrôle |Titre du contrôle |Policy<br /><sub>(Portail Azure)</sub> |Version de la stratégie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Sécurité réseau |1.1 |Protéger les ressources à l'aide de groupes de sécurité réseau ou du Pare-feu Azure sur votre réseau virtuel |[Event Hub doit utiliser un point de terminaison de service de réseau virtuel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|Journalisation et supervision |2.3 |Activer la journalisation d’audit pour les ressources Azure |[Les journaux de diagnostic dans Event Hub doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

