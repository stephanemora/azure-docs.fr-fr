---
title: Présentation d’Azure Machine Learning
description: Vue d’ensemble d’Azure Machine Learning, solution de science des données de bout en bout intégrée destinée aux scientifiques des données professionnels pour développer, tester et déployer des applications d’analytique avancées à l’échelle du cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.openlocfilehash: aea3e6d0e7b0a1d2f28d67f013701a9af5c3ba70
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75542185"
---
# <a name="what-is-azure-machine-learning"></a>Qu'est-ce que Azure Machine Learning ?

Dans cet article, vous allez découvrir Azure Machine Learning, un environnement basé sur le cloud que vous pouvez utiliser pour entraîner, déployer, automatiser, gérer et suivre des modèles ML. 

Azure Machine Learning peut être utilisé pour tout type de machine learning, du machine learning classique à l’apprentissage profond, supervisé et non supervisé. Que vous préfériez écrire du code Python ou R, ou des options zéro code/peu de code, comme le [concepteur](tutorial-designer-automobile-price-train-score.md), vous pouvez générer, entraîner et suivre des modèles de machine learning et d’apprentissage profond d’une grande précision dans un espace de travail Azure Machine Learning. 

Commencez à vous entraîner sur votre ordinateur local, puis effectuez un scale-out sur le cloud. 

Le service interagit également avec des outils open source populaires tels que PyTorch, TensorFlow et scikit-learn.

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **Version d’évaluation gratuite !**  Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui. Vous obtenez des crédits à dépenser en services Azure. Une fois ceux-ci épuisés, vous pouvez conserver le compte et utiliser les [services Azure gratuits](https://azure.microsoft.com/free/). Votre carte de crédit n’est pas débitée tant que vous n’avez pas explicitement modifié vos paramètres pour demander à l’être.


## <a name="what-is-machine-learning"></a>L’apprentissage automatique - De quoi s’agit-il ?

Machine Learning est une technique de science des données qui permet aux ordinateurs d’utiliser des données existantes afin de prévoir les tendances, les résultats et les comportements futurs. Grâce au machine learning, les ordinateurs apprennent sans programmation explicite.

Les prévisions ou prédictions générées à partir de Machine Learning peuvent rendre les applications et les appareils plus intelligents. Par exemple, quand vous faites vos achats en ligne, le machine learning permet de recommander d’autres produits que vous êtes susceptible de vouloir, en fonction de ce que vous avez acheté. Ou, lorsque vous utilisez votre carte de crédit, le machine learning compare la transaction à une base de données de transactions et aide la banque à détecter des fraudes. Et lorsque votre robot aspirateur nettoie une pièce, le machine learning l’aide à déterminer si le travail est terminé.

## <a name="machine-learning-tools-to-fit-each-task"></a>Outils de Machine Learning adaptés à chaque tâche 

Azure Machine Learning fournit tous les outils dont les développeurs et les scientifiques de données ont besoin pour leurs flux de travaux Machine Learning, notamment :
+ Le [concepteur Azure Machine Learning](tutorial-designer-automobile-price-train-score.md) (préversion) : faites glisser et déposez des modules pour générer vos expériences, puis déployez des pipelines.

+ Notebooks Jupyter : utilisez nos [exemples de notebooks](https://aka.ms/aml-notebooks) ou créez vos propres notebooks pour tirer parti de nos exemples de <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Kits de développement logiciel (SDK) Python</a> pour votre Machine Learning. 

+ Utilisez des scripts R ou des notebooks dans lesquels vous utilisez le <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">Kit de développement logiciel (SDK) pour R</a> pour écrire votre propre code, ou utilisez les modules R dans le concepteur.

+ [Extension Visual Studio Code](service/how-to-vscode-tools.md)

+ [Interface CLI Machine Learning](reference-azure-machine-learning-cli.md)

+ Infrastructures open source telles que PyTorch, TensorFlow et scikit-learn, et bien d’autres encore

Vous pouvez même utiliser [MLflow pour suivre des métriques et déployer des modèles](service/how-to-use-mlflow.md) ou Kubeflow pour [générer des pipelines de flux de travail de bout en bout](https://www.kubeflow.org/docs/azure/).

## <a name="build-ml-models-in-python-or-r"></a>Générer des modèles ML en Python ou R

Commencez l’entraînement sur votre ordinateur local en utilisant le <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Kit de développement logiciel (SDK) Python</a> ou le <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">Kit de développement logiciel (SDK) R</a> Azure Machine Learning. Vous pouvez ensuite monter en charge sur le cloud. 

Les nombreuses [cibles de calcul](how-to-set-up-training-targets.md) disponibles, comme la Capacité de calcul Azure Machine Learning et [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), ainsi que les [services de réglage d’hyperparamètres avancés](how-to-tune-hyperparameters.md), vous permettent de générer de meilleurs modèles plus rapidement en tirant parti de la puissance du cloud.

Vous pouvez aussi [automatiser l’entraînement et l’optimisation de modèle](tutorial-auto-train-models.md) à l’aide du SDK.

## <a name="build-ml-models-with-no-code-tools"></a>Générer des modèles ML avec des outils sans code

Pour un entraînement et un déploiement sans code ou avec peu de code, essayez :

+ **Concepteur Azure Machine Learning (préversion)**

  Utilisez le concepteur pour préparer des données, entraîner, tester, déployer, gérer et suivre des modèles de Machine Learning sans écrire de code. Aucune programmation n’est nécessaire : il suffit de visualiser la connexion des jeux de données et des modules pour construire votre modèle. Essayez le [didacticiel du concepteur](tutorial-designer-automobile-price-train-score.md).

  Vous trouverez plus d’informations dans l’[article sur la vue d’ensemble du concepteur Azure Machine Learning](concept-designer.md). 

  ![Exemple de concepteur Azure Machine Learning](./media/overview-what-is-azure-ml/designer-drag-and-drop.gif)

+ **IU de Machine Learning automatisé**

  Découvrez comment créer des [expériences de ML automatisé](tutorial-first-experiment-automated-ml.md) dans l’interface facile à utiliser. 

  [![Volet de navigation d’Azure Machine Learning Studio](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

## <a name="mlops-deploy--lifecycle-management"></a>MLOps : Déployer et gestion du cycle de vie
Quand vous disposez du modèle adéquat, vous pouvez facilement l’utiliser dans un service web, sur un appareil IoT ou à partir de Power BI. Pour plus d’informations, consultez l’article qui explique [comment et où effectuer le déploiement](how-to-deploy-and-where.md).

Vous pouvez ensuite gérer vos modèles déployés à l’aide du [Kit de développement logiciel (SDK) Azure Machine Learning pour Python](https://aka.ms/aml-sdk), d’[Azure Machine Learning Studio](https://ml.azure.com) ou de l’[interface CLI Machine Learning](reference-azure-machine-learning-cli.md).

Une fois déployé, votre modèle peut retourner des prédictions [en temps réel](how-to-consume-web-service.md) ou [de manière asynchrone](how-to-run-batch-predictions.md) sur de grandes quantités de données.

Et avec les [pipelines de machine learning](concept-ml-pipelines.md) avancés, vous pouvez collaborer à chaque étape, de la préparation des données jusqu’à l’entraînement et à l’évaluation des modèles, en passant par le déploiement. Les pipelines vous permettent d’effectuer les opérations suivantes :

* Automatiser le processus de Machine Learning de bout en bout dans le cloud
* Réutiliser des composants et réexécuter les étapes seulement quand c’est nécessaire
* Utiliser des ressources de calcul différentes à chaque étape
* Exécuter des tâches de scoring par lots

Si vous souhaitez utiliser des scripts pour automatiser votre flux de travail de Machine Learning, l’[interface CLI Machine Learning](reference-azure-machine-learning-cli.md) fournit des outils de ligne de commande qui effectuent des tâches courantes telles que l’envoi d’une séquence d’entraînement ou le déploiement d’un modèle.

Pour commencer à utiliser Azure Machine Learning, consultez [Étapes suivantes](#next-steps).

## <a name="integration-with-other-services"></a>Intégration à d’autres services

Azure Machine Learning fonctionne avec d’autres services sur la plateforme Azure et s’intègre également à des outils open source, comme Git et MLFlow.

+ Des cibles de calcul comme __Azure Kubernetes Service__, __Azure Container Instances__, __Azure Databricks__, __Azure Data Lake Analytics__ et __Azure HDInsight__. Pour plus d’informations sur les cibles de calcul, consultez [Présentation des cibles de calcul](concept-compute-target.md).
+ __Azure Event Grid__. Pour plus d’informations, consultez [Consommer des événements Azure Machine Learning](concept-event-grid-integration.md).
+ __Azure Monitor__. Pour plus d’informations, consultez [Supervision d’Azure Machine Learning](service/monitor-azure-machine-learning.md).
+ Des magasins de données comme __Comptes de stockage Azure__, __Azure Data Lake Storage__, __Azure SQL Database__, __Azure Database pour PostgreSQL__ et __Azure Open Datasets__. Pour plus d’informations, consultez [Accéder aux données dans les services de stockage Azure](how-to-access-data.md) et [Créer des jeux de données avec Azure Open Datasets](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets).
+ __Réseaux virtuels Azure__. Pour plus d’informations, consultez [Sécuriser l’expérimentation et l’inférence dans un réseau virtuel](how-to-enable-virtual-network.md).
+ __Azure Pipelines__. Pour plus d’informations, consultez [Entraîner et déployer des modèles Machine Learning](/azure/devops/pipelines/targets/azure-machine-learning).
+ __Journaux du dépôt Git__. Pour plus d’informations, consultez [Intégration de Git](concept-train-model-git-integration.md).
+ __MLFlow__. Pour plus d’informations, consultez [MLflow pour suivre des métriques et déployer des modèles](service/how-to-use-mlflow.md) 
+ __Kubeflow__. Pour plus d’informations, consultez [Générer des pipelines de workflows de bout en bout](https://www.kubeflow.org/docs/azure/).

### <a name="secure-communications"></a>Communications sécurisées

Votre compte de stockage Azure, vos cibles de calcul et d’autres ressources peuvent être utilisées de façon sécurisée au sein d’un réseau virtuel pour entraîner des modèles et effectuer l’inférence. Pour plus d’informations, consultez [Sécuriser l’expérimentation et l’inférence dans un réseau virtuel](how-to-enable-virtual-network.md).

## <a name="sku"></a>Éditions De Base et Entreprise

Azure Machine Learning propose deux éditions adaptées à vos besoins de Machine Learning :
+ De base (mise à la disposition générale)
+ Entreprise (préversion)

Ces éditions déterminent les outils de Machine Learning disponibles pour les développeurs et les scientifiques de données depuis leur espace de travail.   

Les espaces de travail De base vous permettent de continuer à utiliser Azure Machine Learning et de payer uniquement les ressources Azure consommées pendant le processus de Machine Learning. Les espaces de travail de l’édition Entreprise sont facturés uniquement pour leur consommation Azure lorsque l’édition est en préversion. Vous trouverez plus d’informations sur les éléments disponibles dans la [vue d’ensemble et la page de tarification de l’édition](https://azure.microsoft.com/pricing/details/machine-learning/) d’Azure Machine Learning. 

Vous attribuez l’édition chaque fois que vous créez un espace de travail. De plus, les espaces de travail préexistants ont été convertis pour vous dans l’édition De base. L’édition De base comprend toutes les fonctionnalités qui étaient déjà généralement disponibles à partir d’octobre 2019. Toutes les expériences de ces espaces de travail qui ont été créées à l’aide des fonctionnalités de l’édition Entreprise restent disponibles en lecture seule jusqu’à ce que vous procédiez à une mise à niveau vers Entreprise. Découvrez comment [mettre à niveau un espace de travail De base vers l’édition Entreprise](how-to-manage-workspace.md#upgrade). 

Les clients sont responsables des coûts liés au calcul et aux autres ressources Azure pendant cette période.

## <a name="next-steps"></a>Étapes suivantes

- Créez votre première expérience avec la méthode de votre choix :
  + [Utiliser des notebooks Python pour entraîner et déployer des modèles ML](tutorial-1st-experiment-sdk-setup.md)
  + [Utiliser R Markdown pour entraîner et déployer des modèles ML](tutorial-1st-r-experiment.md) 
  + [Utiliser le Machine Learning automatisé pour entraîner et déployer des modèles ML](  service/tutorial-first-experiment-automated-ml.md) 
  + [Utiliser les fonctionnalités glisser-déplacer du concepteur pour entraîner et déployer](tutorial-designer-automobile-price-train-score.md) 
  + [Utiliser l’interface CLI Machine Learning pour entraîner et déployer un modèle](tutorial-train-deploy-model-cli.md)

- Découvrez les [pipelines de machine learning](/azure/machine-learning/service/concept-ml-pipelines) pour générer, optimiser et gérer vos scénarios d’apprentissage.

- Consultez l’article détaillé [Architecture et concepts Azure Machine Learning](concept-azure-machine-learning-architecture.md).
