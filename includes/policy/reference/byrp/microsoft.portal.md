---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/20/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e2d8c130cdb31f94ff044b1c10330059e0ad5ff7
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122607233"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les tableaux de bord partagés ne doivent pas comporter de vignettes Markdown contenant du contenu inline](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |N’autorisez pas la création d’un tableau de bord partagé comportant du contenu inline dans des vignettes Markdown. Imposez que le contenu soit stocké comme fichier Markdown hébergé en ligne. Si vous utilisez du contenu inline dans la vignette Markdown, vous ne pouvez pas gérer le chiffrement du contenu. En configurant votre propre stockage, vous pourrez effectuer un chiffrement, un chiffrement double et même une procédure Bring Your Own Key. Cette stratégie empêche les utilisateurs d’utiliser les versions 2020-09-01-preview et ultérieures de l’API REST des tableaux de bord partagés. |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
