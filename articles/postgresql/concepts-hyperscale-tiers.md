---
title: Niveau De base - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Le niveau de base d’un nœud unique pour Azure Database pour PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: b39ddb8841b5b9a1e0665c94776363162575ad53
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531967"
---
# <a name="basic-tier"></a>Niveau de base

Le niveau de base dans Azure Database pour PostgreSQL - Hyperscale (Citus) est un moyen simple de créer un petit groupe de serveurs que vous pouvez mettre à l’échelle ultérieurement. Alors que les groupes de serveurs au niveau standard ont un nœud coordinateur et au moins deux nœuds Worker, le niveau de base exécute tout ce qui se trouve dans un seul nœud de base de données.

Outre l’utilisation de moins de nœuds, le niveau de base offre toutes les fonctionnalités du niveau standard. Comme le niveau standard, il prend en charge la haute disponibilité, les réplicas de lecture et le stockage de tables en colonnes, parmi d’autres fonctionnalités.

## <a name="choosing-basic-vs-standard-tier"></a>Choix du niveau de base par rapport au niveau standard

Le niveau de base peut être une option de déploiement économique et pratique pour le développement, les tests et l’intégration continue initiaux. Elle utilise un nœud de base de données unique et présente la même API SQL que le niveau standard. Vous pouvez tester les applications avec le niveau de base et passer ultérieurement [au niveau standard](howto-hyperscale-scale-grow.md#add-worker-nodes) en sachant que l’interface reste la même.

Le niveau de base est également approprié pour les charges de travail plus petites en production. Il est possible de mettre à l’échelle verticalement *au sein du* niveau de base en augmentant le nombre de vCore de serveur.

Si une mise à l’échelle est nécessaire immédiatement, utilisez le niveau standard. Son plus petit groupe de serveurs autorisé possède un nœud coordinateur et deux Worker. Vous pouvez choisir d’utiliser plus de nœuds en fonction de votre cas d’utilisation, comme décrit dans notre guide pratique de [dimensionnement initial](howto-hyperscale-scale-initial.md) .

## <a name="next-steps"></a>Étapes suivantes

* Apprendre à [approvisionner le niveau de base](quickstart-create-hyperscale-basic-tier.md)
* Lorsque vous êtes prêt, consultez la section [Comment passer](howto-hyperscale-scale-grow.md#add-worker-nodes) du niveau de base au niveau standard
* L’option de [stockage en colonnes](concepts-hyperscale-columnar.md) est disponible dans les niveaux de base et standard
