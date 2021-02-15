---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f26c7a4e9a571c317aa9cdfa1267778b607430be
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100241"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le point de terminaison du service bot doit être un URI HTTPS valide](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Les données peuvent être falsifiées pendant la transmission. Certains protocoles chiffrent les données pour résoudre les problèmes d’utilisation abusive et de falsification. Pour veiller à ce que vos bots communiquent uniquement sur des canaux chiffrés, définissez le point de terminaison sur un URI HTTPS valide. Ainsi, le protocole HTTPS est utilisé pour chiffrer vos données en transit, ce qui constitue souvent une exigence à des fins de conformité aux normes réglementaires ou du secteur. Consultez : [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines). |audit, deny, disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[Bot Service doit être chiffré avec une clé gérée par le client](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure Bot Service chiffre automatiquement votre ressource pour protéger vos données et satisfaire aux engagements de sécurité et de conformité de l’organisation. Par défaut, les clés de chiffrement gérées par Microsoft sont utilisées. Pour une plus grande flexibilité dans la gestion des clés ou le contrôle de l’accès à votre abonnement, sélectionnez des clés gérées par le client, option également appelée Bring Your Own Key (BYOK). En savoir plus sur le chiffrement Azure Bot Service : [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](https://docs.microsoft.com/azure/bot-service/bot-service-encryption). |audit, deny, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
