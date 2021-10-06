---
author: curib
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: cauribeg
ms.openlocfilehash: a04462ab50a5db71e9d019ab9d49fb137f569569
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585179"
---
| Ressource | Limite |
| --- | --- |
| Taille du cache |1,2 To |
| Bases de données |64 |
| Nombre maximal de clients connectés |40 000 |
| Réplicas du Cache Azure pour Redis, pour la haute disponibilité |1 |
| Partitions dans un cache premium avec le clustering |10 |

Les limites et les tailles des solutions de Cache Azure pour Redis varient en fonction du niveau de tarification. Pour connaître les niveaux de tarification et les tailles associées, consultez la section [Tarification du Cache Azure pour Redis](https://azure.microsoft.com/pricing/details/cache/).

Pour plus d’informations sur les limites de configuration du Cache Azure pour Redis, consultez la section [Configuration du serveur Redis par défaut](../cache-configure.md#default-redis-server-configuration).

Étant donné que la configuration et la gestion des instances de Cache Azure pour Redis sont gérées par Microsoft, toutes les commandes Redis ne sont pas prises en charge dans le Cache Azure pour Redis. Pour plus d’informations, consultez [Commandes Redis non prises en charge dans le Cache Azure pour Redis](../cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).
