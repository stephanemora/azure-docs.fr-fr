---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 77019068279261a36f8ba40247e5f5694ca77892
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238284"
---
| Ressource | Limite |
| --- | --- |
| Taille du cache |530 Go |
| Bases de données |64 |
| Nombre maximum de clients connectés |40 000 |
| Cache Azure pour les réplicas de Redis, pour la haute disponibilité |1 |
| Partitions dans un cache premium avec le clustering |10 |

Les limites et les tailles des solutions de Cache Azure pour Redis varient en fonction du niveau de tarification. Pour voir les niveaux de tarification et les tailles associées, consultez [Cache Azure pour Redis tarification](https://azure.microsoft.com/pricing/details/cache/).

Pour plus d’informations sur les limites de configuration du Cache Azure pour Redis, consultez la section [Configuration du serveur Redis par défaut](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Étant donné que la configuration et la gestion des instances de Cache Azure pour Redis sont gérées par Microsoft, toutes les commandes Redis ne sont pas prises en charge dans le Cache Azure pour Redis. Pour plus d’informations, consultez [Commandes Redis non prises en charge dans le Cache Azure pour Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

