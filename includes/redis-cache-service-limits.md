---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 77019068279261a36f8ba40247e5f5694ca77892
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177467"
---
| Ressource | Limite |
| --- | --- |
| Taille du cache |530 Go |
| Bases de données |64 |
| Nombre maximal de clients connectés |40 000 |
| Réplicas du Cache Azure pour Redis, pour la haute disponibilité |1 |
| Partitions dans un cache premium avec le clustering |10 |

Les limites et les tailles des solutions de Cache Azure pour Redis varient en fonction du niveau de tarification. Pour connaître les niveaux de tarification et les tailles associées, consultez la section [Tarification du Cache Azure pour Redis](https://azure.microsoft.com/pricing/details/cache/).

Pour plus d’informations sur les limites de configuration du Cache Azure pour Redis, consultez la section [Configuration du serveur Redis par défaut](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Étant donné que la configuration et la gestion des instances de Cache Azure pour Redis sont gérées par Microsoft, toutes les commandes Redis ne sont pas prises en charge dans le Cache Azure pour Redis. Pour plus d’informations, consultez [Commandes Redis non prises en charge dans le Cache Azure pour Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

