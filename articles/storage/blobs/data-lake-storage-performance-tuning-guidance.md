---
title: Optimiser Azure Data Lake Storage Gen2 pour les performances | Microsoft Docs
description: Découvrez comment optimiser les performances d’Azure Data Lake Storage Gen2. Ingérez des données, structurez votre jeu de données, et bien plus encore.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 09/21/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3cb875f14fe3d9b18b5249a9d2cd9e1901000610
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609305"
---
# <a name="optimize-azure-data-lake-storage-gen2-for-performance"></a>Optimiser Azure Data Lake Storage Gen2 pour les performances

Azure Data Lake Storage Gen2 prend en charge un débit élevé pour l’analytique intensive des E/S et le déplacement des données. Cet article vous aide à optimiser le débit et l’accès efficace aux données.

> [!NOTE]
> Les performances globales de votre pipeline d’analyse dépendent également de facteurs spécifiques aux moteurs d’analyse. Pour obtenir les meilleurs conseils à jour sur l’optimisation des performances de la charge de travail, consultez la documentation de chaque système que vous prévoyez d’utiliser.

## <a name="optimize-data-ingestion"></a>Optimiser l’ingestion des données

Lors de l’ingestion de données à partir d’un système source, le matériel source, le matériel réseau source ou la connectivité réseau à votre compte de stockage peut être un goulot d’étranglement.

![Diagramme montrant les facteurs à prendre en compte lors de l’ingestion de données d’un système source dans Data Lake Storage Gen2.](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

### <a name="source-hardware"></a>Matériel source

Que vous utilisiez des machines locales ou des machines virtuelles dans Azure, veillez à sélectionner soigneusement le matériel approprié. Pour le matériel de disque, envisagez d’utiliser des disques SSD (Solid State Drive) et de sélectionner le matériel de disque avec des unités plus rapides. Pour le matériel réseau, utilisez les contrôleurs d’interface réseau (NIC) les plus rapides possible. Sur Azure, nous vous recommandons des machines virtuelles Azure D14 qui sont équipées du disque et du matériel de mise en réseau suffisamment puissants.

### <a name="network-connectivity-to-the-storage-account"></a>Vérification de la connectivité réseau au compte de stockage

La connectivité réseau entre vos données sources et votre compte de stockage peut parfois être un goulot d’étranglement. Lorsque vos données sources sont locales, envisagez d’utiliser une liaison dédiée avec [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Si vos données sources sont dans Azure, les performances seront meilleures si les données sont dans la même région Azure que le compte Data Lake Storage Gen2.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Configurer les outils d’ingestion des données pour une parallélisation maximale

Pour obtenir des performances optimales, utilisez tout le débit disponible en effectuant autant de lectures et d’écritures que possible.

![Performances de Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

Le tableau suivant résume les paramètres principaux pour plusieurs outils d’ingestion populaires.

| Outil               | Paramètres |
|--------------------|------------------------------------------------------|
| [DistCp](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)             | -m (mappeur) |
| [Azure Data Factory](../../data-factory/copy-activity-performance.md). | parallelCopies    |
| [Sqoop](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)          | fs.azure.block.size, -m (mappeur)   |

Votre compte peut être mis à l’échelle afin de fournir le débit nécessaire pour tous les scénarios d’analyse. Par défaut, un compte Data Lake Storage Gen2 fournit suffisamment de débit dans sa configuration par défaut pour répondre aux besoins d’une vaste catégorie de cas d’usage. Si vous utilisez la limite par défaut, le compte peut être configuré pour fournir davantage de débit en contactant le [support Azure](https://azure.microsoft.com/support/faq/).

## <a name="structure-your-data-set"></a>Structure de votre jeu de données

Quand les données sont stockées dans un compte de stockage Data Lake Storage Gen2, la taille du fichier, le nombre de fichiers et la structure de dossiers ont un impact sur les performances.  La section suivante décrit les meilleures pratiques en la matière.

### <a name="file-size"></a>Taille du fichier

En règle générale, les moteurs d’analyse tels que HDInsight ont une surcharge par fichier qui implique des tâches telles que le listage, la vérification de l’accès et l’exécution de diverses opérations de métadonnées. Si vous stockez vos données sous forme de petits fichiers nombreux, cela peut nuire aux performances. De manière générale, organisez vos données dans de grands fichiers pour des performances optimales (taille allant de 256 Mo à 100 Go). Certains moteurs et applications peuvent avoir des difficultés à traiter efficacement les fichiers supérieurs à 100 Go.

Parfois, les pipelines de données ont un contrôle limité sur les données brutes qui contiennent un grand nombre de petits fichiers. En général, nous recommandons que votre système dispose d’une sorte de processus permettant d’agréger des fichiers de petite taille en fichiers plus volumineux pour les utiliser dans des applications en aval. Si vous traitez des données en temps réel, vous pouvez utiliser un moteur de diffusion en continu en temps réel (par exemple, [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md) ou [Spark streaming](https://databricks.com/glossary/what-is-spark-streaming)) avec un répartiteur de messages (comme [Event Hub](../../event-hubs/event-hubs-about.md) ou [Apache Kafka](https://kafka.apache.org/)) pour stocker vos données en tant que fichiers plus volumineux.

Au fur et à mesure que vous regroupez des fichiers de petite taille en fichiers plus volumineux, envisagez de les enregistrer dans un format optimisé en lecture comme [Apache Parquet](https://parquet.apache.org/) pour le traitement en aval. Apache Parquet est un format de fichier Open source qui est optimisé pour les pipelines d’analyse volumineux en lecture. La structure de stockage en colonnes de Parquet vous permet d’ignorer les données non pertinentes. Vos requêtes sont bien plus efficaces, car elles permettent d’étendre de manière étroite les données à envoyer du stockage au moteur d’analyse. En outre, étant donné que les types de données similaires (pour une colonne) sont stockés ensemble, Parquet prend en charge des schémas efficaces de compression et d’encodage des données qui peuvent réduire les coûts de stockage de données. Les services tels que [Azure Synapse Analytics](../../synapse-analytics/overview-what-is.md), [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) et [Azure Data Factory](../../data-factory/introduction.md) ont des fonctionnalités natives qui tirent parti des formats de fichiers Parquet.

Pour plus d’informations sur les formats de données, consultez la [section format des données de l’article sur les meilleures pratiques](data-lake-storage-best-practices.md).

### <a name="organizing-time-series-data-in-folders"></a>Organisation des données de série chronologique dans des dossiers

Pour les charges de travail Hive, le nettoyage de partition de données de série chronologique peut aider certaines requêtes à lire uniquement un sous-ensemble de données, ce qui améliore les performances.

Ces pipelines d’ingestion des données de série chronologique, organisent souvent leurs fichiers selon une structure d’appellation très structurée pour les fichiers et les dossiers. Découvrez ci-après un exemple très courant de données structurées par date :

*\DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv*

Notez que les informations de date / heure s’affichent à la fois en tant que dossiers et dans le nom de fichier.

Pour la date et l’heure, le modèle suivant est récurrent.

*\DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv*

Là encore, le type d’organisation des dossiers et fichiers sélectionné doit optimiser les plus gros fichiers et un nombre raisonnable de fichiers par dossier.

Pour d’autres suggestions sur la structure de disposition des répertoires, consultez [Structure des répertoires](data-lake-storage-best-practices.md#directory-layout-considerations)

### <a name="access-data-efficiently-with-query-acceleration"></a>Accéder efficacement aux données avec l’Accélération des requêtes

L’accélération des requêtes (préversion) est une nouvelle capacité d’Azure Data Lake Storage qui permet aux applications et aux infrastructures d’analytique d’optimiser considérablement le traitement des données en extrayant uniquement les données requises pour effectuer une opération donnée. Cela permet de réduire le temps et la puissance de traitement nécessaires pour obtenir un aperçu essentiel des données stockées.

L’accélération des requêtes accepte les prédicats de filtrage et les projections de colonnes qui permettent aux applications de filtrer les lignes et les colonnes au moment où les données sont lues sur le disque. Seules les données qui remplissent les conditions d’un prédicat sont transférées à l’application via le réseau. Cela réduit la latence du réseau et le coût du calcul.

Pour en savoir plus, consultez [Accélération des requêtes Azure Data Lake Storage](data-lake-storage-query-acceleration.md).

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optimiser des tâches à usage intensif d’E/S sur les charges de travail Hadoop et Spark sur HDInsight

La plus grande partie du temps des tâches intensives d’E/S est consacrée à la réalisation d’E/S.  Exemple courant : tâche de copie qui ne traite que les opérations de lecture et écriture.  Autres exemples : tâches de préparation de données qui lisent une grande quantité de données, effectuent une transformation de données et réécrivent les données dans le magasin.  Vous pouvez avoir une tâche qui lit ou écrit jusqu’à 100 Mo en une seule opération, mais une mémoire tampon de cette taille risque de compromettre les performances. Pour optimiser les performances, essayez de maintenir la taille des opérations d’E/S entre 4 et 16 Mo.

### <a name="io-intensive-jobs-with-hdinsight-clusters"></a>Tâches d’E/S intensives avec clusters HDInsight

- **Versions HDInsight.** Pour des performances optimales, utilisez la dernière version de HDInsight.
- **Régions.** Placez le compte Data Lake Storage Gen2 dans la même région que le cluster HDInsight.

Un cluster HDInsight est composé de deux nœuds principaux et de nœuds Worker. Chaque nœud Worker fournit une quantité spécifique de cœurs et de mémoire, déterminée par le type de machine virtuelle.  Lorsque vous exécutez une tâche, YARN est le négociateur de ressource qui alloue la mémoire et les cœurs disponible pour créer des conteneurs.  Chaque conteneur exécute les tâches nécessaires pour terminer la tâche.  Les conteneurs sont exécutés en parallèle pour traiter rapidement les tâches. Par conséquent, l’exécution du plus grand nombre possible de conteneurs en parallèle permet d’améliorer les performances.

Il existe trois couches au sein d’un cluster HDInsight qui peuvent être ajustées pour augmenter le nombre de conteneurs et utiliser tout le débit disponible.

- **Couche physique**
- **Couche YARN**
- **Couche de charge de travail**

### <a name="physical-layer"></a>Couche physique

**Exécutez le cluster avec le plus de nœuds et/ou les plus grosses machines virtuelles.**  Un plus grand cluster permet d’exécuter plus de conteneurs YARN, comme le montre l’image ci-dessous.

![Diagramme illustrant la façon dont un cluster de plus grande taille vous permet d’exécuter davantage de conteneurs YARN.](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Utilisez des machines virtuelles avec davantage de bande passante réseau.**  La quantité de bande passante réseau peut être un goulot d’étranglement si elle moins importante que le débit de Data Lake Storage Gen2.  La taille de la bande passante réseau varie en fonction des machines virtuelles.  Choisissez un type de machine virtuelle qui possède la plus grande bande passante possible.

### <a name="yarn-layer"></a>Couche YARN

**Utilisez des conteneurs YARN plus petits.**  Réduisez la taille de chaque conteneur YARN pour créer plusieurs conteneurs avec la même quantité de ressources.

![Diagramme montrant le résultat de la réduction de la taille de chaque conteneur YARN pour créer d’autres conteneurs.](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

En fonction de votre charge de travail, il y aura toujours une taille de conteneur YARN minimale requise. Si vous sélectionnez un conteneur trop petit, vos tâches rencontreront des problèmes de mémoire insuffisante. En général, les conteneurs YARN ne doivent pas être inférieurs à 1 Go. Les conteneurs YARN de 3 Go sont courants. Pour certaines charges de travail, des conteneurs YARN plus grands peuvent être nécessaires.

**Augmentez le nombre de cœurs par conteneur YARN.**  Augmentez le nombre de cœurs alloués à chaque conteneur pour augmenter le nombre de tâches parallèles qui s’exécutent dans chaque conteneur.  Cela fonctionne pour des applications comme Spark qui exécutent plusieurs tâches par conteneur.  Pour des applications comme Hive qui exécutent un seul thread par conteneur, il est préférable d’avoir plus de conteneurs plutôt que plus de cœurs par conteneur.

### <a name="workload-layer"></a>Couche de charge de travail

**Utilisez tous les conteneurs disponibles.**  Définissez le nombre de tâches de manière à ce qu’il soit égal ou supérieur au nombre de conteneurs disponibles. Ainsi, toutes les ressources sont utilisées.

![Diagramme montrant l’utilisation de tous les conteneurs.](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**L’échec des tâches est coûteux.** Si chaque tâche doit traiter une grande quantité de données, l’échec d’une tâche entraîne une nouvelle tentative coûteuse.  Par conséquent, il est préférable de créer davantage de tâches, chacune d’elle traitant une petite quantité de données.

Outre les instructions générales ci-dessus, chaque système ou infrastructure d’analyse a des paramètres différents que vous pouvez régler pour des performances optimales. Pour obtenir les meilleurs conseils à jour sur l’optimisation des performances de la charge de travail, consultez la documentation de chaque système que vous prévoyez d’utiliser.

## <a name="see-also"></a>Voir aussi

- [Bonnes pratiques d’utilisation d’Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md)
- [Présentation d’Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
