---
title: Taille initiale d’un groupe de serveurs – Hyperscale (Citus) – Azure Database pour PostgreSQL
description: Choisissez la taille initiale appropriée pour votre cas d’usage.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 08/03/2021
ms.openlocfilehash: 22a0fb4d06194138d5169df5005d5a23b3580569
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531551"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Choix de la taille initiale pour le groupe de serveurs Hyperscale (Citus)

La taille d’un groupe de serveurs (nombre de nœuds et capacité matérielle) est [facile à modifier](howto-hyperscale-scale-grow.md). Toutefois, vous devez choisir une taille initiale pour un nouveau groupe de serveurs. Voici quelques conseils pour effectuer un choix raisonnable.

## <a name="use-cases"></a>Cas d’usage

Hyperscale (Citus) est fréquemment utilisé de la façon suivante.

### <a name="multi-tenant-saas"></a>SaaS mutualisé

En cas de migration vers Hyperscale (Citus) à partir d’une instance de base de données PostgreSQL à nœud unique existante, choisissez un cluster dont le nombre de vCore Worker et la mémoire RAM sont au total égaux à ceux de l’instance d’origine. Dans de tels scénarios, nous avons constaté que les performances ont doublé, voire triplé, car le partitionnement améliore l’utilisation des ressources, ce qui permet des index plus petits, etc.

Le nombre de vCore constitue en fait la seule décision. L’allocation de RAM est actuellement déterminée en fonction du nombre de vCore (cf. page [Options de configuration Hyperscale (Citus)](concepts-hyperscale-configuration-options.md)).
Le nœud coordinateur n’a pas besoin d’autant de RAM que les nœuds Worker, mais il n’existe aucun moyen de choisir la RAM et les vCore indépendamment.

### <a name="real-time-analytics"></a>Analyse en temps réel

Nombre total de vCores : quand les données de travail tiennent dans la mémoire RAM, vous pouvez vous attendre à une amélioration linéaire des performances proportionnelle au nombre de cœurs Worker sur Hyperscale (Citus). Pour déterminer le nombre de vCores adapté à vos besoins, tenez compte de la latence actuelle des requêtes dans votre base de données à nœud unique et de la latence nécessaire dans Hyperscale (Citus). Divisez la latence actuelle par la latence souhaitée, puis arrondissez le résultat.

RAM worker : le meilleur des cas serait de fournir suffisamment de mémoire pour que la majorité de la plage de travail tienne dans la mémoire. Le type de requêtes que votre application utilise affecte les besoins en mémoire. Vous pouvez exécuter EXPLAIN ANALYZE sur une requête pour déterminer la quantité de mémoire nécessaire. N’oubliez pas que les vCores et la RAM sont mis à l’échelle ensemble, comme décrit dans l’article sur les [options de configuration Hyperscale (Citus)](concepts-hyperscale-configuration-options.md).

## <a name="choosing-a-hyperscale-citus-tier"></a>Choix d’un niveau Hyperscale (Citus)

Les sections ci-dessus donnent une idée du nombre de vCores et de la quantité de RAM nécessaires pour chaque cas d’usage. Vous pouvez répondre à ces exigences à l’aide d’un choix entre deux niveaux Hyperscale (Citus) : le niveau de base et le niveau standard.

Le niveau de base utilise un seul nœud de base de données pour effectuer le traitement, tandis que le niveau standard autorise davantage de nœuds. À part cela, les niveaux sont identiques et offrent les mêmes fonctionnalités. Dans certains cas, les vCores et l’espace disque d’un seul nœud peuvent être mis à l’échelle de façon suffisante et, dans d’autres cas, ils nécessitent la collaboration de plusieurs nœuds.

Pour une comparaison des niveaux, consultez la page des concepts du [niveau de base](concepts-hyperscale-tiers.md).

## <a name="next-steps"></a>Étapes suivantes

- [Mettre à l’échelle un groupe de serveurs](howto-hyperscale-scale-grow.md)
- Apprenez-en davantage sur les [options de performances](concepts-hyperscale-configuration-options.md) du groupe de serveurs.
