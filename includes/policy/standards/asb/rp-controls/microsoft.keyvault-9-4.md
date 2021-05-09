---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/28/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b9b7eef56c122ec9bc3a59f1442b8ad935a8c71b
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108213109"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La protection contre la suppression définitive doit être activée sur les coffres de clés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |La suppression malveillante d’un coffre de clés peut entraîner une perte définitive des données. Une personne malveillante interne à votre organisation peut potentiellement supprimer et vider des coffres de clés. La protection contre la suppression définitive vous protège des attaques internes en appliquant une période de conservation obligatoire pour les coffres de clés supprimés de manière réversible. Personne au sein de votre organisation ni chez Microsoft ne pourra supprimer définitivement vos coffres de clés pendant la période de conservation de la suppression réversible. |Audit, Refuser, Désactivé |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
