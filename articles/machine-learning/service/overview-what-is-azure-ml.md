---
title: Qu’est-ce que c’est
titleSuffix: Azure Machine Learning service
description: Vue d’ensemble du service Azure Machine Learning, solution de science des données de bout en bout intégrée qui s’adresse aux scientifiques des données professionnels pour développer, tester et déployer des applications d’analytique avancée à l’échelle du cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 1c369a75bbaa0853386e0139b94c119e5bb3675a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251535"
---
# <a name="what-is-azure-machine-learning-service"></a>Qu'est-ce que le service Azure Machine Learning ?

Le service Azure Machine Learning est un service cloud que vous utilisez pour entraîner, déployer, automatiser et gérer des modèles Machine Learning, le tout à l’échelle vaste du cloud.

## <a name="what-is-machine-learning"></a>L’apprentissage automatique - De quoi s’agit-il ?

Machine Learning est une technique de science des données qui permet aux ordinateurs d’utiliser des données existantes afin de prévoir les tendances, les résultats et les comportements futurs. Grâce au machine learning, les ordinateurs apprennent sans programmation explicite.

Les prévisions ou prédictions générées à partir de Machine Learning peuvent rendre les applications et les appareils plus intelligents. Par exemple, quand vous faites vos achats en ligne, le machine learning permet de recommander d’autres produits que vous êtes susceptible de vouloir, en fonction de ce que vous avez acheté. Ou, lorsque vous utilisez votre carte de crédit, le machine learning compare la transaction à une base de données de transactions et aide la banque à détecter des fraudes. Et lorsque votre robot aspirateur nettoie une pièce, le machine learning l’aide à déterminer si le travail est terminé.

## <a name="what-is-azure-machine-learning-service"></a>Qu'est-ce que le service Azure Machine Learning ?

Le service Azure Machine Learning fournit un environnement cloud que vous pouvez utiliser pour développer, entraîner, tester, déployer, gérer et suivre des modèles Machine Learning.

[ ![Workflow du service Azure Machine Learning](./media/overview-what-is-azure-ml/aml.png)] (./media/overview-what-is-azure-ml/aml.png#lightbox)

Le service Azure Machine Learning prend entièrement en charge les technologies open source. Vous pouvez utiliser des dizaines de milliers de packages Python open source avec des composants de machine learning. Par exemple : TensorFlow et scikit-learn.
La prise en charge d’outils complets facilite l’exploration interactive des données, leur transformation, puis le développement et le test de modèles. Par exemple : [les notebooks Jupyter](http://jupyter.org) ou l’extension [Azure Machine Learning pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai#overview).
Le service Azure Machine Learning comprend également des fonctionnalités qui [automatisent la génération et le réglage de modèles](tutorial-auto-train-models.md), ce qui vous permet de facilement créer des modèles efficaces et précis.

Avec le service Azure Machine Learning, vous pouvez commencer l’entraînement sur votre ordinateur local, puis effectuer un scale-out sur le cloud. Les nombreuses [cibles de calcul](how-to-set-up-training-targets.md) disponibles, comme la Capacité de calcul Azure Machine Learning et [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), ainsi que les [services de réglage d’hyperparamètres avancés](how-to-tune-hyperparameters.md), vous permettent de générer de meilleurs modèles plus rapidement en tirant parti de la puissance du cloud.

Une fois que vous disposez du modèle approprié, vous pouvez facilement le déployer dans un conteneur tel que Docker. Vous pouvez donc simplement effectuer le déploiement sur Azure Container Instances ou Azure Kubernetes Service. Vous pouvez aussi utiliser le conteneur dans vos propres déploiements, localement ou dans le cloud. Pour plus d’informations, consultez l’article qui explique [comment et où effectuer le déploiement](how-to-deploy-and-where.md).

Vous pouvez gérer les modèles déployés et suivre plusieurs exécutions pendant les essais pour trouver la meilleure solution.
Une fois déployé, votre modèle peut retourner des prédictions [en temps réel](how-to-consume-web-service.md) ou [de manière asynchrone](how-to-run-batch-predictions.md) sur de grandes quantités de données.

Et avec les [pipelines de machine learning](concept-ml-pipelines.md) avancés, vous pouvez collaborer sur toutes les étapes de préparation des données et d’entraînement, d’évaluation et de déploiement du modèle.

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Que faire avec Azure Machine Learning ?

Le service Azure Machine Learning peut entraîner automatiquement un modèle et le régler automatiquement pour vous.
Pour en voir un exemple, consultez [Entraîner un modèle de régression avec le machine learning automatisé](tutorial-auto-train-models.md).

Avec le <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> Azure Machine Learning pour Python et des packages Python open source, vous pouvez créer et entraîner vous-même des modèles Machine Learning extrêmement précis et des modèles d’apprentissage profond dans un espace de travail de service Azure Machine Learning.
Vous pouvez choisir parmi les nombreux composants de machine learning disponibles dans les packages Python open source, par exemple :

- <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- <a href="https://www.microsoft.com/en-us/cognitive-toolkit/" target="_blank">CNTK (COMPUTATIONAL NETWORK TOOLKIT DE MICROSOFT RESEARCH)</a>
- <a href="http://mxnet.io" target="_blank">MXNet</a>

Une fois que vous avez un modèle, utilisez-le pour créer un conteneur (par exemple Docker) qui peut être déployé localement pour les tests. Une fois les tests effectués, vous pouvez déployer le modèle sous forme de service web de production dans Azure Container Instances ou Azure Kubernetes Service. Pour plus d’informations, consultez l’article qui explique [comment et où effectuer le déploiement](how-to-deploy-and-where.md).

Vous pouvez ensuite gérer vos modèles déployés à l’aide du [SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk) ou du [portail Azure](https://portal.azure.com/).
Vous pouvez évaluer les métriques du modèle, réentraîner et redéployer de nouvelles versions du modèle, tout en suivant les expériences du modèle.

Pour commencer à utiliser le service Azure Machine Learning, consultez [Étapes suivantes](#next-steps).

## <a name="how-is-azure-machine-learning-service-different-from-machine-learning-studio"></a>En quoi le service Azure Machine Learning est-il différent de Machine Learning Studio ?

Azure Machine Learning Studio est un espace de travail collaboratif visuel par glisser-déplacer dans lequel vous pouvez créer, tester et déployer des solutions de machine learning sans avoir besoin d’écrire du code. Il utilise des algorithmes de machine learning prédéfinis et préconfigurés, ainsi que des modules de gestion de données.

Utilisez Machine Learning Studio lorsque vous souhaitez essayer rapidement et simplement des modèles Machine Learning et que les algorithmes de machine learning intégrés sont suffisants pour vos solutions.

Utilisez le service Machine Learning si vous travaillez dans un environnement Python, si vous souhaitez davantage de contrôle sur vos algorithmes de machine learning ou si vous souhaitez utiliser des bibliothèques de machine learning open source.

> [!NOTE]
> Les modèles créés dans Azure Machine Learning Studio ne peuvent pas être déployés ni gérés par le service Azure Machine Learning.

## <a name="free-trial"></a>Essai gratuit

Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](http://aka.ms/AMLFree) dès aujourd’hui.

Vous obtenez des crédits à dépenser en services Azure. Une fois ceux-ci épuisés, vous pouvez conserver le compte et utiliser les [services Azure gratuits](https://azure.microsoft.com/free/). Votre carte de crédit n’est pas débitée tant que vous n’avez pas explicitement modifié vos paramètres pour demander à l’être. Vous pouvez aussi [activer les avantages de l’abonnement MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), qui vous donnent droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.

## <a name="next-steps"></a>Étapes suivantes

- Créez un espace de travail de service Machine Learning pour commencer [à l’aide du portail Azure](quickstart-get-started.md) ou [dans Python](quickstart-create-workspace-with-python.md).

- Suivez l’intégralité du tutoriel [Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md).

- [Utilisez Azure Machine Learning pour générer et régler automatiquement un modèle](tutorial-auto-train-models.md).

- Utilisez le [kit de développement logiciel (SDK) de préparation des données d’Azure Machine Learning](https://aka.ms/data-prep-sdk) pour préparer vos données.

- Découvrez les [pipelines de machine learning](/azure/machine-learning/service/concept-ml-pipelines) pour générer, optimiser et gérer vos scénarios d’apprentissage.

- Consultez l’article approfondi sur les [concepts et l’architecture du service Azure Machine Learning](concept-azure-machine-learning-architecture.md).

- Pour plus d’informations, consultez les [autres produits Microsoft de machine learning](./overview-more-machine-learning.md).


<!-- 

An intro to AML or an end-to-end quickstart video could go here.

In this 9-minute video, learn how you can benefit your app. You'll learn about key features and what a typical workflow looks like. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minutes covers key features and use-cases.
+ 3-4 minutes covers service provisioning. 
+ 4-6 minutes covers Import Data wizard used to create an index using the built-in real estate dataset.

-->
