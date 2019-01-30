---
title: Comparer les options de produits de machine learning
titleSuffix: Microsoft
description: Comparez la gamme de produits proposés par Microsoft pour générer, déployer et gérer vos modèles Machine Learning. Décidez quel produits choisir pour votre solution.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 1/17/2019
ms.openlocfilehash: d5660d1a4d5d2a26af57e2a2411552e3f6267379
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54389144"
---
# <a name="what-are-the-machine-learning-products-at-microsoft"></a>Quels sont les produits de machine learning proposés par Microsoft ?

Microsoft propose toute une gamme de produits pour générer, déployer et gérer vos modèles Machine Learning. Comparez ces produits et choisissez celui qu’il vous faut pour optimiser le développement de vos solutions de machine learning.


**Options cloud**

Les options suivantes sont disponibles pour le machine learning dans le cloud Azure.

| Options&nbsp;cloud | Présentation | Fonctionnalités |
|-|-|-|
| [Service Azure Machine Learning](#azure-machine-learning-services) | Service cloud managé pour ML  | Entraîner, déployer et gérer des modèles dans Azure à l’aide de Python et de l’interface CLI |
| [Azure Machine Learning Studio](#azure-machine-learning-studio) | Interface visuelle de type « glisser-déplacer » pour ML | Créer, expérimenter et déployer des modèles avec des algorithmes préconfigurés (Python et R)|
| [Azure Databricks](#azure-databricks) | Plateforme d’analytique basée sur Spark | Générer et déployer des modèles et des workflows de données |
| [Azure Cognitive Services](#azure-cognitive-services) | Services Azure avec des modèles AI et ML prédéfinis | Ajouter facilement des fonctionnalités intelligentes à vos applications |
| [Azure Data Science Virtual Machine](#azure-data-science-virtual-machine) | Machine virtuelle avec des outils de science des données préinstallés | Développer des solutions ML dans un environnement préconfiguré |

**Options locales**

Les options suivantes sont disponibles pour le machine learning local. Les serveurs locaux peuvent également être exécutés sur une machine virtuelle dans le cloud.

| Options&nbsp;locales | Présentation | Fonctionnalités |
|-|-|-|
| [Services de Machine Learning SQL Server](#sql-server-machine-learning-services) | Moteur d’analytique incorporé à SQL | Générer et déployer des modèles dans SQL Server |
| [Microsoft Machine Learning Server](#microsoft-machine-learning-server) | Serveur d’entreprise autonome pour l’analyse prédictive | Générer et déployer des modèles créés avec R et Python |

**Outils de développement**

Les outils de développement suivants sont disponibles pour le machine learning.

| Outils&nbsp;développement | Présentation | Fonctionnalités |
|-|-|-|
| [ML.NET](#mlnet) | SDK ML open source, multiplateforme | Développer des solutions ML pour des applications .NET |
| [Windows ML](#windows-ml) | Plateforme ML Windows 10 | Évaluer des modèles entraînés sur un appareil Windows 10 |







## <a name="azure-machine-learning-service"></a>Service Azure Machine Learning

Le service [Azure Machine Learning](overview-what-is-azure-ml.md) est un service cloud entièrement managé qui permet d’entraîner, de déployer et de gérer des modèles ML à grande échelle. Il prend entièrement en charge les technologies open source. Vous pouvez donc utiliser des dizaines de milliers de packages Python open source tels que TensorFlow, PyTorch et scikit-learn. Avec des outils puissants comme [Azure Notebooks](https://notebooks.azure.com/), [Jupyter Notebook](http://jupyter.org) ou l’extension [Azure Machine Learning pour Visual Studio Code](https://aka.ms/vscodetoolsforai), vous pouvez facilement explorer et transformer des données, puis entraîner et déployer des modèles. Le service Azure Machine Learning comprend des fonctionnalités qui automatisent la génération de modèles et qui vous permettent de les régler de manière simple, efficace et précise.

Utilisez le service Azure Machine Learning pour entraîner, déployer et gérer des modèles ML à l’aide de Python et de l’interface CLI à l’échelle du cloud.

Essayez la [version gratuite ou payante d’Azure Machine Learning service](http://aka.ms/AMLFree) dès aujourd’hui.

## <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

[Azure Machine Learning Studio](../studio/what-is-ml-studio.md) est un espace de travail visuel et interactif que vous pouvez utiliser pour générer, tester et déployer avec facilité et rapidité des modèles à l’aide d’algorithmes de machine learning prédéfinis. Machine Learning Studio publie des modèles en tant que services web pouvant facilement être consommés par des applications personnalisées ou des outils décisionnels tels qu'Excel.
Aucune programmation n’est requise : vous construisez votre modèle Machine Learning en connectant des jeux de données et des modules d’analyse sur un canevas interactif, puis vous le déployez en quelques clics.

Utilisez Machine Learning Studio quand vous souhaitez développer et déployer des modèles sans écrire de code.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) est une plateforme d’analytique basée sur Apache Spark et optimisée pour la plateforme de services cloud Microsoft Azure. Databricks est intégré à Azure pour offrir une configuration en un clic, des workflows simplifiés et un espace de travail interactif permettant aux scientifiques des données, aux ingénieurs des données et aux analystes métier de collaborer.
Utilisez du code Python, R, Scala et SQL dans des notebooks basés sur le web pour interroger, visualiser et modéliser des données.

Utilisez Databricks quand vous souhaitez travailler à plusieurs sur la conception de solutions de machine learning sur Apache Spark.

## <a name="azure-cognitive-services"></a>Azure Cognitive Services

[Azure Cognitive Services](/azure/cognitive-services/welcome) regroupe un ensemble d’API permettant de créer des applications qui utilisent des méthodes de communication naturelles. Ces API permettent à vos applications de voir, d’entendre, de parler, de comprendre et d’interpréter les besoins des utilisateurs avec seulement quelques lignes de code. Ajoutez facilement des caractéristiques intelligentes à vos applications, par exemple : 

- Émotion et détection de sentiments
- Vision et reconnaissance vocale
- Language understanding (LUIS)
- Connaissances et recherche

Utilisez Cognitive Services pour développer des applications sur divers appareils et plateformes. Les API ne cessent de s’améliorer et sont faciles à configurer.

## <a name="azure-data-science-virtual-machine"></a>Machine virtuelle DSVM (Data Science Virtual Machine) Azure

[Azure Data Science Virtual Machine](../data-science-virtual-machine/overview.md) est un environnement de machine virtuelle personnalisé sur le cloud Microsoft Azure qui est spécialement conçu pour la science des données. Elle inclut de nombreux outils de science des données populaires et d’autres outils sont préinstallés et préconfigurés afin d’accélérer la création d’applications intelligentes à des fins d’analyse avancée.

Data Science Virtual Machine est pris en charge en tant que cible pour le service Azure Machine Learning.
Elle est disponible dans des versions pour Windows et Linux Ubuntu (le service Azure Machine Learning n’est pas pris en charge sur Linux CentOS).
Pour obtenir des informations de version spécifiques et la liste des fonctionnalités incluses, consultez [Présentation d’Azure Data Science Virtual Machine](../data-science-virtual-machine/overview.md).

Utilisez la machine virtuelle DSVM quand vous devez exécuter ou héberger vos tâches sur un même nœud, ou si vous devez monter en puissance à distance le traitement sur un seul ordinateur.

## <a name="sql-server-machine-learning-services"></a>Services de Machine Learning SQL Server

[SQL Server Microsoft Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) ajoute l’analyse statistique, la visualisation des données et l’analytique prédictive dans R et Python pour les données relationnelles dans les bases de données SQL Server. Les bibliothèques R et Python de Microsoft incluent des algorithmes de modélisation et ML avancés que vous pouvez exécuter en parallèle et à grande échelle dans SQL Server.

Utilisez SQL Server Machine Learning Services quand vous souhaitez bénéficier d’une intelligence artificielle intégrée et d’une analytique prédictive des données relationnelles dans SQL Server.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

[Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server) est un serveur d’entreprise pour l’hébergement et la gestion des charges de travail parallèles et distribuées des processus R et Python. Microsoft Machine Learning Server s’exécute sur Linux, Windows, Hadoop et Apache Spark. Il est également disponible sur [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Il fournit un moteur d’exécution pour les solutions créées à l’aide des packages [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) et [MicrosoftML](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package). Il étend également le code R et Python open source en prenant en charge l’analytique hautes performances, l’analyse statistique, le machine learning et les jeux de données volumineux. Cette fonctionnalité est fournie par le biais de packages propriétaires qui sont installés avec le serveur. Pour le développement, vous pouvez utiliser comme IDE [Outils R pour Visual Studio](https://www.visualstudio.com/vs/rtvs/) et [Python Tools pour Visual Studio](https://www.visualstudio.com/vs/python/).

Utilisez Microsoft Machine Learning Server pour créer des modèles créés avec R et Python sur un serveur et les rendre opérationnels, ou distribuer un entraînement R et Python à grande échelle sur un cluster Hadoop ou Spark.

## <a name="mlnet"></a>ML.NET

[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/) est un framework de machine learning gratuit, open source et multiplateforme qui vous permet de créer des solutions de machine learning personnalisées et de les intégrer à vos applications .NET.

Utilisez ML.NET quand vous avez besoin d’intégrer des solutions de machine learning à vos applications .NET.

## <a name="windows-ml"></a>Windows ML

[Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/) vous permet d’utiliser des modèles Machine Learning entraînés dans vos applications. Les modèles entraînés sont évalués localement sur des appareils Windows 10.

Utilisez Windows ML quand vous avez besoin d’utiliser des modèles Machine Learning entraînés dans vos applications Windows.

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir l’ensemble des produits de développement Microsoft axés sur l’intelligence artificielle, consultez [Plateforme Microsoft AI](https://www.microsoft.com/ai)
- Pour apprendre à développer des solutions d’intelligence artificielle, consultez [Microsoft AI School](https://aischool.microsoft.com/learning-paths)
