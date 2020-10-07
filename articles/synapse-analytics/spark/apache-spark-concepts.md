---
title: Concepts de base d’Apache Spark
description: Introduction à Apache Spark dans Azure Synapse Analytics et aux différents concepts.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 74e85906742207d6cde0b7c4cc5c021c23ee4c7b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91260136"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark dans Azure Synapse Analytics - Concepts clés

Apache Spark est un framework de traitement parallèle qui prend en charge le traitement en mémoire pour améliorer les performances des applications d’analytique du Big Data. Apache Spark dans Azure Synapse Analytics est l’une des implémentations par Microsoft d’Apache Spark dans le cloud. 

Azure Synapse vous permet de créer et de configurer facilement des fonctionnalités Spark dans Azure. Azure Synapse fournit une implémentation différente de ces fonctionnalités Spark qui sont documentées ici.

## <a name="spark-pools-preview"></a>Pools Spark (préversion)

La création d’un pool Spark (préversion) s’effectue dans le portail Azure. Il s’agit de la définition d’un pool Spark qui, quand il est instancié, est utilisé pour créer une instance Spark qui traite des données. Quand un pool Spark est créé, il existe uniquement en tant que métadonnées, et aucune ressource n’est consommée, exécutée ou facturée. Un pool Spark possède une série de propriétés qui contrôlent les caractéristiques d’une instance Spark. Ces caractéristiques incluent entre autres le nom, la taille, le comportement de mise à l’échelle et la durée de vie.

Comme aucun coût en euro ou en ressource n’est associé à la création des pools Spark, vous pouvez en créer autant que vous voulez avec autant de configurations différentes que vous le souhaitez. Vous pouvez aussi appliquer des autorisations aux pools Spark, ce qui permet aux utilisateurs d’avoir uniquement accès à certains d’entre eux.

Une bonne pratique consiste à créer de petits pools Spark qui peuvent être utilisés pour le développement et le débogage, puis de plus grands pour l’exécution des charges de travail de production.

Pour découvrir comment créer un pool Spark et afficher toutes ses propriétés, consultez [Bien démarrer avec les pools Spark dans Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md).

## <a name="spark-instances"></a>Instances Spark

Des instances Spark sont créées quand vous vous connectez à un pool Spark, créez une session et exécutez un travail. Étant donné que plusieurs utilisateurs peuvent avoir accès à un même pool Spark, une nouvelle instance Spark est créée pour chaque utilisateur qui se connecte. 

Lorsque vous soumettez un deuxième travail, s’il y a la capacité dans le pool, l’instance Spark existante dispose également de la capacité. L’instance existante traitera donc la tâche. Dans le cas contraire, si la capacité est disponible au niveau du pool, une instance Spark sera créée.

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
- Vous soumettez à présent un autre travail, J2, qui utilise 10 nœuds, car il y a encore de la capacité dans le pool, et l’instance J2 est traitée par SI1.

## <a name="next-steps"></a>Étapes suivantes

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentation Apache Spark](https://spark.apache.org/docs/2.4.4/)
