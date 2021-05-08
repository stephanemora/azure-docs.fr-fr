---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/28/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 44ae7fbfa73a6374af2e55ee7e26c3857e114447
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108212155"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La sécurisation du transfert vers des comptes de stockage doit être activée](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Auditer l’exigence de transfert sécurisé dans votre compte de stockage. L’option de sécurisation du transfert oblige votre compte de stockage à accepter uniquement des requêtes provenant de connexions sécurisées (HTTPS). L’utilisation de HTTPS garantit l’authentification entre le serveur et le service et protège les données en transit contre les attaques de la couche réseau (attaque de l’intercepteur ou « man-in-the-middle », écoute clandestine, détournement de session) |Audit, Refuser, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
