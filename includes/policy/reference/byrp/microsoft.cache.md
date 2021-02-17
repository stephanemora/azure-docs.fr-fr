---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4f9711ddf6501273c9ed9ebb2c5c0e142d8e0096
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100098574"
---
|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Cache pour Redis doit se trouver dans un réseau virtuel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |Le déploiement d’un réseau virtuel Azure offre une sécurité améliorée et une isolation pour Azure Cache for Redis, ainsi que des sous-réseaux, des stratégies de contrôle d’accès et d’autres fonctionnalités pour restreindre davantage l’accès. Quand une instance Azure Cache for Redis est configurée avec un réseau virtuel, elle n’est pas adressable publiquement et n’est accessible qu’à partir des machines virtuelles et des applications au sein du réseau virtuel. |Audit, Refuser, Désactivé |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Seules les connexions sécurisées à votre instance Azure Cache pour Redis doivent être activées](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditer l’activation des connexions établies uniquement par le biais de SSL au cache Azure pour Redis. L'utilisation de connexions sécurisées garantit l'authentification entre le serveur et le service et protège les données en transit contre les attaques de la couche réseau (attaque de l'intercepteur ou « man-in-the-middle », écoute clandestine, détournement de session). |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
