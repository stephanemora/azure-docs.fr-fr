---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4d95cac0532c17bc369beccc376603567b0defe0
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100090499"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les travaux Azure Data Box doivent activer le chiffrement double pour les données au repos sur l’appareil](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |Activez une deuxième couche de chiffrement basé sur un logiciel pour les données au repos sur l’appareil. L’appareil est déjà protégé par un chiffrement Advanced Encryption Standard 256 bits pour les données au repos. Cette option ajoute une deuxième couche de chiffrement des données. |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Les travaux Azure Data Box doivent utiliser une clé gérée par le client pour chiffrer le mot de passe de déverrouillage de l’appareil](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |Utilisez une clé gérée par le client pour contrôler le chiffrement du mot de passe de déverrouillage de l’appareil pour Azure Data Box. Les clés gérées par le client permettent également de gérer l’accès au mot de passe de déverrouillage de l’appareil par le service Data Box afin de préparer l’appareil et de copier des données de manière automatisée. Les données situées sur l’appareil lui-même sont déjà chiffrées au repos par un chiffrement Advanced Encryption Standard 256 bits, et le mot de passe de déverrouillage de l’appareil est chiffré par défaut avec une clé gérée par Microsoft. |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
