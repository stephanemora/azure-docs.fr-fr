---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2a30c574c8d6c3b05b291965b2d510faa8d3beb6
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108793452"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La sauvegarde géoredondante doit être activée pour Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB vous permet de choisir l’option de redondance pour votre serveur de base de données. Vous pouvez choisir un stockage de sauvegarde géoredondant. Dans ce cas, les données sont non seulement stockées dans la région qui héberge votre serveur, mais elles sont aussi répliquées dans une région jumelée pour offrir une possibilité de récupération en cas de défaillance régionale. La configuration du stockage géoredondant pour la sauvegarde est uniquement possible lors de la création du serveur. |Audit, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
