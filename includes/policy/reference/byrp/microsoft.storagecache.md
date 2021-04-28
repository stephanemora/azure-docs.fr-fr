---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f14136e77bb0018ea5e15f6e07fa7a633addf49f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876007"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les comptes HPC Cache doivent utiliser une clé gérée par le client pour le chiffrement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Gérez le chiffrement au repos d’Azure HPC Cache avec des clés gérées par le client. Par défaut, les données client sont chiffrées avec des clés gérées par le service. Cependant, des clés gérées par le client sont généralement nécessaires pour répondre aux standards de la conformité réglementaire. Les clés gérées par le client permettent de chiffrer les données avec une clé Azure Key Vault que vous avez créée et dont vous êtes le propriétaire. Vous avez le contrôle total et la responsabilité du cycle de vie des clés, notamment leur permutation et leur gestion. |Audit, Désactivé, Refus |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
