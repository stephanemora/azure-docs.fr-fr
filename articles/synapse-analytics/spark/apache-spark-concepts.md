---
title: Apache Spark dans Azure Synapse Analytics - Concepts clés
description: Cet article présente Apache Spark dans Azure Synapse Analytics et les différents concepts.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: b1db306ffdb1c05c880e5fc639de2cc1db130d8e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82096280"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark dans Azure Synapse Analytics - Concepts clés

Apache Spark est un framework de traitement parallèle qui prend en charge le traitement en mémoire pour améliorer les performances des applications d’analytique du Big Data. Apache Spark dans Azure Synapse Analytics est l’une des implémentations par Microsoft d’Apache Spark dans le cloud. 

Azure Synapse vous permet de créer et de configurer facilement des fonctionnalités Spark dans Azure. Azure Synapse fournit une implémentation différente de ces fonctionnalités Spark qui sont documentées ici.

## <a name="spark-pools-preview"></a>Pools Spark (préversion)

La création d’un pool Spark (préversion) s’effectue dans le portail Azure. Il s’agit de la définition d’un pool Spark qui, quand il est instancié, est utilisé pour créer une instance Spark qui traite des données. Quand un pool Spark est créé, il existe uniquement en tant que métadonnées ; aucune ressource n’est consommée, exécutée ou facturée. Un pool Spark a une série de propriétés qui contrôlent les caractéristiques d’une instance Spark ; ces caractéristiques incluent notamment le nom, la taille, le comportement de mise à l’échelle et la durée de vie.

Comme aucun coût en euro ou en ressource n’est associé à la création des pools Spark, vous pouvez en créer autant que vous voulez avec autant de configurations différentes que vous le souhaitez. Vous pouvez aussi appliquer des autorisations aux pools Spark, ce qui permet aux utilisateurs d’avoir uniquement accès à certains d’entre eux.

Une bonne pratique consiste à créer de petits pools Spark qui peuvent être utilisés pour le développement et le débogage, puis de plus grands pour l’exécution des charges de travail de production.

Pour découvrir comment créer un pool Spark et afficher toutes ses propriétés, consultez [Bien démarrer avec les pools Spark dans Synapse Analytics](../quickstart-create-apache-spark-pool.md).

## <a name="spark-instances"></a>Instances Spark

Des instances Spark sont créées quand vous vous connectez à un pool Spark, créez une session et exécutez un travail. Étant donné que plusieurs utilisateurs peuvent avoir accès à un même pool Spark, une nouvelle instance Spark est créée pour chaque utilisateur qui se connecte. 

Quand vous soumettez un deuxième travail, si la capacité du pool est suffisante, l’instance Spark existante a également une capacité ; elle traitera donc le travail. Si ce n’est pas le cas et qu’il existe une capacité au niveau du pool, une nouvelle instance Spark sera créée.

## <a name="examples"></a>Exemples

### <a name="example-1"></a>Exemple 1

- Vous créez un pool Spark appelé SP1 ; Il a une taille de cluster fixe de 20 nœuds.
- Vous soumettez un travail de notebook, J1, qui utilise 10 nœuds. Une instance Spark, SI1, est créée pour traiter le travail.
- Vous soumettez à présent un autre travail, J2, qui utilise 10 nœuds. Comme il y a encore une capacité dans le pool, l’instance J2 est traitée par SI1.
- Si J2 avait demandé 11 nœuds, la capacité aurait été insuffisante dans SP1 ou SI1. Dans le cas présent, si J2 provient d’un notebook, le travail sera rejeté. Si J2 provient d’un traitement par lots, il sera mis en file d’attente.

### <a name="example-2"></a>Exemple 2

- Vous créez un appel de pool Spark SP2 ; la mise à l’échelle automatique est activée pour 10 à 20 nœuds.
- Vous soumettez un travail de notebook, J1, qui utilise 10 nœuds. Une instance Spark, SI1, est créée pour traiter le travail.
- Vous soumettez maintenant un autre travail, J2, qui utilise 10 nœuds. Comme il y a encore une capacité dans le pool, l’instance augmente automatiquement à 20 nœuds et traite J2.

### <a name="example-3"></a>Exemple 3

- Vous créez un pool Spark appelé SP1 ; Il a une taille de cluster fixe de 20 nœuds.
- Vous soumettez un travail de notebook, J1, qui utilise 10 nœuds. Une instance Spark, SI1, est créée pour traiter le travail.
- Un autre utilisateur, U2, soumet un travail, J3, qui utilise 10 nœuds. Une nouvelle instance Spark, SI2, est créée pour traiter le travail.
- Vous soumettez à présent un autre travail, J2, qui utilise 10 nœuds. Comme il y a encore une capacité dans le pool, l’instance J2 est traitée par SI1.

## <a name="next-steps"></a>Étapes suivantes

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentation Apache Spark](https://spark.apache.org/docs/2.4.4/)
