---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e28dc20fe5e789bbaa5aad9402d37f039eabe6e5
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504208"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les tableaux de bord partagés ne doivent pas comporter de vignettes Markdown contenant du contenu inline](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |N’autorisez pas la création d’un tableau de bord partagé comportant du contenu inline dans des vignettes Markdown. Imposez que le contenu soit stocké comme fichier Markdown hébergé en ligne. Si vous utilisez du contenu inline dans la vignette Markdown, vous ne pouvez pas gérer le chiffrement du contenu. En configurant votre propre stockage, vous pourrez effectuer un chiffrement, un chiffrement double et même une procédure Bring Your Own Key. Cette stratégie empêche les utilisateurs d’utiliser les versions 2020-09-01-preview et ultérieures de l’API REST des tableaux de bord partagés. |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
