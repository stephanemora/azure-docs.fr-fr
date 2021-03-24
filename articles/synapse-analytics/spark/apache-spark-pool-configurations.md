---
title: Concepts concernant les pools Apache Spark
description: Présentation des tailles et configurations des pools Apache Spark dans Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 12/2/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: e68b8aff700519b6bef31e5126c91fa8bc4a3593
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103202459"
---
# <a name="apache-spark-pool-configurations-in-azure-synapse-analytics"></a>Configurations des pools Apache Spark dans Azure Synapse Analytics

Un pool Spark est un ensemble de métadonnées qui définit les besoins en ressources de calcul et les caractéristiques de comportement associées lorsqu’une instance Spark est instanciée. Ces caractéristiques incluent entre autres le nom, le nombre de nœuds, la taille de nœud, le comportement de mise à l’échelle et la durée de vie. Un pool Spark en lui-même ne consomme pas de ressources. La création de pools Spark est gratuite. Les frais ne sont facturés qu’une fois qu’un travail Spark est exécuté sur le pool Spark cible et que l’instance Spark est instanciée à la demande.

Pour découvrir comment créer un pool Spark et afficher toutes ses propriétés, consultez [Bien démarrer avec les pools Spark dans Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md).

## <a name="nodes"></a>Nœuds

Une instance de pool Apache Spark se compose d’un nœud principal et de deux nœuds Worker ou plus avec un minimum de trois nœuds dans une instance Spark.  Le nœud principal exécute des services de gestion supplémentaires tels que Livy, Yarn Resource Manager, Zookeeper et le pilote Spark.  Tous les nœuds exécutent des services tels que l’agent de nœud et Yarn Node Manager. Tous les nœuds Worker exécutent le service Spark Executor.

## <a name="node-sizes"></a>Tailles de nœuds

Un pool Spark peut être défini avec des tailles de nœuds allant d’un petit nœud de calcul avec 8 vCore et 64 Go de mémoire jusqu’à un nœud de calcul XXLarge avec 64 vCore et 432 Go de mémoire par nœud. Les tailles de nœud peuvent être modifiées après la création du pool bien que l’instance doive être redémarrée.

|Taille | vCore | Mémoire|
|-----|------|-------|
|Small|4|32 Go|
|Moyenne|8|64 Go|
|Large|16|128 Go|
|XLarge|32|256 Go|
|XXLarge|64|432 Go|

## <a name="autoscale"></a>Mise à l’échelle automatique

Les pools Apache Spark offrent la possibilité de mettre à l’échelle automatiquement les ressources de calcul en fonction de la quantité d’activité.  Lorsque la fonctionnalité de mise à l’échelle automatique est activée, vous pouvez définir le nombre minimal et maximal de nœuds à mettre à l’échelle.
Lorsque la fonctionnalité de mise à l’échelle automatique est désactivée, le nombre de nœuds définis reste fixe.  Ce paramètre peut être modifié après la création du pool bien que l’instance doive être redémarrée.

## <a name="automatic-pause"></a>Pause automatique

La fonctionnalité de pause automatique libère les ressources après une période d’inactivité définie, réduisant ainsi le coût global d’un pool Apache Spark.  Le nombre de minutes d’inactivité peut être défini une fois que cette fonctionnalité est activée.  La fonctionnalité de pause automatique est indépendante de la fonctionnalité de mise à l’échelle automatique. Les ressources peuvent être suspendues si la mise à l’échelle automatique est activée ou désactivée.  Ce paramètre peut être modifié après la création du pool bien que l’instance doive être redémarrée.

## <a name="next-steps"></a>Étapes suivantes

* [Azure Synapse Analytics](/azure/synapse-analytics)
* [Documentation Apache Spark](https://spark.apache.org/docs/2.4.5/)
