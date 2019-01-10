---
title: Notes de publication archivées pour Azure HDInsight
description: Notes de publication archivées et versions pour Azure HDInsight.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: hrasheed
ms.openlocfilehash: 19a842eacb00e877d31712febeeebc72cbdd25a0
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53743168"
---
# <a name="archived-release-notes-for-azure-hdinsight"></a>Notes de publication archivées pour Azure HDInsight

Pour obtenir les mises à jour des notes de publication Azure HDInsight les **plus récentes**, consultez [Notes de publication HDInsight](hdinsight-release-notes.md).

> [!IMPORTANT]  
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [l’article sur le contrôle de version de HDInsight](hdinsight-component-versioning.md).

## <a name="notes-for-06272018---release-of-new-open-source-versions-adls-gen2-etc-on-hdinsight-36"></a>Notes du 27/06/2018 – Lancement de nouvelles versions open source, ADLS Gen2, etc. sur HDInsight 3.6
La version de HDInsight de juin 2018 est une version riche en mises à jour et fonctionnalités pour nos clients. Pour plus de détails, lisez ce [billet](https://azure.microsoft.com/blog/enterprises-get-deeper-insights-with-hadoop-and-spark-updates-on-azure-hdinsight/).

Les points essentiels sont exposés ci-dessous. Les notes de publication détaillées, les bogues résolus, les problèmes connus, etc., sont répertoriés dans [Notes de publication pour Azure HDInsight](hdinsight-release-notes.md).

- **Mise à jour d’Apache Hadoop et d’autres projets open source** : en plus des correctifs de bogues, qui se montent à plus de 1 000 dans plus 20 projets open source, cette mise à jour contient une nouvelle version d’Apache Spark (2.3) et d’Apache Kafka (1.0).
- **Mise à jour de R Server 9.1 vers Machine Learning Services 9.3** : avec cette version, nous offrons aux scientifiques des données et aux ingénieurs le meilleur de l’open source qui bénéficie d’innovations algorithmiques et d’une grande facilité d’opérationnalisation, le tout disponible dans leur langage préféré avec la vitesse d’Apache Spark. Cette version étend les fonctionnalités offertes par R Server avec l’ajout de la prise en charge de Python, entraînant le remplacement du nom du cluster R Server par ML Services. 
- **Prise en charge d’Azure Data Lake Storage Gen2** : HDInsight prendra en charge la préversion d’Azure Data Lake Storage Gen2. Dans les régions disponibles, les clients pourront choisir un compte ADLS Gen2 comme magasin pour leurs clusters HDInsight.
- **Mises à jour du Pack Sécurité Entreprise HDInsight (préversion)**  : (préversion) les points de terminaison du service de réseau virtuel prennent en charge le Stockage Blob Azure, ADLS Gen1, Cosmos DB et Azure DB. 

## <a name="notes-for-03202018---release-of-spark-22-on-hdinsight-36"></a>Notes du 20/03/2018 - mise en production de Spark 2.2 sur HDInsight 3.6

- Spark 2.2.0 améliore la stabilité sur Spark Core, SQL, ML et offre des flux structurés à l’état de disponibilité générale. Spark 2.2.0 est maintenant disponible sur HDInsight 3.6.


## <a name="notes-for-08012017-release-of-hdinsight"></a>Notes relatives à la version du 01/08/2017 de HDInsight

| Intitulé | Description | Zone concernée  | Type du cluster  | 
| --- | --- | --- | --- | --- |
| Version de Microsoft R Server 9.1 sur HDInsight |HDInsight prend désormais en charge l’approvisionnement des clusters R Server 9.1 sur HDInsight. Pour plus d’informations sur la version Microsoft R Server 9.1, consultez [ce blog](https://blogs.technet.microsoft.com/dataplatforminsider/2017/04/19/introducing-microsoft-r-server-9-1-release/). |de diffusion en continu |R Server |
| HDInsight 3.6 inclut désormais les versions les plus récentes de la pile Hadoop|<ul><li>Pour une liste détaillée des versions mises à jour, consultez [Versions des composants Apache Hadoop disponibles dans HDInsight](hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).</li><li>Pour obtenir la liste des bogues corrigés dans les versions les plus récentes de la pile Hadoop, consultez [Apache Patch Information](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html) (Informations sur le correctif Apache).</li><li>Pour obtenir la liste des changements cassants entre HDP 2.6.1 (qui est maintenant disponible dans HDInsight 3.6), consultez [https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html).</li><li>Pour obtenir la liste des problèmes connus dans HDP 2.6.1, consultez [Problèmes connus](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html).</li></ul> |de diffusion en continu |Tous |N/A |
| Mises à jour des clusters Hive interactif (préversion) |<ul><li><b>Amélioration de la fonctionnalité.</b> Implémentation du metastore mis en cache, qui permet de réduire la charge sur le serveur principal SQL en mettant en cache les métadonnées, et d’améliorer les performances liées à toutes les opérations de métadonnées.  Cette amélioration est désormais disponible par défaut sur tous les clusters Interactive Apache Hive. Pour plus d’informations, consultez [https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520).</li><li><b>Amélioration de la fonctionnalité.</b> Le chargement de la partition dynamique est optimisé. Pour plus d’informations, consultez [https://issues.apache.org/jira/browse/HIVE-14204](https://issues.apache.org/jira/browse/HIVE-14204).</li><li><b>Amélioration de la fonctionnalité.</b> Optimisations de configuration pour HDInsight sur Linux.</li><li><b>Résolution de bogue.</b> `CredentialProviderFactory$getProviders` n’est pas thread-safe. » Ce problème est maintenant résolu. Pour plus d’informations, consultez [https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195).</li><li><b>Résolution de bogue.</b> Utilisation élevée de l’UC avec l’API `liststatus` du pilote WASB, entraînant de mauvaises performances ATS. » Ce problème est maintenant résolu. Pour plus d’informations, consultez [https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154).</li></ul> |de diffusion en continu |Hive interactif (version préliminaire) |
| Mises à jour des clusters Hadoop |La fiabilité des opérations liées au travail Templeton est améliorée. Pour plus d’informations, consultez [https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947) |de diffusion en continu |Hadoop |
| Mises à jour YARN | HDInsight crée désormais une base de données Ambari de 250 Go (sans frais supplémentaires), ce qui offre une meilleure expérience pour les clients. Cette modification est censée empêcher la saturation ATS et apporter de meilleures performances. |de diffusion en continu |Tous |
| Mises à jour Spark | Version de Spark 2.1.1. Pour plus d’informations, consultez [Apache Spark version 2.1.1](https://spark.apache.org/releases/spark-release-2-1-1.html). | de diffusion en continu | Spark |

  


## <a name="04062017---general-availability-of-hdinsight-36"></a>06/04/2017 - Disponibilité générale de HDInsight 3.6

* Avec cette version, Azure HDInsight ajoute la version 3.6, qui est basée sur HDP 2.6. Les notes de publication de HDP 2.6 sont disponibles [ici](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html). De plus amples informations sur les versions de HDInsight sont disponibles [ici](hdinsight-component-versioning.md). HDInsight 3.6 est disponible pour les charges de travail suivantes :

    * Hadoop v2.7.3
    * HBase v1.1.2
    * Storm v1.1.0
    * Spark v2.1.0
    * Hive interactif v2.1.0

* **Prise en charge de Hive View 2.0**. Cela doit améliorer l’expérience utilisateur de Hive interactif. Pour plus d’informations, voir la [documentation Hortonworks](https://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Amélioration des performances avec Hive LLAP**. Pour plus d’informations, voir la [documentation Hortonworks](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Nouvelles fonctionnalités Hive** Consultez la [documentation Hortonworks](https://hortonworks.com/apache/hive/#section_4).

* **Dépréciation de Hive CLI** : Hive CLI est déprécié et les clients sont encouragés à utiliser Beeline à la place. Pour plus d’informations, consultez la [documentation Apache](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Pour obtenir des instructions sur l’utilisation de Beeline avec HDInsight, consultez [Utiliser Beeline avec des clusters Hadoop HDInsight](hadoop/apache-hadoop-use-hive-beeline.md).

* **Nouvelles fonctionnalités Apache Phoenix et HBase**.
    * Prise en charge des quotas de stockage : couramment utilisée dans des environnements multi-locataires, cette option permet d’avoir un espace de stockage limité par table et par espace de noms.
    * Améliorations apportées à l’indexation Phoenix : création d’index incrémentiel et regénération/reprise de l’indexation à partir des échecs antérieurs.
    * Outil d’intégrité des données Phoenix : prend en charge la validation de schémas, d’index et d’autres métadonnées.


* **Problème avec HBase** : Lors de l’exécution d’une tâche MapReduce de chargement en bloc de fichiers CSV, la syntaxe suivante peut provoquer une erreur.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Utilisez plutôt la syntaxe suivante :

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>Version du 28/02/2017 de Spark 2.1 sur HDInsight 3.6 (préversion)
* [Spark 2.1](https://spark.apache.org/releases/spark-release-2-1-0.html) améliore les nombreux problèmes de stabilité et de convivialité des versions précédentes. Il apporte également de nouvelles fonctionnalités pour toutes les charges de travail Spark telles que Spark Core, SQL, ML et Streaming.
* La fonctionnalité Structured Streaming bénéficie d’une meilleure évolutivité avec prise en charge des filigranes de temps d’événement et du connecteur Kafka 0.10.
* Le partitionnement Spark SQL est désormais géré à l’aide d’un nouveau mécanisme de gestion de partition évolutive. Vous trouverez [ici](https://spark.apache.org/releases/spark-release-2-1-0.html) plus de détails sur la mise à niveau.
* Actuellement, Spark 2.1 sur Azure HDInsight 3.6 en préversion ne prend pas en charge la connectivité des outils décisionnels à l’aide du pilote ODBC.
* L’accès à Azure Data Lake Storage à partir de clusters Spark 2.1 n’est pas pris en charge dans cette préversion.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>Version du 18/11/2016 de Spark 2.0.1 sur HDinsight 3.5
Spark 2.0.1 est désormais disponible sur les clusters Spark (HDInsight version 3.5).

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>Version du 16/11/2016 de R Server 9.0 sur HDInsight 3.5 (Spark 2.0)
*   Les clusters R Server incluent désormais une option pour les deux versions : R Server 9.0 sur HDI 3.5 (Spark 2.0) et R Server 8.0 sur HDI 3.4 (Spark 1.6).
*   R Server 9.0 sur HDI 3.5 (Spark 2.0) repose sur R 3.3.2 et inclut les nouvelles fonctions de source de données ScaleR appelées RxHiveData et RxParquetData permettant de charger des données à partir de Hive et de Parquet directement dans Spark DataFrames, pour que ScaleR les analyse. Pour plus d’informations, consultez l’aide en ligne sur ces fonctions dans R à l’aide des commandes **?RxHiveData** et **?RxParquetData**.
*   L’édition communautaire de RStudio Server est désormais installée par défaut (avec une option de désistement) dans le panneau Configuration de cluster dans le cadre du flux d’approvisionnement.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>Version du 09/11/2016 de Spark 2.0 sur HDinsight
* Les clusters Spark 2.0 sur HDInsight 3.5 prennent désormais en charge les services Livy et Jupyter.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>26/10/2016 - Version de R Server sur HDInsight
* L’URI d’accès au nœud de périmètre a été modifiée en **clustername**-ed-ssh.azurehdinsight.net
* L’approvisionnement du cluster R Server sur HDInsight a été simplifié.
* R Server sur HDInsight est désormais disponible en tant que type de cluster R Server HDInsight standard, et n’est plus installé comme une application HDInsight distincte. Le nœud de périphérie et les fichiers binaires R Server sont désormais approvisionnés dans le cadre du déploiement du cluster R Server. Cela améliore la vitesse et la fiabilité de l’approvisionnement. Le modèle de tarification de R Server est mis à jour en conséquence.
* Le prix du type de cluster R Server est désormais basé sur le prix de niveau Standard, plus le coût supplémentaire R Server. Cette modification n’affecte pas la tarification effective de R Server. Elle modifie uniquement la façon dont les frais sont présentés sur la facture. Tous les clusters R Server existants continuent de fonctionner, ainsi que les modèles Resource Manager jusqu’à avis de désapprobation. **Il est cependant recommandé de mettre à jour vos déploiements par script afin d’utiliser le nouveau modèle Resource Manager.**

