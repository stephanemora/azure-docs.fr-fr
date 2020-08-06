---
title: Nœuds – Hyperscale (Citus) – Azure Database pour PostgreSQL
description: Découvrez les deux types de nœud, coordinateur et de travail, au sein d’un groupe de serveurs dans Azure Database pour PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: af743ca56572f507091db01f11d3283294a9e3d5
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87382795"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Nœuds dans Azure Database pour PostgreSQL - Hyperscale (Citus)

Le type d’hébergement Hyperscale (Citus) permet à Azure Database pour les serveurs PostgreSQL (appelés nœuds) de se coordonner entre eux dans une architecture dite « sans partage » (« shared nothing »). Collectivement, les nœuds dans un groupe de serveurs contiennent davantage de données et utilisent plus de cœurs d’UC que ce qui serait possible sur un seul serveur. L’architecture permet également à la base de données de procéder à la mise à l’échelle en ajoutant des nœuds supplémentaires au groupe de serveurs.

## <a name="coordinator-and-workers"></a>Coordinateur et travailleurs

Chaque groupe de serveurs dispose d’un nœud coordinateur et de plusieurs Workers. Les applications envoient leurs requêtes vers le nœud coordinateur, qui les relaie vers les Workers concernés et accumule leurs résultats. Les applications ne sont pas en mesure de se connecter directement aux Workers.

Hyperscale (CITUS) permet à l’administrateur de base de données de *distribuer* des tables, en stockant des différentes lignes sur différents nœuds Worker. Les tables distribuées sont la clé des performances d’Hyperscale. À défaut de distribuer les tables, celles-ci restent entièrement sur le nœud coordinateur et ne peut pas tirer parti du parallélisme entre ordinateurs.

Soit le coordinateur achemine chaque requête sur des tables distribuées vers un nœud Worker unique, soit il la met en parallèle sur plusieurs autres, en fonction de l’emplacement des données requises, sur un nœud unique ou sur plusieurs nœuds. Le coordinateur décide de la mesure à prendre en consultant les tables de métadonnées. Ces tables suivent les noms DNS et l’intégrité des nœuds Worker, ainsi que la distribution des données entre les nœuds.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [tables distribuées ](concepts-hyperscale-distributed-data.md)
