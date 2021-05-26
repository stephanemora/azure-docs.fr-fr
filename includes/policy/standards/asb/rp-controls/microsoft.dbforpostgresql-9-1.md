---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 62a6a6d36cd8750ed0408fb25bbb036b41d89779
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110163890"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La sauvegarde géoredondante doit être activée pour Azure Database pour PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database pour PostgreSQL vous permet de choisir l’option de redondance pour votre serveur de base de données. Vous pouvez choisir un stockage de sauvegarde géoredondant. Dans ce cas, les données sont non seulement stockées dans la région qui héberge votre serveur, mais elles sont aussi répliquées dans une région jumelée pour offrir une possibilité de récupération en cas de défaillance régionale. La configuration du stockage géoredondant pour la sauvegarde est uniquement possible lors de la création du serveur. |Audit, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
