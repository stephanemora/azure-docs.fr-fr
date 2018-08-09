---
title: Présentation d’Azure Data Lake Storage Gen2 (préversion)
description: Fournit une vue d’ensemble d’Azure Data Lake Storage Gen2 (préversion)
services: storage
author: jamesbak
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 51f38cf7ade01b58ad5ce7925af5546d1a4f1a0c
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525380"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Présentation d’Azure Data Lake Storage Gen2 (préversion)

Azure Data Lake Storage Gen2 (préversion) est un ensemble de fonctionnalités dédié à l’analytique du Big Data, qui repose sur le [Stockage Blob Azure](../blobs/storage-blobs-introduction.md). Il vous permet d’interagir avec vos données selon les deux paradigmes que sont le système de fichiers et le stockage d’objets. Data Lake Storage Gen2 est donc le seul service de stockage multimodal dans le cloud qui vous permet d’extraire de précieuses données analytiques de toutes vos données.

Data Lake Storage Gen2 présente toutes les qualités nécessaires pour le cycle de vie complet des données analytiques. Il rassemble donc les fonctionnalités de nos deux services de stockage existants. Les fonctionnalités [d’Azure Data Lake Storage Gen1](../../data-lake-store/index.md), comme la sémantique des systèmes de fichiers, la sécurité au niveau du fichier et la mise à l'échelle, sont combinées à celles du [Stockage Blob Azure](../blobs/storage-blobs-introduction.md), comme le stockage hiérarchisé économique, la haute disponibilité/reprise après sinistre et le grand écosystème de SDK/d’outils. Dans Data Lake Storage Gen2, toutes les qualités du stockage d’objets sont conservées, avec en plus les avantages d’une interface de système de fichiers optimisée pour les charges de travail analytiques.

## <a name="designed-for-enterprise-big-data-analytics"></a>Conçu pour l’analytique du Big Data d’entreprise

Data Lake Storage Gen2 est le service de stockage de base pour créer des dépôts Data Lake d’entreprise sur Azure. Conçu dès le départ pour traiter plusieurs téraoctets d’informations tout en assurant des centaines de gigaoctets de débit, Data Lake Storage Gen2 est un moyen facile de gérer les gros volumes de données.

Une fonctionnalité fondamentale de Data Lake Storage Gen 2 est l’ajout d’un [espace de noms hiérarchique](./namespace.md) au service de stockage Blob qui organise les objets et les fichiers en une hiérarchie de répertoires pour optimiser l’accès aux données. L’espace de noms hiérarchique permet également à Data Lake Storage Gen2 de prendre en charge les deux paradigmes que sont les magasins d’objets et les systèmes de fichiers. Par exemple, une convention de nommage de magasin d’objets commune utilise des barres obliques dans le nom pour reproduire une structure hiérarchique de dossiers. Cette structure devient réelle avec Data Lake Storage Gen2. Des opérations comme le renommage ou la suppression d’un répertoire deviennent des opérations atomiques uniques de métadonnées sur le répertoire au lieu d’énumérer et de traiter tous les objets qui partagent le préfixe du nom de répertoire.

Dans le passé, l’analytique cloud devait trouver le meilleur compromis entre les performances, la gestion et la sécurité. Data Lake Storage Gen2 traite chacun de ces aspects de plusieurs manières :

- Les **performances** sont optimisées, car vous n’avez pas besoin de copier ou transformer les données avant l’analyse. L’espace de noms hiérarchique améliore considérablement les performances des opérations de gestion de répertoires, et donc les performances générale du travail.

- La **gestion** est plus facile, car vous pouvez organiser et manipuler les fichiers dans des répertoires et des sous-répertoires.

- La **rentabilité** est réelle, car Data Lake Storage Gen2 repose sur le [Stockage Blob Azure](../blobs/storage-blobs-introduction.md) dont le coût est faible. Les fonctionnalités supplémentaires réduisent encore le coût total de possession de l’analytique du Big Data sur Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Principales fonctionnalités de Data Lake Storage Gen2

> [!NOTE]
> Pendant la préversion publique de Data Lake Storage Gen2, la disponibilité de certaines des fonctionnalités répertoriées ci-dessous peut varier. Vous serez informés à mesure de la publication des nouvelles régions et fonctionnalités.
> [Inscrivez-vous](https://aka.ms/adlsgen2signup) à la préversion publique de Data Lake Storage Gen2.  

- **Accès compatible Hadoop** : Data Lake Storage Gen2 vous permet de gérer les données et d’y accéder comme avec un [système de fichiers DFS Hadoop (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Le nouveau [pilote ABFS](./abfs-driver.md) est disponible dans tous les environnements Apache Hadoop, y compris [Azure HDInsight](../../hdinsight/index.yml) et [Azure Databricks](../../azure-databricks/index.yml) pour accéder aux données stockées dans Data Lake Storage Gen2.

- **Accès aux données multiprotocole et multimodal**: Data Lake Storage Gen2 est considéré comme un service de stockage **multimodal**, car il fournit à la fois des interfaces de magasin d’objets et de système de fichiers pour les mêmes données **en même temps**. Cela est possible par le biais de plusieurs points de terminaison de protocole capables d’accéder aux mêmes données. 

    Contrairement à d’autres solutions analytiques, les données stockées dans Data Lake Storage Gen2 n’ont pas besoin d’être déplacées ou transformées pour pouvoir exécuter divers outils analytiques. Vous pouvez accéder aux données au moyen [d’API de stockage Blob](../blobs/storage-blobs-introduction.md) traditionnelles (par exemple : ingestion de données avec [Event Hubs Capture](../../event-hubs/event-hubs-capture-enable-through-portal.md)) et traiter ces données à l’aide de HDInsight ou d’Azure Databricks en même temps. 

- **Économique** : Data Lake Storage Gen2 intègre une capacité de stockage et des transactions économiques. Tout au long du cycle de vie des données, les taux de facturation changent pour maintenir les coûts au minimum par le biais de fonctionnalités intégrées comme le [cycle de vie du Stockage Blob Azure](../common/storage-lifecycle-managment-concepts.md).

- **Fonctionne avec les outils, les frameworks et les applications de stockage Blob** : Data Lake Storage Gen2 continue de fonctionner avec une large gamme d’outils, de frameworks et d’applications qui existent actuellement pour le stockage Blob.

- **Pilote optimisé** : Le pilote `abfs` est [optimisé spécifiquement](./abfs-driver.md) pour l’analytique du Big Data. Les API REST correspondantes sont exposées par le biais du point de terminaison `dfs`, `dfs.core.windows.net`.

## <a name="scalability"></a>Extensibilité

Le Stockage Azure est nativement scalable, que vous y accédiez via les interfaces Data Lake Storage Gen2 ou de stockage Blob. Il peut stocker et traiter *plusieurs exaoctets de données*. Ce volume de stockage est disponible avec un débit mesuré en gigabits par seconde (Gbits/s) et des niveaux élevés d’opérations d’entrée/sortie par seconde (IOPS). En plus de la simple persistance, le traitement est exécuté à des latences par demande quasi constantes, qui sont mesurées au niveau du service, du compte et du fichier.

## <a name="cost-effectiveness"></a>Rentabilité

Un des nombreux avantages de la conception de Data Lake Storage Gen2 sur le stockage Blob Azure est le [faible coût](https://azure.microsoft.com/pricing/details/storage) de la capacité de stockage et des transactions. Contrairement à d’autres services de stockage cloud, Data Lake Storage Gen2 réduit les coûts en évitant de déplacer ou transformer les données avant d’effectuer l’analyse.

Par ailleurs, des fonctionnalités comme [l’espace de noms hiérarchique](./namespace.md) améliorent considérablement les performances générales de nombreuses tâches analytiques. Cette amélioration des performances signifie que vous avez besoin d’une puissance de calcul moindre pour traiter la même quantité de données, ce qui contribue à la baisse du coût total de possession (TCO) pour le travail analytique de bout en bout.

## <a name="next-steps"></a>Étapes suivantes

Les articles suivants décrivent certains des principaux concepts de Data Lake Storage Gen2 et détaillent comment stocker et gérer les données, y accéder, et les utiliser pour obtenir des insights :

* [Espace de noms hiérarchique](./namespace.md)
* [Créez un compte de stockage](./quickstart-create-account.md)
* [Créer un cluster HDInsight avec Azure Data Lake Storage Gen2](./quickstart-create-connect-hdi-cluster.md)
* [Utiliser un compte Azure Data Lake Storage Gen2 dans Azure Databricks](./quickstart-create-databricks-account.md) 