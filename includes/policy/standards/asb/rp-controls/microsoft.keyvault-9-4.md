---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f3d63180219caa99fed22a5f8249de7e8f1a3eea
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097231"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La protection contre la suppression définitive doit être activée sur les coffres de clés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |La suppression malveillante d’un coffre de clés peut entraîner une perte définitive des données. Une personne malveillante interne à votre organisation peut potentiellement supprimer et vider des coffres de clés. La protection contre la suppression définitive vous protège des attaques internes en appliquant une période de conservation obligatoire pour les coffres de clés supprimés de manière réversible. Personne au sein de votre organisation ni chez Microsoft ne pourra supprimer définitivement vos coffres de clés pendant la période de conservation de la suppression réversible. |Audit, Refuser, Désactivé |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
