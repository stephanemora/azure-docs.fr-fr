---
title: Nœuds dans Azure Database pour PostgreSQL - Hyperscale (Citus) (préversion)
description: Les deux types de nœuds possibles dans un groupe de serveurs.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: c6b948ed63f43f1597103d123be5ed39f42bd276
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077274"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Nœuds dans Azure Database pour PostgreSQL - Hyperscale (Citus) (préversion)

Le type d’hébergement Hyperscale (Citus) (préversion) permet à Azure Database pour les serveurs PostgreSQL (appelés nœuds) de se coordonner entre eux dans une architecture dite « sans partage » (« shared nothing »). Collectivement, les nœuds dans un groupe de serveurs contiennent davantage de données et utilisent plus de cœurs d’UC que ce qui serait possible sur un seul serveur. L’architecture permet également à la base de données de procéder à la mise à l’échelle en ajoutant des nœuds supplémentaires au groupe de serveurs.

## <a name="coordinator-and-workers"></a>Coordinateur et travailleurs

Chaque groupe de serveurs dispose d’un nœud coordinateur et de plusieurs Workers. Les applications envoient leurs requêtes vers le nœud coordinateur, qui les relaie vers les Workers concernés et accumule leurs résultats. Les applications ne sont pas en mesure de se connecter directement aux Workers.

Soit le coordinateur achemine chaque requête vers un nœud Worker unique, soit il la met en parallèle sur plusieurs d’entre eux, en fonction de l’emplacement des données requises, sur un nœud unique ou sur plusieurs nœuds. Le coordinateur décide de la mesure à prendre en consultant les tables de métadonnées. Ces tables suivent les noms DNS et l’intégrité des nœuds Worker, ainsi que la distribution des données entre les nœuds.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment les nœuds stockent les [données distribuées](concepts-hyperscale-distributed-data.md)
