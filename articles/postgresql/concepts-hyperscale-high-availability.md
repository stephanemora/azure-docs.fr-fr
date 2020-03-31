---
title: Haute disponibilité – Hyperscale (Citus) – Azure Database pour PostgreSQL
description: Concepts de haute disponibilité et de reprise d’activité après sinistre
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975531"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Haute disponibilité dans Azure Database pour PostgreSQL - Hyperscale (Citus)

La haute disponibilité évite les temps d’arrêt des bases de données en conservant des réplicas de secours de chaque nœud dans un groupe de serveurs. Si un nœud devient inactif, Hyperscale bascule les connexions entrantes du nœud défaillant vers son nœud de secours. Le basculement se produit en quelques minutes, et les nœuds promus disposent toujours de données actualisées par le biais de la réplication en streaming synchrone PostgreSQL.

Pour tirer parti de la haute disponibilité sur le nœud coordinateur, les applications de base de données doivent détecter et retenter les connexions abandonnées et les transactions ayant échoué. Le coordinateur nouvellement promu sera accessible avec la même chaîne de connexion.

La reprise peut être divisée en trois étapes : la détection, le basculement et la reprise complète.  Hyperscale exécute des contrôles d’intégrité réguliers sur chaque nœud, et après quatre échecs du contrôle, il détermine qu’un nœud est inactif. Hyperscale promeut ensuite un nœud de secours à l’état de nœud principal (basculement), puis provisionne un nouveau nœud de secours.
La réplication en streaming commence, mettant à jour le nouveau nœud.  Une fois toutes les données répliquées, le nœud a atteint la reprise complète.

### <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [activer la haute disponibilité](howto-hyperscale-high-availability.md) dans un groupe de serveurs Hyperscale.
