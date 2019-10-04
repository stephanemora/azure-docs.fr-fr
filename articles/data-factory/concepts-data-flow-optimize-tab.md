---
title: Onglet Optimiser de la fonctionnalité de mappage de flux de données d’Azure Data Factory
description: Optimiser le mappage de flux de données d’Azure Data Factory à l’aide de l’onglet Optimiser avec les paramètres de partition
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 40023931b2a0b3788a583a5b5240e7916b187e34
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190643"
---
# <a name="mapping-data-flow-transformation-optimize-tab"></a>Onglet Optimiser de la fonctionnalité de transformation du mappage de flux de données

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Chaque transformation de flux de données comporte un onglet « Optimiser ». L’onglet Optimiser contient des paramètres facultatifs pour configurer des schémas de partition pour des flux de données.

<img src="media/data-flow/opt001.png" width="800">

Le paramétrage par défaut est d’utiliser le partitionnement actuel. Le partitionnement actuel donne pour instruction à Azure Data Factory d’utiliser le schéma de partition natif pour les flux de données s’exécutant sur Spark dans Azure Databricks. Il s’agit de l’approche généralement recommandée.

Toutefois, il existe des instances où vous pouvez souhaiter ajuster le partitionnement. Par exemple, si vous souhaitez acheminer vos transformations vers un seul fichier dans le lac de données, choisissez « partition unique » sous l’onglet Optimiser pour le partitionnement dans la transformation du récepteur.

Un autre cas où vous pouvez souhaiter exercer un contrôle sur les schémas de partition utilisés pour vos transformations de données a trait aux performances. L’ajustement du partitionnement des données permet de contrôler la répartition de celles-ci entre les nœuds de calcul, ainsi que les optimisations de la localisation des données, qui peuvent avoir des effets tant positifs que négatifs sur les performances globales du flux de données.

Si vous souhaitez modifier le partitionnement de toute transformation, cliquez simplement sur l’onglet Optimiser, puis activez la case d’option « Définir le partitionnement ». Vous obtenez alors une série d’options pour le partitionnement. La meilleure méthode de partitionnement à implémenter varie en fonction des volumes de données, des clés candidates, des valeurs null et de la cardinalité. La meilleure pratique consiste à commencer avec le partitionnement par défaut, puis à essayer les différentes options de partitionnement. Vous pouvez effectuer un test en utilisant le débogage dans le pipeline pour voir le temps passé à chaque regroupement de transformations, ainsi que l’utilisation de la partition dans l’affichage Supervision.

<img src="media/data-flow/opt002.png" width="600">

### <a name="round-robin"></a>Tourniquet

Un tourniquet (round robin) est une partition simple qui distribue automatiquement les données de façon uniforme sur les partitions. Utilisez un tourniquet lorsque vous n’avez pas de bonnes clés candidates pour implémenter une stratégie de partitionnement solide et intelligente. Vous pouvez définir le nombre de partitions physiques.

### <a name="hash"></a>Hachage

Azure Data Factory produit un hachage de colonnes pour produire des partitions uniformes de sorte que des lignes contenant des valeurs similaires tombent dans la même partition. Lorsque vous utilisez l’option Hachage, effectuez un test pour détecter une éventuelle inclinaison de partition. Vous pouvez définir le nombre de partitions physiques.

### <a name="dynamic-range"></a>Plage dynamique

Une plage dynamique utilise des plages dynamiques Spark basées sur les colonnes ou expressions que vous fournissez. Vous pouvez définir le nombre de partitions physiques. 

### <a name="fixed-range"></a>Plage fixe

Vous devez créer une expression qui fournit une plage fixe pour les valeurs figurant dans vos colonnes de données partitionnées. Avant d’utiliser cette option afin d’éviter une inclinaison de partition, vous devez avoir une bonne compréhension de vos données. Les valeurs que vous entrez pour l’expression seront utilisées dans une fonction de partition. Vous pouvez définir le nombre de partitions physiques.

### <a name="key"></a>Clé

Si vous avez une bonne compréhension de la cardinalité de vos données, une clé de partitionnement peut être une bonne stratégie de partition. Une clé de partitionnement crée des partitions pour chaque valeur unique dans votre colonne. Vous ne pouvez pas définir le nombre de partitions, car celui-ci dépend des valeurs uniques dans les données.

## <a name="next-steps"></a>Étapes suivantes

[Guide des performances de mappage des flux de données](concepts-data-flow-performance.md)
[Supervision du flux de données](concepts-data-flow-monitoring.md)
