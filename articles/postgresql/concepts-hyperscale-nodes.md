---
title: Nœuds dans Azure Database pour PostgreSQL – très grande échelle (Citus) (version préliminaire)
description: Les deux types de nœuds dans un groupe de serveurs.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: c6b948ed63f43f1597103d123be5ed39f42bd276
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077274"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Nœuds dans Azure Database pour PostgreSQL – très grande échelle (Citus) (version préliminaire)

La très grande échelle (Citus) (version préliminaire) qui héberge le type permet de base de données Azure pour les serveurs PostgreSQL (appelés nœuds) pour coordonner entre eux dans une architecture « shared nothing ». Collectivement, les nœuds dans un groupe de serveurs contiennent davantage de données et utilisent plus de cœurs de processeur que serait possible sur un seul serveur. L’architecture permet également de la base de données à l’échelle en ajoutant des nœuds supplémentaires au groupe de serveurs.

## <a name="coordinator-and-workers"></a>Coordinateur et travailleurs

Chaque groupe de serveurs a un nœud coordinateur et plusieurs travailleurs. Les applications envoient leurs requêtes vers le nœud coordinateur, qui relaie aux travailleurs concernés et accumule leurs résultats. Les applications ne sont pas en mesure de se connecter directement aux travailleurs.

Pour chaque requête, le coordinateur achemine vers un nœud worker unique ou il parallélise sur plusieurs selon si les données requises se trouvent sur un nœud unique ou multiple. Le coordinateur décide ce qu’il faut faire en consultant les tables de métadonnées. Ces tables suivent les noms DNS et d’intégrité des nœuds de travail et la distribution des données entre les nœuds.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment stockent des nœuds [données distribuées](concepts-hyperscale-distributed-data.md)
