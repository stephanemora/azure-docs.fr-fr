---
title: Mappage de flux de données de performances et réglage guide dans Azure Data Factory | Microsoft Docs
description: En savoir plus sur les facteurs clés qui affectent les performances de flux de données dans Azure Data Factory lorsque vous utilisez le mappage de flux de données.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 46be01c57be0e4f5fa74f8e8b0d91db3d78f441c
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480420"
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

## <a name="monitor-data-flow-performance"></a>Surveiller les performances de flux de données

Pendant la conception de vos données de mappage de flux dans le navigateur, vous pouvez test unitaire chaque transformation individuelle en cliquant sur l’onglet d’aperçu de données dans le volet de paramètres en bas de chaque transformation. L’étape suivante, que vous devez effectuer consiste à tester vos données flux de bout en bout dans le Concepteur de pipeline. Ajouter une activité d’exécution de flux de données et utilisez le bouton de débogage pour tester les performances de votre flux de données. Dans le volet inférieur de la fenêtre de pipeline, vous verrez une icône de lunettes sous « actions » :

![Analyse de flux de données](media/data-flow/mon002.png "moniteur 2 du flux de données")

Cliquez sur cette icône Afficher le plan d’exécution et le profil de performance suivants de votre flux de données. Vous pouvez utiliser ces informations pour évaluer les performances de votre flux de données avec des sources de données de taille différente. Notez que vous pouvez supposer que 1 minute de la moment de la configuration de cluster de l’exécution du travail dans vos calculs de performance globale et si vous utilisez la valeur par défaut du Runtime d’intégration Azure, vous devrez peut-être ajouter 5 minutes d’ainsi les temps d’accès de cluster.

![Analyse du flux de données](media/data-flow/mon003.png "moniteur 3 de flux de données")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optimisation de la base de données SQL Azure et Azure SQL Data Warehouse

![Partie source](media/data-flow/sourcepart2.png "partie Source")

### <a name="partition-your-source-data"></a>Partitionner vos données source

* Accédez à « Optimisation » et sélectionnez « Source ». Définir une colonne de table spécifique ou un type dans une requête.
* Si vous avez choisi « colonne », puis choisissez la colonne de partition.
* En outre, définissez le nombre maximal de connexions à votre base de données SQL Azure. Vous pouvez essayer un paramètre plus élevé pour obtenir des connexions parallèles à votre base de données. Toutefois, certains cas peuvent entraîner des performances plus rapides avec un nombre limité de connexions.
* Vos tables de base de données source n’avez pas besoin d’être partitionnées.
* La définition d’une requête dans votre transformation Source qui correspond au schéma de partitionnement de votre table de base de données permettra le moteur de base de données source tirer parti d’élimination de partition.
* Si votre source n’est pas déjà partitionné, ADF utiliseront toujours le partitionnement des données dans l’environnement de transformation Spark basé sur la clé que vous sélectionnez dans la transformation Source.

### <a name="set-batch-size-and-query-on-source"></a>Définir la taille du lot et requête sur la source

![Source](media/data-flow/source4.png "Source")

* Définir la taille de lot indiquera ADF pour stocker les données dans les jeux en mémoire au lieu de la ligne par ligne. Il s’agit d’un paramètre facultatif et vous pouvez exécuter plus de ressources sur les nœuds de calcul si elles ne sont pas dimensionnées correctement.
* Définition d’une requête peut vous permettre filtrer le droit des lignes à la source avant leur arrivée même pour les flux de données pour le traitement, ce qui peut rendre l’acquisition initiale des données plus rapide.
* Si vous utilisez une requête, vous pouvez ajouter des indicateurs de requête facultatif pour votre Azure SQL DB, par exemple, READ UNCOMMITTED

### <a name="set-sink-batch-size"></a>Définir la taille de lot de récepteur

![Récepteur](media/data-flow/sink4.png "récepteur")

* Afin d’éviter un traitement ligne par ligne de votre flux de données, définissez la « taille de lot » dans les paramètres de récepteur de base de données SQL Azure. Cela indiquera QU'ADF pour le processus de base de données écrit par lots en fonction de la taille fournie.

### <a name="set-partitioning-options-on-your-sink"></a>Définir les options sur votre récepteur de partitionnement

* Même si vous n’avez pas vos données partitionnées dans vos tables de base de données SQL Azure de destination, accédez à l’onglet de l’optimiser et partitionnement du jeu.
* Très souvent, signale simplement ADF à utiliser le partitionnement sur les clusters de l’exécution de Spark entraîne le chargement au lieu de forcer toutes les connexions à partir d’une nœud unique/partition de données beaucoup plus rapides de répétition alternée.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Augmentez la taille de votre moteur de calcul dans le Runtime d’intégration Azure

![Nouveau runtime d’intégration](media/data-flow/ir-new.png "nouveau runtime d’intégration")

* Augmenter le nombre de cœurs, ce qui augmentera le nombre de nœuds et vous fournir plus de puissance de traitement pour interroger et d’écrire dans votre base de données SQL Azure.
* Essayez les options « Calcul optimisé », « mémoire » à appliquer davantage de ressources à vos nœuds de calcul.

### <a name="unit-test-and-performance-test-with-debug"></a>Test unitaire et test de performances avec le débogage

* Lorsque unit test des flux de données, la valeur est le bouton « Données de flux de débogage » ON.
* Dans le Concepteur de flux de données, utilisez l’onglet Aperçu des données sur les transformations pour afficher les résultats de votre logique de transformation.
* Test unitaire vos données circulent à partir du Concepteur de pipeline en plaçant une activité de flux de données sur la conception de pipeline canevas et permet de tester le bouton « Debug ».
* Test en mode débogage fonctionnera dans un environnement de cluster chauffés en direct sans devoir attendre pour un cluster de juste-à-temps en place.

### <a name="disable-indexes-on-write"></a>Désactiver l’index lors de l’écriture
* Utiliser une activité de procédure stockée de pipeline ADF avant votre activité de flux de données qui désactive les index sur les tables cible qui sont écrits dans votre récepteur de.
* Après votre activité de flux de données, ajoutez une autre activité de procédure stockée qui activé ces index.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Augmenter la taille de votre base de données SQL Azure
* Planifier un redimensionnement de votre source et récepteur Azure SQL DB avant que votre limite de votre pipeline pour augmenter le débit et réduire la limitation Azure une fois que vous atteignez DTU l’exécution.
* Une fois terminée l’exécution de votre pipeline, vous pouvez redimensionner vos bases de données à leur fréquence d’exécution normale.

## <a name="optimizing-for-azure-sql-data-warehouse"></a>Optimisation pour Azure SQL Data Warehouse

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>Utiliser la mise en lots pour charger des données en bloc par le biais de Polybase

* Afin d’éviter un traitement ligne par ligne de votre flux de données, définissez l’option de « Intermédiaire » dans les paramètres de récepteur afin que ADF peut tirer parti de Polybase pour éviter les insertions de ligne par ligne dans l’entrepôt de données. Cela indiquera ADF pour utiliser Polybase afin que les données peuvent être chargées en bloc.
* Lorsque vous exécutez votre activité de flux de données à partir d’un pipeline, avec mise en lots est activé, vous devez sélectionner l’emplacement de magasin d’objets Blob de vos données de zone de transit pour le chargement en masse.

### <a name="increase-the-size-of-your-azure-sql-dw"></a>Augmenter la taille de votre entrepôt de données SQL Azure

* Planifier un redimensionnement de votre source et récepteur Azure SQL Data Warehouse avant d’exécuter votre pipeline pour augmenter le débit et réduire la limitation Azure une fois que vous atteignez les limites DWU.

* Une fois terminée l’exécution de votre pipeline, vous pouvez redimensionner vos bases de données à leur fréquence d’exécution normale.

## <a name="optimize-for-files"></a>Optimiser pour les fichiers

* Vous pouvez contrôler le nombre de partitions que ADF utilisera. Sur chaque transformation Source et récepteur, ainsi que chaque transformation individuelles, vous pouvez définir un schéma de partitionnement. Pour les fichiers plus petits, vous pouvez trouver la sélection « Partition unique » peut parfois fonctionne mieux et plus rapidement que si vous demandez de Spark pour partitionner vos fichiers de petite taille.
* Si vous n’avez pas suffisamment d’informations sur votre source de données, vous pouvez choisir « Tourniquet » de partitionnement et de définir le nombre de partitions.
* Si vous explorez vos données et que vous trouvez que des colonnes qui peuvent être des clés de hachage bon, utilisez l’option de partitionnement par hachage.

### <a name="file-naming-options"></a>Options d’affectation de noms de fichiers

* La nature de la valeur par défaut de l’écriture des données transformées dans le flux de données de mappage ADF consiste à écrire dans un jeu de données qui a un objet Blob ou le Service lié ADLS. Vous devez définir ce jeu de données pour pointer vers un dossier ou un conteneur, pas un fichier nommé.
* Utilisation de flux de données Azure Databricks Spark pour l’exécution, ce qui signifie que votre sortie sera réparti sur plusieurs fichiers basés sur soit par défaut Spark partitionnement ou le partitionnement de schéma que vous avez choisi explicitement.
* Une opération très courante dans les flux de données ADF consiste à choisir « Output à fichier unique » afin que tous vos fichiers de partie de sortie sont fusionnés dans un fichier de sortie unique.
* Toutefois, cette opération nécessite que la sortie est réduit à une partition unique sur un seul nœud du cluster.
* Gardez cela à l’esprit lorsque vous choisissez cette option populaires. Vous pouvez exécuter plus de ressources de nœud de cluster si vous combinez le nombre de fichiers source volumineux dans une partition de fichier de sortie unique.
* Pour éviter d’épuiser les ressources de nœud de calcul, vous pouvez conserver la valeur par défaut ou schéma de partitionnement explicite dans ADF, ce qui optimise les performances, et puis ajoutez une activité de copie suivantes dans le pipeline qui fusionne toutes de la partie des fichiers à partir du dossier de sortie pour un seul nouveau fichier. Pour l’essentiel, cette technique sépare l’action de transformation de fusion de fichier et obtenir le même résultat que la définition de « sortie à fichier unique ».

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres articles du flux de données :

- [Vue d’ensemble du flux de données](concepts-data-flow-overview.md)
- [Activité de flux de données](control-flow-execute-data-flow-activity.md)
- [Surveiller les performances de flux de données](concepts-data-flow-monitoring.md)
