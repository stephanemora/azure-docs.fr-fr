---
title: Présentation d’Azure Machine Learning
description: Vue d’ensemble d’Azure Machine Learning, solution de science des données de bout en bout intégrée destinée aux scientifiques des données professionnels pour développer, tester et déployer des applications d’analytique avancées à l’échelle du cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 08/05/2019
ms.custom: seodec18
ms.openlocfilehash: 0a70b1c9d3ac888f0f77cf0f6e9ca37bc6cd4324
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999933"
---
# <a name="what-is-azure-machine-learning"></a>Qu'est-ce que Azure Machine Learning ?

Azure Machine Learning est un service cloud que vous utilisez pour entraîner, déployer, automatiser et gérer des modèles Machine Learning, le tout à l’échelle vaste que propose le cloud.

## <a name="what-is-machine-learning"></a>L’apprentissage automatique - De quoi s’agit-il ?

Machine Learning est une technique de science des données qui permet aux ordinateurs d’utiliser des données existantes afin de prévoir les tendances, les résultats et les comportements futurs. Grâce au machine learning, les ordinateurs apprennent sans programmation explicite.

Les prévisions ou prédictions générées à partir de Machine Learning peuvent rendre les applications et les appareils plus intelligents. Par exemple, quand vous faites vos achats en ligne, le machine learning permet de recommander d’autres produits que vous êtes susceptible de vouloir, en fonction de ce que vous avez acheté. Ou, lorsque vous utilisez votre carte de crédit, le machine learning compare la transaction à une base de données de transactions et aide la banque à détecter des fraudes. Et lorsque votre robot aspirateur nettoie une pièce, le machine learning l’aide à déterminer si le travail est terminé.

## <a name="what-is-azure-machine-learning"></a>Qu'est-ce que Azure Machine Learning ?

Azure Machine Learning fournit un environnement cloud que vous pouvez utiliser pour préparer les données, entraîner, tester, déployer, gérer et suivre des modèles Machine Learning. Commencez à vous entraîner sur votre ordinateur local, puis effectuez un scale-out sur le cloud. Le service prend entièrement en charge les technologies open source comme PyTorch, TensorFlow et scikit-learn et peut être utilisé pour n’importe quel type de machine learning, du machine learning classique au deep learning (apprentissage profond), pour l’apprentissage supervisé ou non supervisé.

Explorez et préparez des données, entraînez et testez des modèles, puis déployez-les à l’aide d’outils élaborés comme :
+ Une [interface visuelle](ui-tutorial-automobile-price-train-score.md) dans laquelle vous pouvez glisser-déplacer des modules afin de générer vos expériences et déployer des modèles
+ Des [notebooks Jupyter](https://jupyter.org) dans lesquels vous pouvez utiliser les [SDK](https://docs.microsoft.com/azure/machine-learning) pour écrire votre propre code, comme [ces exemples de notebooks](https://aka.ms/aml-notebooks)
+ [Extension Visual Studio Code](how-to-vscode-tools.md)


> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

## <a name="what-can-i-do-with-azure-machine-learning"></a>Que faire avec Azure Machine Learning ?

Utilisez le <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">kit SDK Python Azure Machine Learning</a> avec des packages Python open source ou utilisez l’[interface visuelle (préversion)](ui-tutorial-automobile-price-train-score.md) pour générer et entraîner vous-même des modèles Machine Learning et Deep Learning extrêmement précis dans un espace de travail Azure Machine Learning.

Vous pouvez choisir parmi les nombreux composants de machine learning disponibles dans les packages Python open source, comme <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>, <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>, <a href="https://pytorch.org" target="_blank">PyTorch</a> et <a href="https://mxnet.io" target="_blank">MXNet</a>.

Si vous écrivez du code ou utilisez l’interface visuelle, vous pouvez suivre plusieurs exécutions que vous expérimentez afin de trouver la meilleure solution et gérer les modèles déployés.

### <a name="code-first-experience"></a>Expérience orientée code

Commencez à vous entraîner sur votre ordinateur local en utilisant le <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK Python Azure Machine Learning</a>, puis effectuez un scale-out sur le cloud. Les nombreuses [cibles de calcul](how-to-set-up-training-targets.md) disponibles, comme la Capacité de calcul Azure Machine Learning et [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), ainsi que les [services de réglage d’hyperparamètres avancés](how-to-tune-hyperparameters.md), vous permettent de générer de meilleurs modèles plus rapidement en tirant parti de la puissance du cloud.

Vous pouvez aussi [automatiser l’entraînement et l’optimisation de modèle](tutorial-auto-train-models.md) à l’aide du SDK.

### <a name="ui-based-low-code-experience"></a>Expérience d’interface utilisateur à faible niveau de codage

Pour l’entraînement sans code, essayez :

+ Création d’[expériences de ML automatisé](tutorial-first-experiment-automated-ml.md) dans l’interface facile à utiliser.
+ L’[interface visuelle pour l’expérimentation par glisser-déplacer](ui-tutorial-automobile-price-train-score.md).
  ![Interface visuelle pour Azure Machine Learning](media/overview-what-is-azure-ml/visual-interface.png)



### <a name="operationalization-mlops"></a>Opérationnalisation (MLOps)

Quand vous disposez du modèle adéquat, vous pouvez facilement l’utiliser dans un service web, sur un appareil IoT ou à partir de Power BI. Pour plus d’informations, consultez l’article qui explique [comment et où effectuer le déploiement](how-to-deploy-and-where.md).

Vous pouvez ensuite gérer vos modèles déployés à l’aide du [SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk), du [portail Azure](https://portal.azure.com/) ou de la [page d’accueil de votre espace de travail (préversion)](https://ml.azure.com).

Une fois déployé, votre modèle peut retourner des prédictions [en temps réel](how-to-consume-web-service.md) ou [de manière asynchrone](how-to-run-batch-predictions.md) sur de grandes quantités de données.

Et avec les [pipelines de machine learning](concept-ml-pipelines.md) avancés, vous pouvez collaborer à chaque étape, de la préparation des données jusqu’à l’entraînement et à l’évaluation des modèles, en passant par le déploiement. Les pipelines vous permettent d’effectuer les opérations suivantes :

* Automatiser le processus de Machine Learning de bout en bout dans le cloud
* Réutiliser des composants et réexécuter les étapes uniquement si vous en avez besoin
* Utiliser des ressources de calcul différentes à chaque étape
* Exécuter des tâches de scoring par lots

Pour commencer à utiliser Azure Machine Learning, consultez [Étapes suivantes](#next-steps).

## <a name="how-does-azure-machine-learning-differ-from-studio"></a>En quoi Azure Machine Learning se distingue-t-il de Studio ?

[Machine Learning Studio](../studio/what-is-ml-studio.md) est un espace de travail visuel collaboratif par glisser-déplacer dans lequel vous pouvez générer, tester et déployer des solutions de machine learning sans avoir à écrire du code. Il s’appuie sur des algorithmes de machine learning et des modules de traitement des données prédéfinis et préconfigurés, mais aussi sur une plateforme de calcul propriétaire.

Azure Machine Learning propose les deux kits SDK **plus** une interface visuelle (préversion) qui vous permet de préparer rapidement les données, d’entraîner et de déployer des modèles Machine Learning. Cette interface visuelle (préversion) offre une expérience de glisser-déplacer similaire à Studio. Cependant, contrairement à la plateforme de calcul propriétaire de Studio, l’interface visuelle utilise vos propres ressources de calcul et est entièrement intégrée à Azure Machine Learning.

Voici une comparaison rapide.

|| Machine Learning Studio | Azure Machine Learning :<br/>Interface visuelle|
|---| --- | --- |
|| Disponibilité générale (GA) | En préversion|
|Modules pour l’interface| Divers | Jeu initial de modules courants|
|Cibles de calcul d’entraînement| Cible de calcul propriétaire, prise en charge CPU uniquement| Prend en charge le calcul Azure Machine Learning, GPU ou CPU<br/>(Autres calculs pris en charge dans le SDK)|
|Cibles de calcul de déploiement| Format de service web propriétaire, non personnalisable | Options de sécurité d’entreprise et Azure Kubernetes Service. <br/>([Autres calculs](how-to-deploy-and-where.md) pris en charge dans le SDK) |
|Entraînement de modèle automatisé et optimisation des hyperparamètres | Non | Pas encore dans l’interface visuelle. <br/> (Pris en charge dans le SDK et sur le portail Azure/sur la page d’accueil de l’espace de travail.) |

Essayer l’interface visuelle (préversion) avec le [Didacticiel : Prédire le prix de voitures à l’aide de l’interface visuelle](ui-tutorial-automobile-price-train-score.md).

> [!NOTE]
> Les modèles créés dans Studio ne peuvent pas être déployés ni gérés par Azure Machine Learning. En revanche, les modèles créés et déployés dans l’interface visuelle du service peuvent être gérés via l’espace de travail Azure Machine Learning.

## <a name="free-trial"></a>Essai gratuit

Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

Vous obtenez des crédits à dépenser en services Azure. Une fois ceux-ci épuisés, vous pouvez conserver le compte et utiliser les [services Azure gratuits](https://azure.microsoft.com/free/). Votre carte de crédit n’est pas débitée tant que vous n’avez pas explicitement modifié vos paramètres pour demander à l’être. Vous pouvez aussi [activer les avantages de l’abonnement MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), qui vous donnent droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.

## <a name="next-steps"></a>Étapes suivantes

- [Créez un espace de travail du service Machine Learning](how-to-manage-workspace.md) pour démarrer.

- Suivez les tutoriels complets :
  + [Créer un espace de travail et entraîner votre premier modèle ML](tutorial-1st-experiment-sdk-setup.md)
  + [Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md)


- Découvrez les [pipelines de machine learning](/azure/machine-learning/service/concept-ml-pipelines) pour générer, optimiser et gérer vos scénarios d’apprentissage.

- Consultez l’article détaillé [Architecture et concepts Azure Machine Learning](concept-azure-machine-learning-architecture.md).

- Pour plus d’informations, consultez les [autres produits Microsoft de machine learning](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning).
