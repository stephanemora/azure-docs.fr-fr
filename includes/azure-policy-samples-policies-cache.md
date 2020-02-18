---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 1d5e916d9f8256c002f6810d1fc7aedebeba6481
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170007"
---
|Name |Description |Effet(s) |Version |
|---|---|---|---|
|[Seules les connexions sécurisées à votre cache Redis doivent être activées](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |Auditer l'activation des connexions via SSL uniquement à votre cache Redis. L'utilisation de connexions sécurisées garantit l'authentification entre le serveur et le service et protège les données en transit contre les attaques de la couche réseau (attaque de l'intercepteur ou « man-in-the-middle », écoute clandestine, détournement de session). |Audit, Refuser, Désactivé |1.0.0 |
