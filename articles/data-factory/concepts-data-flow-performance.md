---
title: Guide des performances et du réglage du mappage de flux de données dans Azure Data Factory | Microsoft Docs
description: En savoir plus sur les facteurs clés ayant des répercussions sur les performances des flux de données dans Azure Data Factory lorsque vous utilisez le mappage de flux de données.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: bbbc2bc5c47821469ecf15a27195b1bf0c12e6e5
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190623"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Guide des performances et du réglage du mappage de flux de données

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Le mappage de flux de données Azure Data Factory fournit une interface de navigateur sans code pour concevoir, déployer et orchestrer des transformations de données à l’échelle.

> [!NOTE]
> Si vous n’êtes pas familiarisé avec le mappage de flux de données ADF, consultez [Vue d’ensemble des flux de données](concepts-data-flow-overview.md) avant de lire cet article.
>

> [!NOTE]
> Lorsque vous concevez et testez des flux de données à partir de l’interface utilisateur ADF, veillez à activer le commutateur de débogage de façon à pouvoir exécuter vos flux de données en temps réel sans attendre le préchauffage d’un cluster.
>

![Bouton de débogage](media/data-flow/debugb1.png "Déboguer")

## <a name="monitor-data-flow-performance"></a>Analyser les performances des flux de données

Pendant la conception de vos flux de données de mappage dans le navigateur, vous pouvez effectuer un test unitaire de chaque transformation individuelle en cliquant sur l’onglet d’aperçu des données dans le volet inférieur des paramètres de chaque transformation. L’étape suivante que vous devriez effectuer consiste à tester votre flux de données de bout en bout dans le concepteur de pipeline. Ajoutez une activité Exécuter Data Flow et utilisez le bouton Déboguer pour tester les performances de votre flux de données. Dans le volet inférieur de la fenêtre de pipeline, vous voyez une icône de lunettes sous « actions » :

![Analyse de flux de données](media/data-flow/mon002.png "Analyse de flux de données 2")

Cliquez sur cette icône pour afficher le plan d'exécution et le profil de performance de votre flux de données qui en résulte. Vous pouvez utiliser ces informations pour évaluer les performances de votre flux de données avec des sources de données de tailles différentes. Notez que vous pouvez vous baser sur 1 minute d’exécution du travail de cluster pour l’ensemble de vos calculs de performance et que, si vous utilisez la valeur par défaut d’Azure Integration Runtime, vous devrez peut-être ajouter 5 minutes pour la mise en place du cluster.

![Analyse de flux de données](media/data-flow/mon003.png "Analyse de flux de données 3")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optimisation pour Azure SQL Database et Azure SQL Data Warehouse

![Partie source](media/data-flow/sourcepart3.png "Partie Source")

### <a name="partition-your-source-data"></a>Partitionner vos données sources

* Accédez à « Optimiser » et sélectionnez « Source ». Définissez une colonne de table spécifique ou un type dans une requête.
* Si vous avez choisi « colonne », choisissez la colonne de partition.
* Définissez également le nombre maximum de connexions à votre base de données Azure SQL. Vous pouvez essayer d’utiliser un paramètre plus élevé pour obtenir des connexions parallèles à votre base de données. Toutefois, dans certains cas ceci peut entraîner des performances plus rapides avec un nombre limité de connexions.
* Vos tables de base de données sources n’ont pas besoin d’être partitionnées.
* La définition d’une requête dans votre transformation source qui correspond au schéma de partitionnement de votre table de base de données permettra au moteur de base de données source de tirer parti de la suppression de la partition.
* Si votre source n’est pas encore partitionnée, ADF utilise toujours le partitionnement des données dans l’environnement de transformation Spark basé sur la clé sélectionnée dans la transformation source.

### <a name="set-batch-size-and-query-on-source"></a>Définissez la taille de lot et la requête sur la source

![Source](media/data-flow/source4.png "Source")

* La définition de la taille de lot indique à ADF qu’elle doit stocker les données dans les ensembles en mémoire et non pas ligne par ligne. Il s’agit d’un paramètre facultatif. Il est possible que vous manquiez de ressources sur les nœuds de calcul s’il est mal dimensionné.
* La définition d’une requête peut vous permettre de filtrer des lignes à la source avant même qu’elles n’arrivent dans le flux de données pour être traitées, ce qui peut accélérer l’acquisition initiale des données.
* Si vous utilisez une requête, vous pouvez ajouter des indicateurs de requêtes facultatifs pour votre base de données Azure SQL, c’est à dire READ UNCOMMITTED.

### <a name="set-isolation-level-on-source-transformation-settings-for-sql-datasets"></a>Définir le niveau d’isolement sur les paramètres de transformation de source pour les jeux de données SQL.

* La lecture non validée fournira des résultats plus rapides suite à une requête concernant la transformation de la source.

![Niveau d’isolement](media/data-flow/isolationlevel.png "Niveau d’isolement")

### <a name="set-sink-batch-size"></a>Définir la taille de lot du récepteur

![Récepteur](media/data-flow/sink4.png "Récepteur")

* Afin d’éviter un traitement ligne par ligne de vos flux de données, définissez la « taille de lot » dans les paramètres du récepteur de la base de données Azure SQL. Ceci indique à ADF qu’elle doit traiter les écritures de base de données par lots en fonction de la taille fournie.

### <a name="set-partitioning-options-on-your-sink"></a>Définissez des options de partitionnement sur votre récepteur

* Même si vos données ne sont pas partitionnées dans vos tables de base de données Azure SQL de destination, accédez à l’onglet Optimiser et définissez le partitionnement.
* Très souvent, le simple fait d’indiquer à ADF qu’elle doit utiliser le partitionnement en tourniquet (round robin) sur les clusters d’exécution Spark entraîne un chargement beaucoup plus rapide des données au lieu de forcer toutes les connexions à partir d’un nœud/d’une partition uniques.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Augmenter la taille de votre moteur de calcul dans Azure Integration Runtime

![Nouveau runtime d’intégration](media/data-flow/ir-new.png "Nouveau runtime d’intégration")

* Augmentez le nombre de cœurs, ce qui augmentera le nombre de nœuds et vous fournira plus de puissance de traitement pour interroger et d’écrire dans votre base de données SQL Azure.
* Essayez les options « Optimisé pour le calcul » et « À mémoire optimisée » pour appliquer davantage de ressources à vos nœuds de calcul.

### <a name="unit-test-and-performance-test-with-debug"></a>Test unitaire et test de performances avec le débogage

* Lors du test unitaire des flux de données, mettez le bouton « Déboguer le flux de données » sur ACTIVÉ.
* Au sein du concepteur de flux de données, utilisez l’onglet Aperçu des données sur les transformations pour afficher les résultats de votre logique de transformation.
* Effectuez un test unitaire de vos flux de données à partir du concepteur de pipeline en plaçant une activité de flux de données sur le canevas de conception de pipeline, et utilisez le bouton « Déboguer » pour tester.
* Les tests en mode de débogage fonctionneront dans un environnement de cluster chauffé en direct sans devoir attendre un préchauffage juste-à-temps du cluster.

### <a name="disable-indexes-on-write"></a>Désactivez les index lors de l’écriture
* Utilisez une activité de procédure stockée du pipeline ADF avant votre activité de flux de données qui désactive les index sur vos tables cibles lors de l’écriture sur ces dernières à partir de votre récepteur.
* Après votre activité de flux de données, ajoutez une autre activité de traitement stockée qui a activé ces index.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Augmentez la taille de votre base de données SQL Azure
* Planifiez un redimensionnement de votre base de données SQL Azure de source et de récepteur Azure avant d’exécuter votre pipeline pour augmenter le débit et réduire la limitation de requêtes Azure une fois que vous aurez atteint les limites DTU.
* Une fois l’exécution de votre pipeline terminée, vous pouvez redimensionner vos bases de données à leur fréquence d’exécution normale.

## <a name="optimizing-for-azure-sql-data-warehouse"></a>Optimisation pour Azure SQL Data Warehouse

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>Utilisez la mise en lots pour charger des données en bloc par le biais de Polybase

* Pour éviter un traitement ligne par ligne de vos flux de données, définissez l’option « Mise en lots » dans les paramètres de récepteur de telle manière qu’ADF puisse tirer parti de Polybase pour éviter les insertions ligne par ligne dans l’entrepôt de données. Ceci indiquera à ADF qu’elle doit utiliser Polybase afin que ces données puissent être chargées en bloc.
* Lorsque vous exécutez votre activité de flux de données à partir d’un pipeline avec la mise en lots activée, vous devez sélectionner l’emplacement de magasin d’objets Blob de vos données mises en lots pour le chargement en masse.

### <a name="increase-the-size-of-your-azure-sql-dw"></a>Augmentez la taille de votre entrepôt de données SQL Azure

* Planifiez un redimensionnement de votre entrepôt de données SQL Azure de source et de récepteur Azure avant d’exécuter votre pipeline pour augmenter le débit et réduire la limitation de requêtes Azure une fois que vous aurez atteint les limites DWU.

* Une fois l’exécution de votre pipeline terminée, vous pouvez redimensionner vos bases de données à leur fréquence d’exécution normale.

## <a name="optimize-for-files"></a>Optimiser les fichiers

* Vous pouvez contrôler le nombre de partitions qu’ADF utilisera. Sur chaque transformation de source et de récepteur, ainsi que sur chaque transformation individuelle, vous pouvez définir un schéma de partitionnement. Pour les fichiers plus petits, vous pouvez trouver que sélectionner « Partition unique » fonctionne parfois mieux et plus rapidement que si vous demandez à Spark de partitionner vos fichiers de petite taille.
* Si vous n’avez pas suffisamment d’informations sur vos données sources, vous pouvez choisir le partitionnement en tourniquet (round robin) et définir le nombre de partitions.
* Si vous explorez vos données et que vous trouvez que vous avez des colonnes qui feraient de bonnes clés de code de hachage, utilisez l’option Partitionnement de hachage.

### <a name="file-naming-options"></a>Options d’attribution de noms de fichiers

* La nature de la valeur par défaut de l’écriture de données transformées dans le mappage des flux de données d’ADF consiste à écrire dans un jeu de données qui a un objet Blob ou un service lié ADLS. Vous devez définir ce jeu de données de manière qu’il pointe vers un dossier ou un conteneur, pas vers un fichier nommé.
* Les flux de données utilisent Azure Databricks Spark pour l’exécution, ce qui signifie que votre sortie sera réparti sur plusieurs fichiers basés soit sur le partitionnement Spark par défaut, soit sur le schéma de partitionnement que vous avez explicitement choisi.
* Une opération très courante dans les flux de données ADF consiste à choisir « Sortie vers un fichier unique » afin que tous vos fichiers de partie de sortie soient fusionnés dans un fichier de sortie unique.
* Toutefois, cette opération nécessite que la sortie soit réduite à une partition unique sur un seul nœud de cluster.
* Pensez-y lorsque vous choisissez cette option populaire. Il est possible que vous n’ayez plus de ressources de nœud de cluster si vous combinez de nombreux fichiers sources dans un seul partitionnement de fichier de sortie.
* Pour éviter d’épuiser les ressources de nœud de calcul, vous pouvez conserver le schéma par défaut ou le schéma de partitionnement explicite dans ADF, ce qui optimise les performances, puis ajouter une activité de copie suivante dans le pipeline qui fusionne tous les fichiers de PARTIE du dossier de sortie vers un nouveau fichier unique. Pour l’essentiel, cette technique sépare l’action de transformation de la fusion de fichiers et donne le même résultat que la définition sur « sortir dans un fichier unique ».

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres articles sur les flux de données consacrés aux performances :

- [Onglet Optimiser le flux de données](concepts-data-flow-optimize-tab.md)
- [Activité Data Flow](control-flow-execute-data-flow-activity.md)
- [Analyser les performances des flux de données](concepts-data-flow-monitoring.md)
