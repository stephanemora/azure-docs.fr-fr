---
title: Autres produits d’apprentissage automatique de Microsoft - Azure Machine Learning | Microsoft Docs
description: Outre Azure Machine Learning, il existe une grande variété d’options Microsoft permettant de générer, déployer et gérer vos modèles d’apprentissage automatique.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: overview
ms.date: 04/11/2018
ms.openlocfilehash: 3e36d9202c578294609b01eaf2731b1551ae67af
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282328"
---
# <a name="other-machine-learning-products-and-services-from-microsoft"></a>Autres produits et services d’apprentissage automatique de Microsoft

En dehors d’[Azure Machine Learning](overview-what-is-azure-ml.md), il existe une grande variété d’options Microsoft permettant de générer, déployer et gérer vos modèles Machine Learning. 
* Services de Machine Learning SQL Server
* Microsoft Machine Learning Server
* Machine virtuelle DSVM (Data Science Virtual Machine) Azure
* Spark MLLib dans HDInsight
* Service Batch AI Training
* Microsoft Cognitive Toolkit (CNTK)
* Azure Cognitive Services


## <a name="sql-server-machine-learning-services"></a>Services de Machine Learning SQL Server
[SQL Server Microsoft Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) vous permet d’exécuter, de former et de déployer des modèles d’apprentissage automatique à l’aide de R ou de Python. Vous pouvez utiliser les données situées en local et dans des bases de données SQL Server. 

Utilisez Microsoft Machine Learning Services quand vous devez former ou déployer des modèles en local ou dans des bases de données Microsoft SQL Server. Les modèles créés avec Machine Learning Services peuvent être déployés à l’aide du service Gestion des modèles Azure Machine Learning. 

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server 
[Microsoft Machine Learning Server](https://docs.microsoft.com/en-us/machine-learning-server/what-is-machine-learning-server) est un serveur d’entreprise pour l’hébergement et la gestion des charges de travail parallèles et distribuées des processus R et Python. Microsoft Machine Learning Server s’exécute sur Linux, Windows, Hadoop et Apache Spark. Il est également disponible sur [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Il fournit un moteur d’exécution pour les solutions créées à l’aide des [packages Microsoft Machine Learning](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package) et étend Python et R open source avec la prise en charge des scénarios suivants :

- Analytique hautes performances
- Analyse statistique
- Apprentissage automatique
- Jeux de données très volumineux

D’autres fonctionnalités sont fournies par le biais des packages propriétaires qui sont installés avec le serveur. Pour le développement, vous pouvez utiliser comme IDE [Outils R pour Visual Studio](https://www.visualstudio.com/vs/rtvs/) et [Python Tools pour Visual Studio](https://www.visualstudio.com/vs/python/).

Utilisez Microsoft Machine Learning Server quand vous devez :

- Générer et déployer des modèles créés avec R et Python sur un serveur
- Distribuer une formation R et Python à l’échelle sur un cluster Hadoop ou Spark.

## <a name="azure-data-science-virtual-machine"></a>Machine virtuelle DSVM (Data Science Virtual Machine) Azure
La machine virtuelle [DSVM (Data Science Virtual Machine)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) est un environnement de machine virtuelle personnalisé du cloud Microsoft Azure, spécialement conçu pour la science des données. Elle inclut de nombreux outils de science des données populaires et d’autres outils sont préinstallés et préconfigurés afin d’accélérer la création d’applications intelligentes à des fins d’analyse avancée. La machine virtuelle DSVM est disponible dans Windows Server 2016 et 2012, et sur une machine virtuelle Linux, elle est disponible sur Ubuntu 16.04 LTS et sur les distributions OpenLogic CentOS 7.4. 

Utilisez la machine virtuelle DSVM quand vous devez exécuter ou héberger vos tâches sur un même nœud, ou si vous devez monter en puissance à distance le traitement sur un seul ordinateur. L’image DSVM est prise en charge en tant que cible pour les services Expérimentation Azure Machine Learning et Gestion des modèles Azure Machine Learning. 

## <a name="spark-mllib-in-hdinsight"></a>Spark MLLib dans HDInsight
[Spark MLLib dans HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) vous permet de créer des modèles dans le cadre de tâches Spark qui s’exécutent sur les données volumineuses. Spark vous permet de transformer et préparer facilement les données, puis de monter en charge la création du modèle dans une seule tâche. Les modèles créés par le biais de Spark MLLib peuvent être déployés, gérés et surveillés via le service Gestion des modèles Azure Machine Learning. Les exécutions d’apprentissage peuvent être réparties et gérées avec le service Expérimentation Azure Machine Learning. Spark peut également être utilisé pour la montée en charge des tâches de préparation de données créées dans Machine Learning Workbench. 

Utilisez Spark quand vous devez monter en charge le traitement des données et créer des modèles dans le cadre d’un pipeline de données. Vous pouvez créer des tâches Spark dans Scala, Java, Python ou R. 

## <a name="batch-ai-training"></a>Batch AI Training 
[Azure Batch AI Training](https://aka.ms/batchaitraining) vous aide à faire des essais en parallèle avec vos modèles IA à l’aide de tout framework, puis effectue leur apprentissage à l’échelle sur des GPU en cluster. Décrivez les spécifications de votre tâche et la configuration à exécuter, et nous nous occupons du reste. 

Batch AI Training vous permet de monter en charge des tâches d’apprentissage profond sur des GPU en cluster, en utilisant des frameworks comme :

- Cognitive Toolkit
- Caffe
- Chainer
- TensorFlow

Le service Gestion des modèles Azure Machine Learning peut servir à retirer des modèles de Batch AI Training pour les déployer, gérer et surveiller.  Batch AI Training sera intégré au service Expérimentation Azure Machine Learning à l’avenir. 

## <a name="microsoft-cognitive-toolkit-cntk"></a>Microsoft Cognitive Toolkit (CNTK)
[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) est un kit de ressources d’apprentissage profond unifié qui décrit les réseaux neuronaux comme des étapes de calcul dans un graphe orienté. Dans ce graphe orienté, les nœuds terminaux représentent des valeurs d’entrée ou des paramètres réseau, tandis que les autres nœuds représentent des opérations de matrice sur leurs entrées. Cognitive Toolkit vous permet de réaliser et combiner facilement des types de modèles populaires tels que des réseaux DNN de transfert du flux, des réseaux convolutionnels (CNN) et des réseaux récurrents (RNN/LSTM). Il implémente la formation SGD (Stochastic Gradient Descent), rétropropagation des erreurs, avec distinction automatique et parallélisation sur plusieurs GPU et serveurs.

Utilisez Cognitive Toolkit quand vous souhaitez générer un modèle avec l’apprentissage profond.  Cognitive Toolkit peut être utilisé dans tous les services précédents.

## <a name="azure-cognitive-services"></a>Azure Cognitive Services
[Azure Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) regroupe une trentaine d’API permettant de créer des applications qui utilisent des méthodes de communication naturelles. Ces API permettent à vos applications de voir, d’entendre, de parler, de comprendre et d’interpréter les besoins des utilisateurs avec seulement quelques lignes de code. Ajoutez facilement des caractéristiques intelligentes à vos applications, par exemple : 

- Émotion et détection de sentiments
- Vision et reconnaissance vocale
- Language understanding (LUIS)
- Connaissances et recherche

Cognitive Services peut être utilisé pour développer des applications sur des appareils et des plateformes. Les API ne cessent de s’améliorer et sont faciles à configurer. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Étapes suivantes

Consultez la vue d’ensemble [d’Azure Machine Learning](overview-what-is-azure-ml.md).
