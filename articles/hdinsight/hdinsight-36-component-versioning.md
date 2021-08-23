---
title: Composants et versions d’Apache Hadoop – Azure HDInsight 3.6
description: Découvrez les composants et les versions d’Apache Hadoop dans Azure HDInsight 3.6.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: f9e040c596d9335dcf1027aa96ad671a77ac88c0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524008"
---
# <a name="hdinsight-36-component-versions"></a>Composants et versions de HDInsight 3.6

Dans cet article, vous allez découvrir les composants et les versions de l’environnement Apache Hadoop inclus dans Azure HDInsight 3.6.

## <a name="support-for-hdinsight-36"></a>Prise en charge de HDInsight 3.6

À partir du 1er juillet 2021, Microsoft proposera le plan Support de base pour certains types de cluster HDI 3.6.
Le tableau ci-dessous répertorie la période de prise en charge des types de clusters HDInsight 3.6.

| Type du cluster                    | Version du framework | Expiration du support standard       | Date d’expiration du support de base | Date de mise hors service |
|---------------------------------|-------------------|-----------------------------------|------------------------------|-----------------|
| HDInsight 3.6 Hadoop            | 2.7.3             | 30 juin 2021                     | 3 avril 2022                | 4 avril 2022 |
| HDInsight 3.6 Spark             | 2.3               | 30 juin 2021                     | 3 avril 2022                | 4 avril 2022 |
| HDInsight 3.6 Kafka             | 1,1               | 30 juin 2021                     | 3 avril 2022                | 4 avril 2022 |
| HDInsight 3.6 HBase             | 1,1               | 30 juin 2021                     | 3 avril 2022                | 4 avril 2022 |
| HDInsight 3.6 Interactive Query | 2.1               | 30 juin 2021                     | 3 avril 2022                | 4 avril 2022 |
| HDInsight 3.6 Storm             | 1,1               | 30 juin 2021                     | 3 avril 2022                | 4 avril 2022 |
| HDInsight 3.6  ML Services      | 9.3               | -                                 | -                            | 31 décembre 2020 |
| HDInsight 3.6 Spark             | 2,2               | -                                 | -                            | 30 juin 2020 |
| HDInsight 3.6 Spark             | 2.1               | -                                 | -                            | 30 juin 2020 |
| HDInsight 3.6 Kafka             | 1.0               | -                                 | -                            | 30 juin 2020 |

## <a name="apache-components-available-with-hdinsight-version-36"></a>Composants d’Apache disponibles avec HDInsight version 3.6

Les versions des composants OSS associées à HDInsight 3.6 sont répertoriées dans le tableau suivant.

| Composant              | HDInsight 3.6 (par défaut)     |
|------------------------|-----------------------------|
| Apache Hadoop et YARN | 2.7.3                       |
| Apache Tez             | 0.7.0                       |
| Apache Pig             | 0.16.0                      |
| Apache Hive            | (2.1.0 sur ESP Interactive Query) |
| Apache Tez/Hive2       | 0.8.4                       |
| Apache Ranger          | 0.7.0                       |
| Apache HBase           | 1.1.2                       |
| Apache Sqoop           | 1.4.6                       |
| Apache Oozie           | 4.2.0                       |
| Apache Zookeeper       | 3.4.6                       |
| Apache Storm           | 1.1.0                       |
| Apache Mahout          | 0.9.0+                      |
| Apache Phoenix         | 4.7.0                       |
| Apache Spark           | 2.3.2.                      |
| Apache Livy            | 0.4.                        |
| Apache Kafka           | 1.1                         |
| Apache Ambari          | 2.6.0                       |
| Apache Zeppelin        | 0.7.3                       |
| Mono                   | 4.2.1                       |

## <a name="next-steps"></a>Étapes suivantes

- [Création de clusters pour Apache Hadoop, Spark, etc. dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Pack Sécurité Entreprise](./enterprise-security-package.md)
- [Travailler à partir d’un PC Windows dans Apache Hadoop sur HDInsight](hdinsight-hadoop-windows-tools.md)
