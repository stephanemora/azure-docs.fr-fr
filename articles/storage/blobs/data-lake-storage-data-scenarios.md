---
title: Scénarios de données impliquant Azure Data Lake Storage Gen2 | Microsoft Docs
description: Comprendre les différents scénarios et outils à l’aide desquels les données peuvent être ingérées, traitées, téléchargées et affichées dans Data Lake Storage Gen2 (anciennement Azure Data Lake Store)
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.openlocfilehash: 1c50a6e14955b2c31222ff1317aa99ad28866ec8
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864731"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Utilisation d’Azure Data Lake Storage Gen2 pour le Big Data

Il existe quatre étapes principales dans traitement des données Big Data :

* Réception de grandes quantités de données dans un magasin de données, en temps réel ou par lots
* Traitement des données
* Téléchargement des données
* Visualisation des données

Dans cet article, nous allons examiner ces étapes en ce qui concerne Azure Data Lake Storage Gen2 pour comprendre les options et les outils disponibles pour répondre à vos besoins en termes de Big Data.

## <a name="ingest-data-into-data-lake-storage-gen2"></a>Ingérer des données dans Data Lake Storage Gen2
Cette section présente les différentes sources de données et les différentes manières d’ingérer ces données dans un compte Data Lake Storage Gen2.

![Ingérer des données dans Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Ingérer des données dans Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Données ad hoc
Ceci représente les petits jeux de données qui sont utilisés pour créer un prototype d’une application de Big Data. Il existe différentes façons de recevoir des données ad hoc en fonction de la source de données.

| source de données | Réception avec |
| --- | --- |
| Ordinateur local |<ul> <li>[Explorateur Stockage](https://azure.microsoft.com/features/storage-explorer/)</ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)</li> <li>[Outil AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[DistCp en cours d’exécution sur un cluster HDInsight](data-lake-storage-use-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Flux de données
Ceci représente les données qui peuvent être générées par diverses sources, telles que des applications, des appareils, des capteurs, etc. Ces données peuvent être ingérées dans Data Lake Storage Gen2 par des outils divers. En général, ces outils capturent et traitent les données sur la base de l’événement en temps réel, puis ils écrivent les événements par lots dans Data Lake Storage Gen2 afin qu’ils puissent être traités.

Voici les outils que vous pouvez utiliser :

* [Azure HDInsight Storm](../../hdinsight/storm/apache-storm-write-data-lake-store.md) : vous pouvez écrire des données directement dans Data Lake Storage Gen2 à partir du cluster Storm.

### <a name="relational-data"></a>Données relationnelles
Les bases de données relationnelles peuvent également être utilisées comme sources des données. Sur une période donnée, les bases de données relationnelles collectent de grandes quantités de données qui peuvent fournir des informations clés si elles sont traitées via un pipeline de Big Data. Vous pouvez utiliser les outils suivants pour déplacer ces données vers Data Lake Storage Gen2.

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Données de journal de serveur web (téléchargement à l’aide d’applications personnalisées)
Ce type de jeu de données est spécifiquement indiqué, car l’analyse des données de journal de serveur web constitue un cas d’usage courant pour les applications de Big Data et nécessite le chargement d’importants volumes de fichiers journaux sur Data Lake Storage Gen2. Vous pouvez utiliser les outils suivants pour écrire vos propres scripts ou applications pour télécharger ces données.

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)

Pour télécharger des données de journal de serveur web, et également pour télécharger d’autres types de données (par exemple, les données relatives aux sentiments sociaux), il est préférable d’écrire vos propres scripts/applications personnalisés, car cela vous donne la possibilité d’inclure votre composant de téléchargement de données dans le cadre de votre application de Big Data plus étendue. Dans certains cas, ce code peut prendre la forme d’un script ou d’un utilitaire de ligne de commande simple. Dans d’autres cas, le code peut être utilisé pour intégrer le traitement de Big Data dans une solution ou une application métier.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Données associées aux clusters Azure HDInsight
La plupart des types de clusters HDInsight (Hadoop, HBase, Storm) prennent en charge Data Lake Storage Gen2 comme référentiel de stockage des données. Les clusters HDInsight accèdent aux données à partir des objets blob d’Azure Storage (WASB). Pour optimiser les performances, vous pouvez copier les données à partir de WASB sur un compte Data Lake Storage Gen2 associé au cluster. Vous pouvez utiliser les outils suivants pour copier les données.

* [Apache DistCp](data-lake-storage-use-distcp.md)
* [Outil AzCopy](../common/storage-use-azcopy-v10.md)
* [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Données stockées localement ou dans des clusters IaaS Hadoop
De grandes quantités de données peuvent être stockées dans des clusters Hadoop existants, localement sur les ordinateurs à l’aide de HDFS. Les clusters Hadoop peuvent être inclus dans un déploiement local ou au sein d’un cluster IaaS sur Azure. Il peut être nécessaire de copier ces données dans Azure Data Lake Storage Gen2 afin de bénéficier d’un accès unique ou périodique. Différentes options vous permettent d’y parvenir. Voici une liste de ces options et des compromis correspondants.

| Approche | Détails | Avantages | Considérations |
| --- | --- | --- | --- |
| Utiliser Azure Data Factory (ADF) pour copier des données directement à partir de clusters Hadoop dans Azure Data Lake Storage Gen2 |[ADF prend en charge HDFS comme source de données](../../data-factory/connector-hdfs.md) |ADF offre une prise en charge immédiate de HDFS ainsi qu’une gestion et une surveillance de bout en bout de premier ordre |Nécessite que la passerelle de gestion des données soit déployée localement ou dans le cluster IaaS |
| Utilisez Distcp pour copier les données de Hadoop dans Azure Storage. Copiez ensuite les fichiers de Stockage Azure vers Data Lake Storage Gen2 à l’aide du mécanisme approprié. |Vous pouvez copier les données de Stockage Azure vers Data Lake Storage Gen2 en utilisant : <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Outil AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[pache DistCp en cours d’exécution sur des clusters HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Vous pouvez utiliser des outils open source. |Processus en plusieurs étapes qui implique différentes technologies |

### <a name="really-large-datasets"></a>Jeux de données très volumineux
Pour télécharger des jeux de données qui comptent plusieurs téraoctets, l’utilisation des méthodes décrites ci-dessus peut parfois être lente et coûteuse. Dans ce cas, vous pouvez utiliser les options ci-dessous.

* **Utilisation d’Azure ExpressRoute**. Azure ExpressRoute vous permet de créer des connexions privées entre les infrastructures et les centres de données Azure dans votre environnement local. Ceci constitue une option fiable pour le transfert de grandes quantités de données. Pour plus d’informations, consultez la [Documentation Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-data-stored-in-data-lake-storage-gen2"></a>Traiter des données stockées dans Data Lake Storage Gen2
Une fois que les données sont disponibles dans Data Lake Storage Gen2, vous pouvez exécuter une analyse sur ces données à l’aide des applications de Big Data prises en charge. Actuellement, vous pouvez utiliser Azure HDInsight et Azure Databricks pour exécuter des tâches d’analyse des données sur les données stockées dans Data Lake Storage Gen2.

![Analyser des données dans Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analyser des données dans Data Lake Storage Gen2")

Pour obtenir un exemple, consultez [Utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2).


## <a name="download-data-from-data-lake-storage-gen2"></a>Télécharger des données à partir de Data Lake Storage Gen2
Vous pouvez également être amené à télécharger ou à déplacer les données à partir d’Azure Data Lake Storage Gen2 dans certains cas, tels que :

* Déplacer des données vers d’autres référentiels pour créer une interface avec vos pipelines de traitement des données existantes. Par exemple, vous pourriez vouloir déplacer des données de Data Lake Storage Gen2 vers Azure SQL Database ou vers SQL Server local.
* Télécharger des données sur votre ordinateur local pour le traitement dans des environnements IDE lors de la création de prototypes d’applications.

![Sortir des données à partir de Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Sortir des données à partir de Data Lake Storage Gen2")

Dans ce cas, vous pouvez utiliser l’une des options suivantes :

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-storage-use-distcp.md)

## <a name="visualize-data-in-data-lake-storage-gen2"></a>Visualiser des données dans Data Lake Storage Gen2
Vous pouvez utiliser une combinaison de services pour créer des représentations visuelles des données stockées dans Data Lake Storage Gen2.

![Visualiser des données dans Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "Visualiser des données dans Data Lake Storage Gen2")

* Vous pouvez commencer par utiliser [Azure Data Factory pour déplacer les données de Data Lake Storage Gen2 vers Azure SQL Data Warehouse](../../data-factory/copy-activity-overview.md)
* Ensuite, vous pouvez [intégrer Power BI à Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) pour créer une représentation visuelle des données.
