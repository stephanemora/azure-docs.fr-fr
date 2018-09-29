---
title: Qu'est-ce que le service Azure Machine Learning ?
description: Explique les concepts de base de l’apprentissage automatique dans le cloud, décrit à quoi il sert, et en définit les principaux termes. Vue d’ensemble d’Azure Machine Learning, une solution de science des données de bout en bout intégrée destinée aux scientifiques des données professionnels pour développer, tester et déployer des applications d’analyse avancées à l’échelle du cloud.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 09/24/2018
ms.openlocfilehash: 6e0294f99d1c2291e84bf2ac5d5b1f771222b551
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433410"
---
# <a name="what-is-azure-machine-learning-service-preview"></a>Qu’est-ce que le service Azure Machine Learning (préversion) ?

Le service Azure Machine Learning (préversion) est un service cloud que vous pouvez utiliser pour développer et déployer des modèles Machine Learning. Avec le service Azure Machine Learning, vous pouvez suivre vos modèles au moment même où vous les créez, entraînez, déployez et gérez, tout cela à l’échelle du cloud.

## <a name="what-is-machine-learning"></a>L’apprentissage automatique - De quoi s’agit-il ?

Machine Learning est une technique de science des données qui permet aux ordinateurs d’utiliser des données existantes afin de prévoir les tendances, les résultats et les comportements futurs. Grâce à Machine Learning, les ordinateurs apprennent sans être explicitement programmés.

Les prévisions ou prédictions générées à partir de Machine Learning peuvent rendre les applications et les appareils plus intelligents. Par exemple, lorsque vous faites vos achats en ligne, le machine learning permet de recommander d’autres produits que vous êtes susceptible d’aimer, en fonction de ce que vous avez acheté. Ou, lorsque vous utilisez votre carte de crédit, le machine learning compare la transaction à une base de données de transactions et aide la banque à détecter des fraudes. Et lorsque votre robot aspirateur nettoie une pièce, le machine learning l’aide à déterminer si le travail est terminé.

## <a name="what-is-azure-machine-learning-service"></a>Qu'est-ce que le service Azure Machine Learning ?

Le service Azure Machine Learning fournit un environnement cloud que vous pouvez utiliser pour développer, entraîner, tester, déployer, gérer et suivre des modèles Machine Learning.

[ ![Workflow du service Azure Machine Learning](./media/overview-what-is-azure-ml/aml.png) ] (./media/overview-what-is-azure-ml/aml.png#lightbox)

Le service Azure Machine Learning prend entièrement en charge les technologies open source. Vous pouvez donc utiliser des dizaines de milliers de packages Python open source avec des composants de machine learning comme TensorFlow et scikit-learn.
Des outils riches, comme les [blocs-notes Jupyter](http://jupyter.org) ou [Visual Studio Code Tools for AI](https://visualstudio.microsoft.com/downloads/ai-tools-vscode/), facilitent l’exploration interactive des données et leur transformation, puis le développement et le test de modèles.
Le service Azure Machine Learning comprend également des fonctionnalités qui [automatisent la génération et le réglage de modèles](tutorial-auto-train-models.md), ce qui vous permet de facilement créer des modèles efficaces et précis.

Avec le service Azure Machine Learning, vous pouvez commencer l’entraînement sur votre ordinateur local, puis monter en puissance sur le cloud. Avec la prise en charge native d’[Azure Batch AI](https://azure.microsoft.com/services/batch-ai/) et les [services avancés de réglage d’hyperparamètres](how-to-tune-hyperparameters.md), vous pouvez créer de meilleurs modèles plus rapidement, en utilisant la puissance du cloud. 

Une fois que vous disposez du modèle approprié, vous pouvez facilement le déployer dans un conteneur tel que Docker. Il sera alors simple à déployer sur [Azure Container Instances](how-to-deploy-to-aci.md) ou [Azure Kubernetes Service](how-to-deploy-to-aks.md), ou vous pouvez utiliser le conteneur dans vos propres déploiements, que ce soit localement ou dans le cloud.
Vous pouvez gérer les modèles déployés et suivre plusieurs exécutions pendant les essais pour trouver la meilleure solution.

[!INCLUDE [aml-preview-note](../../../includes/aml-preview-note.md)]

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Que faire avec Azure Machine Learning ?

Le service Azure Machine Learning peut générer automatiquement un modèle et le régler automatiquement pour vous.
Par consulter un exemple, voir [Tutoriel : entraîner automatiquement un modèle de classification avec le machine learning automatisé dans Azure Machine Learning](tutorial-auto-train-models.md).

Ou, à l’aide du <a href="http://aka.ms/aml-sdk" target="_blank">kit SDK</a> Azure Machine Learning pour Python et des packages Python open source, vous pouvez créer et entraîner vous-même des modèles Machine Learning extrêmement précis et des modèles d’apprentissage profond dans un espace de travail Azure Machine Learning.
Vous pouvez choisir parmi les nombreux composants de machine learning disponibles dans les packages Python open source, comme les suivants :

- <a href="http://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- <a href="https://www.microsoft.com/cognitive-toolkit/" target="_blank">CNTK (COMPUTATIONAL NETWORK TOOLKIT DE MICROSOFT RESEARCH)</a>
- <a href="http://mxnet.io" target="_blank">MXNet</a>

Une fois que vous disposez d’un modèle, vous l’utilisez pour créer un conteneur (par exemple, Docker) qui peut être déployé localement à des fins de test, puis en tant que service web de production dans [Azure Container Instances](how-to-deploy-to-aci.md) ou [Azure Kubernetes Service](how-to-deploy-to-aks.md).

Vous pouvez ensuite gérer vos modèles déployés à l’aide du [portail Azure](https://portal.azure.com/) ou de l’[extension CLI Azure Machine Learning](https://review.docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli).
Vous pouvez évaluer les métriques du modèle, réentraîner et redéployer de nouvelles versions du modèle, tout en suivant les expériences du modèle.

Pour commencer à utiliser le service Azure Machine Learning, consultez [Étapes suivantes](#next-steps).

## <a name="how-is-azure-machine-learning-service-different-from-studio"></a>En quoi le service Azure Machine Learning est-il différent de Studio ?

Azure Machine Learning Studio est un espace de travail collaboratif visuel par glisser-déplacer dans lequel vous pouvez créer, tester et déployer des solutions de machine learning sans avoir besoin d’écrire du code. Il utilise des algorithmes de machine learning prédéfinis et préconfigurés, ainsi que des modules de gestion des données.

Utilisez Machine Learning Studio lorsque vous souhaitez essayer rapidement et simplement des modèles Machine Learning et que les algorithmes de machine learning intégrés sont suffisants pour vos solutions.

Utilisez le service Machine Learning si vous travaillez dans un environnement Python, si vous souhaitez davantage de contrôle sur vos algorithmes de machine learning ou si vous souhaitez utiliser des bibliothèques de machine learning open source.

> [!NOTE]
> Les modèles créés dans Azure Machine Learning Studio ne peuvent pas être déployés ni gérés par le service Azure Machine Learning.

## <a name="free-trial"></a>Essai gratuit
Si vous n’êtes pas abonné, vous pouvez [ouvrir gratuitement un compte Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Vous obtenez des crédits à dépenser en services Azure. Une fois ceux-ci épuisés, vous pouvez conserver le compte et utiliser les [services Azure gratuits](https://azure.microsoft.com/free/). Votre carte de crédit n’est pas débitée tant que vous n’avez pas explicitement modifié vos paramètres pour demander à l’être. Vous pouvez aussi [activer les avantages de l’abonnement MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) : votre abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.

## <a name="next-steps"></a>Étapes suivantes

- Créez un espace de travail de machine learning à l’aide de l’article [Utiliser le portail Azure pour bien démarrer](quickstart-get-started.md).
 
- Suivez dans son intégralité le tutoriel [Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md), pour apprendre à entraîner et déployer des modèles à l’aide du service Azure Machine Learning.

- Pour plus d’informations sur le fait d’autoriser Azure Machine Learning à générer et régler automatiquement votre modèle, consultez [Tutoriel : entraîner automatiquement un modèle de classification avec le machine learning automatisé dans Azure Machine Learning](tutorial-auto-train-models.md).

- Pour un examen technique détaillé du service, consultez [Architecture et concepts du service Azure Machine Learning](concept-azure-machine-learning-architecture.md).

- Pour plus d’informations sur d’autres produits de machine learning proposés par Microsoft, consultez [Autres produits de machine learning de Microsoft](./overview-more-machine-learning.md).


<!-- 

An intro to AML or an end-to-end quickstart video could go here.

In this 9-minute video, learn how you can benefit your app. You'll learn about key features and what a typical workflow looks like. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minutes covers key features and use-cases.
+ 3-4 minutes covers service provisioning. 
+ 4-6 minutes covers Import Data wizard used to create an index using the built-in real estate dataset.

-->
