---
title: Mappage de flux de données de performances et réglage guide dans Azure Data Factory | Microsoft Docs
description: En savoir plus sur les facteurs clés qui affectent les performances de flux de données dans Azure Data Factory lorsque vous utilisez le mappage de flux de données.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 7fca586083f70e0b0f7e593d5203392260cd2136
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172339"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Mappage des performances de flux de données et guide d’optimisation

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory mappage de flux de données fournissent une interface de navigateur sans code pour concevoir, déployer et orchestrer des transformations de données à grande échelle.

> [!NOTE]
> Si vous n’êtes pas familiarisé avec les flux de données de mappage ADF en général, consultez [vue d’ensemble des flux de données](concepts-data-flow-overview.md) avant de lire cet article.
>

> [!NOTE]
> Lorsque vous concevez et testez des flux de données à partir de l’UI ADF, veillez à activer le commutateur de débogage afin que vous pouvez exécuter dans votre flux de données en temps réel sans attendre d’un cluster pour le préchauffage.
>

![Bouton de débogage](media/data-flow/debugb1.png "déboguer")

## <a name="optimizing-for-azure-sql-database"></a>Optimisation de la base de données SQL Azure

![Partie source](media/data-flow/sourcepart2.png "partie Source")

### <a name="you-can-match-spark-data-partitioning-to-your-source-database-partitioning-based-on-a-database-table-column-key-in-the-source-transformation"></a>Vous pouvez faire correspondre Spark partitionnement des données à votre partitionnement de base de données source sur une clé de colonne de table de base de données dans la transformation source

* Accédez à « Optimisation » et sélectionnez « Source ». Définir une colonne de table spécifique ou un type dans une requête.
* Si vous avez choisi « colonne », puis choisissez la colonne de partition.
* En outre, définissez le nombre maximal de connexions à votre base de données SQL Azure. Vous pouvez essayer un paramètre plus élevé pour obtenir des connexions parallèles à votre base de données. Toutefois, certains cas peuvent entraîner des performances plus rapides avec un nombre limité de connexions.

### <a name="set-batch-size-and-query-on-source"></a>Définir la taille du lot et requête sur la source

![Source](media/data-flow/source4.png "Source")

* Définir la taille de lot indiquera ADF pour stocker les données dans les jeux en mémoire au lieu de la ligne par ligne. Il s’agit d’un paramètre facultatif et vous pouvez exécuter plus de ressources sur les nœuds de calcul si elles ne sont pas dimensionnées correctement.
* Définition d’une requête peut vous permettre filtrer le droit des lignes à la source avant leur arrivée même pour les flux de données pour le traitement, ce qui peut rendre l’acquisition initiale des données plus rapide.
* Si vous utilisez une requête, vous pouvez ajouter des indicateurs de requête facultatif pour votre Azure SQL DB, par exemple, READ UNCOMMITTED

### <a name="set-sink-batch-size"></a>Définir la taille de lot de récepteur

![Récepteur](media/data-flow/sink4.png "récepteur")

* Afin d’éviter un traitement ligne par ligne de votre floes de données, définissez la « taille de lot » dans les paramètres de récepteur de base de données SQL Azure. Cela indiquera QU'ADF pour le processus de base de données écrit par lots en fonction de la taille fournie.

### <a name="set-partitioning-options-on-your-sink"></a>Définir les options sur votre récepteur de partitionnement

* Même si vous n’avez pas vos données partitionnées dans vos tables de base de données SQL Azure de destination, accédez à l’onglet de l’optimiser et partitionnement du jeu.
* Très souvent, signale simplement ADF à utiliser le partitionnement sur les clusters de l’exécution de Spark entraîne le chargement au lieu de forcer toutes les connexions à partir d’une nœud unique/partition de données beaucoup plus rapides de répétition alternée.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Augmentez la taille de votre moteur de calcul dans le Runtime d’intégration Azure

![Nouveau runtime d’intégration](media/data-flow/ir-new.png "nouveau runtime d’intégration")

* Augmenter le nombre de cœurs, ce qui augmentera le nombre de nœuds et vous fournir plus de puissance de traitement pour interroger et d’écrire dans votre base de données SQL Azure.
* Essayez les options « Calcul optimisé », « mémoire » à appliquer davantage de ressources à vos nœuds de calcul.

### <a name="disable-indexes-on-write"></a>Désactiver l’index lors de l’écriture
* Utiliser une activité de procédure stockée de pipeline ADF avant votre activité de flux de données qui désactive les index sur les tables cible qui sont écrits dans votre récepteur de.
* Après votre activité de flux de données, ajoutez une autre activité de procédure stockée qui activé ces index.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Augmenter la taille de votre base de données SQL Azure
* Planifier un redimensionnement de votre source et récepteur Azure SQL DB avant que votre limite de votre pipeline pour augmenter le débit et réduire la limitation Azure une fois que vous atteignez DTU l’exécution.
* Une fois terminée l’exécution de votre pipeline, vous pouvez redimensionner vos bases de données à leur fréquence d’exécution normale.

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres articles du flux de données :

- [Vue d’ensemble du flux de données](concepts-data-flow-overview.md)
- [Activité de flux de données](control-flow-execute-data-flow-activity.md)

