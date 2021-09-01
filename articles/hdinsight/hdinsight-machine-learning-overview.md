---
title: Vue d’ensemble du Machine Learning - Azure HDInsight
description: Vue d’ensemble des options de Machine Learning Big Data des clusters dans Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: a911e468443fe49bb1edc18af3d3412edc8eb8cc
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112678213"
---
# <a name="machine-learning-on-hdinsight"></a>Machine Learning sur HDInsight

HDInsight autorise un Machine Learning sur le Big Data, en permettant d’extraire de précieuses informations de grandes quantités (pétaoctets voire exaoctets) de structurées et non structurées, ainsi que de données à déplacement rapide. Il existe plusieurs options de Machine Learning dans HDInsight : SparkML et Apache Spark MLlib, Apache Hive et Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML et MLlib

[HDInsight Spark](spark/apache-spark-overview.md) est une offre d’[Apache Spark](https://spark.apache.org/) hébergée sur Azure, une infrastructure open source de traitement parallèle des données qui utilise le traitement en mémoire pour améliorer les performances des analyses de Big Data. Le moteur de traitement Spark est élaboré pour permettre des analyses rapides, simples d’utilisation et sophistiquées. De par ses capacités de calcul distribué en mémoire, Spark constitue le choix idéal pour les algorithmes itératifs utilisés dans l’apprentissage automatique et les calculs de graphiques. Il existe deux bibliothèques Machine Learning évolutives, qui offrent des fonctionnalités de modélisation d’algorithme à cet environnement distribué : MLlib et SparkML. MLib contient l’API d’origine qui vient au-dessus des RDD. SparkML est un package plus récent qui fournit une API de niveau supérieur reposant sur des trames de données pour construire des pipelines ML. SparkML ne prend pas en charge toutes les fonctionnalités de MLlib, mais remplace MLlib en tant que bibliothèque de Machine Learning standard de Spark.

La bibliothèque Microsoft Machine Learning pour Apache Spark est [MMLSpark](https://github.com/Azure/mmlspark). Cette bibliothèque est conçue pour améliorer la productivité des chercheurs de données sur Spark, accroître le taux d’expérimentation et pour tirer parti des techniques de Machine Learning de pointe, notamment l’apprentissage profond, sur des jeux de données très volumineux. MMLSpark crée une couche au-dessus des API de bas niveau de SparkML lors de la création de modèles ML évolutifs (comme des chaînes d’indexation), lors du formatage de données dans une disposition compatibles avec les algorithmes de Machine Learning et lors de l’assemblage de vecteurs de caractéristiques. La bibliothque MMLSpark simplifie ces opérations ainsi que d’autres tâches courantes permettant de créer des modèles dans PySpark.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning et Apache Hive

Azure Machine Learning fournit des outils de modélisation d’analyses prédictives et un service complètement managé vous permettant de déployer vos modèles prédictifs sous la forme de services web prêts à l’emploi. Azure Machine Learning est une solution complète d’analyse prédictive sur le cloud, que vous pouvez utiliser pour créer, tester, mettre en service et gérer rapidement des modèles prédictifs. Effectuez une sélection dans une volumineuse bibliothèque d’algorithmes, utilisez un studio web pour générer des modèles et déployez facilement votre modèle en tant que service web.

:::image type="content" source="./media/hdinsight-machine-learning-overview/azure-machine-learning.png" alt-text="Vue d’ensemble de Microsoft Azure Machine Learning" border="false":::

Créez des fonctionnalités pour les données dans un cluster HDInsight Hadoop à l’aide de [requêtes Hive](/azure/architecture/data-science-process/create-features-hive). La *conception de fonctionnalités* tente d’augmenter la puissance prédictive des algorithmes d’apprentissage en créant des fonctionnalités à partir des données brutes qui facilitent le processus d’apprentissage. Vous pouvez exécuter des requêtes HiveQL à partir d’Azure Machine Learning Studio (Classic) et accéder aux données traitées dans Hive et stockées dans le stockage d’objets blob à l’aide du [module Importer des données](../machine-learning/classic/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

L’[apprentissage profond](https://www.microsoft.com/en-us/research/group/dltc/) est une branche du Machine Learning qui utilise les réseaux neuronaux profonds, à l’image des processus biologiques du cerveau humain. De nombreux chercheurs considèrent l’apprentissage profond comme une approche prometteuse de l’intelligence artificielle. Les traducteurs de langue parlée, les systèmes de reconnaissance d’image et le raisonnement automatique sont quelques exemples d’apprentissage profond.

Dans le cadre de son travail sur l’apprentissage profond, Microsoft a développé [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), un outil open source gratuit et facile à utiliser. Cette boîte à outils est largement utilisée par un grand nombre de produits Microsoft, par des entreprises du monde entier qui doivent déployer l’apprentissage profond à grande échelle ainsi que par des étudiants intéressés par les techniques et les algorithmes les plus récents.

## <a name="see-also"></a>Voir aussi

### <a name="scenarios"></a>Scénarios

* [Apache Spark avec Machine Learning : Utiliser Spark dans HDInsight pour analyser la température d’un bâtiment à l’aide de données issues des systèmes de chauffage, de ventilation et de climatisation](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark avec Machine Learning : utiliser Spark dans HDInsight pour prédire les résultats de l’inspection d’aliments](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Générer des recommandations de films avec Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive et Azure Machine Learning](/azure/architecture/data-science-process/create-features-hive)
* [Apache Hive et Azure Machine Learning de bout en bout](/azure/architecture/data-science-process/hive-walkthrough)
* [Machine Learning avec Apache Spark sur HDInsight](/azure/architecture/data-science-process/spark-overview)

### <a name="deep-learning-resources"></a>Ressources d’apprentissage profond

* [Utiliser le modèle de formation approfondie Microsoft Cognitive Toolkit avec un cluster Azure HDInsight Spark](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Infrastructures de Deep Learning et AI sur la Data Science Virtual Machine (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
