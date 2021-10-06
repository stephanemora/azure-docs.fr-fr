---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 386109e6a28514c6d57f201bdc6097f4d969cdad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909725"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les machines virtuelles doivent chiffrer les disques temporaires, les caches et les flux de données entre les ressources de calcul et de stockage](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Par défaut, le système d’exploitation et les disques de données d’une machine virtuelle sont chiffrés au repos à l’aide de clés gérées par la plateforme. Les disques temporaires, les caches de données et le flux de données entre le calcul et le stockage ne sont pas chiffrés. Ignorez cette recommandation dans les deux cas suivants : Vous utilisez le chiffrement sur l’hôte. Le chiffrement côté serveur des Disques managés répond à vos besoins en matière de sécurité. Découvrez-en plus sur le [chiffrement côté serveur du Stockage sur disque Azure](../../../../../articles/virtual-machines/disk-encryption.md). et les [différentes offres de chiffrement de disque](../../../../../articles/virtual-machines/disk-encryption-overview.md#comparison). |AuditIfNotExists, Désactivé |[2.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |