---
title: Qu’est-ce que le Machine Learning (ML) automatisé / AutoML
titleSuffix: Azure Machine Learning
description: Découvrez comment Azure Machine Learning peut choisir automatiquement un algorithme pour vous et générer un modèle à partir de celui-ci pour vous permettre de gagner du temps en utilisant les paramètres et les critères que vous fournissez de façon à sélectionner le meilleur algorithme pour votre modèle.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 02/28/2020
ms.openlocfilehash: c8864e00be9f491d87478c253286070b9334a26e
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383189"
---
# <a name="what-is-automated-machine-learning"></a>Qu’est-ce que le machine learning automatisé ?

Le machine learning automatisé, également appelé ML automatisé, est le processus d’automatisation des tâches fastidieuses et itératives de développement de modèle Machine Learning. Il permet aux chercheurs de données, analystes et développeurs de créer des modèles ML à grande échelle, efficaces et productifs, tout en maintenant la qualité du modèle. L’apprentissage automatique automatisé se base sur une innovation de notre [division Microsoft Research](https://arxiv.org/abs/1705.05355).

Le développement de modèle Machine Learning traditionnel consomme beaucoup de ressources, nécessitant une connaissance significative du domaine et du temps pour produire et comparer des dizaines de modèles. Le Machine Learning automatisé permet de réduire le temps nécessaire pour obtenir des modèles ML prêts pour la production avec une facilité et une efficacité extraordinaires.

 


## <a name="when-to-use-automated-ml"></a>Quand utiliser un Machine Learning automatisé

Appliquez le Machine Learning automatisé lorsque vous souhaitez qu’Azure Machine Learning effectue l’apprentissage d’un modèle et le règle à votre place à l’aide de la métrique cible que vous spécifiez. Le ML automatisé démocratise le processus de développement de modèle Machine Learning et permet à ses utilisateurs, quel que soit leur expertise en matière de science des données, d’identifier un pipeline de Machine Learning de bout en bout pour tout problème.

Les scientifiques des données, analystes et développeurs de différents secteurs peuvent utiliser le Machine Learning automatisé pour ce qui suit :

+ Implémenter des solutions de Machine Learning sans disposer d’une connaissance approfondie de la programmation.
+ Économiser du temps et des ressources.
+ Tirer parti des meilleures pratiques en matière de science des données.
+ Fournir une résolution de problème agile.

Le tableau suivant liste les cas d’usage courants du ML automatisé. 

classification ;| Prévision de séries chronologiques | régression ;
---|---|---
[Détection des fraudes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[Prévisions de ventes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)|[Prédiction des performances du processeur](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)
|[Prédiction marketing](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[Prédiction de la demande](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
|[Classification des données de groupes de discussion](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)|[Prévision de production de boissons](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)|

## <a name="design-automated-ml-experiments"></a>Concevoir des expériences de Machine Learning automatisé

**Azure Machine Learning** vous permet de concevoir et d’exécuter vos expériences d’entraînement de Machine Learning automatisé en effectuant les étapes suivantes :

1. **Identifier le problème de Machine Learning** à résoudre : classification, prévision ou régression.

1. **Spécifier la source et le format des données d’apprentissage étiquetées** : tableaux NumPy ou cadre de données Pandas.

1. **Configurer la cible de calcul pour l’apprentissage du modèle** : [ordinateur local, calculs Azure Machine Learning, machines virtuelles à distance ou Azure Databricks](how-to-set-up-training-targets.md).  Apprenez-en davantage sur l’apprentissage automatisé [sur une ressource distante](how-to-auto-train-remote.md).

1. **Configurer les paramètres de Machine Learning automatisé** qui déterminent le nombre d’itérations sur les différents modèles, les réglages d’hyperparamètre, le prétraitement et la personnalisation avancés, ainsi que les métriques à examiner lors du choix du meilleur modèle.  Vous pouvez configurer les paramètres de l’expérience d’entraînement automatique dans [Azure Machine Learning Studio](https://ml.azure.com) ou [avec le SDK](how-to-configure-auto-train.md). 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **Lancer l’exécution de l’apprentissage.**

## <a name="how-automated-ml-works"></a>Fonctionnement du Machine Learning automatisé

Pendant l’entraînement, Azure Machine Learning crée un certain nombre de pipelines parallèles qui testent différents algorithmes et paramètres. Le service effectue des itérations dans les algorithmes de Machine Learning associés aux sélections de fonctionnalités, où chaque itération produit un modèle avec un score d’apprentissage. Plus le score est élevé, plus le modèle est considéré comme « adapté » à vos données.  Il s’arrête une fois qu’il réunit les critères de sortie définis dans l’expérience. Le diagramme suivant illustre ce processus. 

  ![Machine Learning automatisé](./media/concept-automated-ml/automl-concept-diagram2.png)


Vous pouvez également inspecter les informations d’exécution journalisées qui [contiennent les métriques collectées](how-to-understand-automated-ml.md) pendant l’exécution. L’exécution de l’apprentissage produit un objet sérialisé Python (fichier `.pkl`) contenant le modèle et le prétraitement des données.

Bien que la création de modèles soit automatisée, vous pouvez également [découvrir l’importance ou la pertinence des fonctionnalités](how-to-configure-auto-train.md#explain) pour les modèles générés.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Prétraitement

Dans chaque expérience de Machine Learning automatisé, vos données sont prétraitées à l’aide des méthodes par défaut et éventuellement via un prétraitement avancé.

> [!NOTE]
> Les étapes de prétraitement du Machine Learning automatisé (normalisation des fonctionnalités, gestion des données manquantes, conversion de texte en valeurs numériques, etc.) font partie du modèle sous-jacent. Lorsque vous utilisez le modèle pour des prédictions, les étapes de prétraitement qui sont appliquées pendant l’entraînement sont appliquées automatiquement à vos données d’entrée.

### <a name="automatic-preprocessing-standard"></a>Prétraitement automatique (standard)

Dans chaque expérience de Machine Learning automatisé, vos données sont automatiquement mises à l’échelle et normalisées pour faciliter la bonne exécution des algorithmes.  Pendant l’apprentissage du modèle, l’une des techniques suivantes de mise à l’échelle ou de normalisation est appliquée à chaque modèle.

|Mise à l’échelle&nbsp;et&&nbsp;normalisation| Description |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Normaliser les fonctionnalités en supprimant la moyenne et en mettant à l’échelle de l’écart d’unité.  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformer les fonctionnalités en les mettant à l’échelle des valeurs minimale et maximale de cette colonne.  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Mettre à l’échelle chaque fonctionnalité en fonction de sa valeur absolue maximale. |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Ce processus de mise à l’échelle opère par plage de quantiles. |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Réduction de dimensionnalité linéaire à l’aide d’une décomposition de valeur singulière des données afin de les projeter vers un espace dimensionnel inférieur. |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Ce transformateur effectue une réduction de dimensionnalité linéaire au moyen d’une décomposition de valeur singulière tronquée. Contrairement à PCA, cet estimateur ne centre pas les données avant de calculer la décomposition de valeur singulière, ce qui signifie qu’il peut travailler efficacement avec des matrices scipy.sparse. |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Chaque exemple (autrement dit, chaque ligne de la matrice de données) avec au moins un composant différent de zéro est remis à l’échelle indépendamment des autres exemples afin que sa norme (l1 ou l2) soit égale à un. |

### <a name="advanced-preprocessing-optional-featurization"></a>Prétraitement avancé : personnalisation facultative

Un prétraitement avancé et une personnalisation supplémentaires sont également disponibles, tels que des garde-fous des données, un codage et des transformations. [En savoir plus sur la personnalisation incluse](how-to-use-automated-ml-for-ml-models.md#featurization). Activez ce paramètre avec :

+ Azure Machine Learning Studio : Activez **Caractérisation automatique** dans la section **Afficher des configurations supplémentaires** [en suivant ces étapes](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

+ Kit de développement logiciel (SDK) Python : en spécifiant `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` pour la [`AutoMLConfig`classe](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

## <a name="classification--regression"></a>Classification et régression

La classification et la régression sont les types les plus courants de tâches d’apprentissage automatique. Tous deux sont des types d’apprentissage supervisé dans lesquels des modèles apprennent à utiliser des données d’apprentissage, et appliquent ces apprentissages à de nouvelles données. Azure Machine Learning offre des caractérisations spécifiquement pour ces tâches, telles que des caractériseurs de réseau neuronal profond pour la classification. Apprenez-en davantage sur les [optons de caractérisation](how-to-use-automated-ml-for-ml-models.md#featurization). 

L’objectif principal des modèles de classification est de prédire les catégories dans lesquelles les nouvelles données seront classées, sur la base des apprentissages de leurs données d’apprentissage. Les exemples de classification courants incluent la détection des fraudes, la reconnaissance de l’écriture manuscrite et la détection d’objets.  Apprenez-en davantage et découvrez un exemple de [classification avec Machine Learning automatisé](tutorial-train-models-with-aml.md).

À la différence d’une classification dans laquelle les valeurs de sortie prédites sont catégoriques, les modèles de régression prédisent des valeurs de sortie numériques en fonction de prédictions indépendantes. Dans une régression, l’objectif est d’aider à établir la relation entre ces variables de prédiction indépendantes en estimant l’impact d’une variable sur les autres. Par exemple, le coût de l’automobile basé sur des caractéristiques telles que la consommation de carburant, la cote de sécurité, etc. Apprenez-en davantage et découvrez un exemple de [Régression avec Machine Learning automatisé](tutorial-auto-train-models.md).

## <a name="time-series-forecasting"></a>Prévision de série chronologique

L’établissement de prévisions fait partie intégrante de toute entreprise, qu’il s’agisse du chiffre d’affaires, de l’inventaire, des ventes ou de la demande des clients. Vous pouvez utiliser le Machine Learning automatisé pour combiner des techniques et approches, et obtenir une prévision de série chronologique recommandée de haute qualité.

Une expérience de série chronologique automatisée est traitée comme un problème de régression multivariable. Les valeurs de série chronologique passées « pivotent » afin de devenir des dimensions supplémentaires pour le régresseur, avec d’autres prédicteurs. Contrairement aux méthodes de séries chronologiques classique, cette méthode présente l’avantage d’incorporer naturellement plusieurs variables contextuelles et leurs relations entre elles pendant l’apprentissage. Le Machine Learning automatisé effectue l’apprentissage d’un modèle unique, mais souvent ramifié en interne, pour tous les éléments du jeu de données et les horizons de prédiction. Plus de données sont ainsi disponibles pour estimer les paramètres du modèle et la généralisation en séries invisibles devient possible.

Apprenez-en davantage et découvrez un exemple de [Machine Learning automatisé pour la prévision de série chronologique](how-to-auto-train-forecast.md). Ou consultez le [notebook sur la demande d’énergie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) pour obtenir des exemples de code détaillés de la configuration de prévision avancée, à savoir :

* détection et personnalisation de congé
* série chronologique et apprenants DNN (Auto-ARIMA, Prophet, ForecastTCN)
* prise en charge de plusieurs modèles via le regroupement
* validation croisée d’origine
* décalages configurables
* caractéristiques des agrégations des fenêtres dynamiques

## <a name="ensemble-models"></a><a name="ensemble"></a> Modèles ensemblistes

Le Machine Learning automatisé prend en charge les modèles ensemblistes, qui sont activés par défaut. L’apprentissage ensembliste améliore les résultats de Machine Learning et les performances prédictives en combinant plusieurs modèles. Les itérations d’ensembles apparaissent comme les dernières itérations de votre exécution. Le Machine Learning automatisé utilise des méthodes ensemblistes de vote et d’empilement pour combiner les modèles :

* **Vote** : prédictions basées sur la moyenne pondérée des probabilités de classe prédites (pour les tâches de classification) ou des cibles de régression prédites (pour les tâches de régression).
* **Empilement** : l’empilement combine des modèles hétérogènes et entraîne un métamodèle basé sur la sortie de différents modèles. Actuellement, les métamodèles par défaut sont LogisticRegression pour les tâches de classification, et ElasticNet pour les tâches de régression et de prévision.

L’[algorithme de sélection d’ensemble Caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf), avec initialisation des ensembles triés, est utilisé pour déterminer les modèles qui doivent être utilisés au sein de l’ensemble. Pour résumer, cet algorithme initialise l’ensemble avec un maximum de cinq modèles ayant obtenu les meilleurs scores, puis vérifie que ces scores se situent dans une marge de plus ou moins 5 % par rapport au meilleur score, afin d’éviter un ensemble de niveau médiocre. Ensuite, pour chaque itération d’ensemble, un nouveau modèle est ajouté à l’ensemble existant et le score est calculé. Si un nouveau modèle a amélioré le score existant de l’ensemble, l’ensemble est mis à jour pour inclure le nouveau modèle.

Pour savoir comment modifier les paramètres par défaut de l’ensemble au niveau du Machine Learning automatisé, consultez [cette procédure](how-to-configure-auto-train.md#ensemble).

## <a name="use-with-onnx-in-c-apps"></a>Utilisation avec ONNX dans des applications C#

Avec Azure Machine Learning, vous pouvez utiliser le Machine Learning automatisé pour générer un modèle Python et le convertir au format ONNX. Le runtime ONNX prenant en charge C#, vous pouvez utiliser le modèle généré automatiquement dans votre applications C# sans avoir besoin de recodage ou des latences réseau que les points de terminaison REST introduisent. Essayez un exemple de ce flux [dans ce Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb).

## <a name="automated-ml-in-azure-machine-learning"></a>Machine learning automatisé dans Azure Machine Learning

Azure Machine Learning offre deux expériences pour utiliser le machine learning automatisé

* Pour les clients expérimentés en programmation, [SDK Python Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 

* Pour les clients avec une expérience limitée ou inexistante en programmation, Azure Machine Learning Studio sur [https://ml.azure.com](https://ml.azure.com/)  

Les éléments suivants résument les fonctionnalités générales de machine learning automatisé prises en charge dans chaque expérience.

<a name="parity"></a>

### <a name="experiment-settings"></a>Paramètres de l’expérience 

Les paramètres suivants vous permettent de configurer votre expérience de machine learning automatisé. 

| | Kit de développement logiciel (SDK) Python| Studio
----|:----:|:----:
Division des données en jeux d’entraînement/de validation| ✓|✓
Prise en charge des tâches Machine Learning : classification, régression et prévision| ✓| ✓
Optimisation basée sur une métrique principale| ✓| ✓
Prise en charge du calcul AML en tant que cible de calcul | ✓|✓
Configuration de l’horizon de prévision, des décalages de cibles et de la fenêtre dynamique|✓|✓
Définition des critères de sortie |✓|✓ 
Définition des itérations simultanées| ✓|✓
Supprimer des colonnes| ✓|✓
Blocage des algorithmes|✓|✓
Validation croisée |✓|✓
Prise en charge de l’entraînement sur les clusters Azure Databricks| ✓|
Affichage des noms de caractéristiques traités|✓|
Résumé de la caractérisation| ✓|
Caractérisation des congés|✓|
Niveau de verbosité pour les fichiers journaux| ✓|

### <a name="model-settings"></a>Paramètres du modèle

Ces paramètres peuvent être appliqués au meilleur modèle à la suite de votre expérience de machine learning automatisé.

||Kit de développement logiciel (SDK) Python|Studio
----|:----:|:----:
Inscription du meilleur modèle| ✓|✓
Déploiement du meilleur modèle| ✓| ✓
Explicabilité du meilleur modèle| ✓|✓
Activation des modèles de l’ensemble de vote et de l’ensemble d’empilement| ✓|✓
Affichage du meilleur modèle selon une métrique non principale|✓|
Activation/désactivation de la compatibilité du modèle ONNX|✓|
Tester le modèle | ✓| |

### <a name="run-control-settings"></a>Paramètres de contrôle d’exécution

Ces paramètres vous permettent d’examiner et de contrôler les exécutions de votre expérience et ses exécutions enfants. 

||Kit de développement logiciel (SDK) Python| Studio
----|:----:|:----:
Exécuter le tableau récapitulatif| ✓|✓
Annuler l’exécution| ✓|✓
Annuler l’exécution d’enfant| ✓| ✓
Obtenir des garde-fous| ✓|✓
Suspendre l’exécution| ✓| 
Reprendre l’exécution| ✓| 

## <a name="next-steps"></a>Étapes suivantes

Consultez les exemples et découvrez comment générer des modèles à l’aide du Machine Learning automatisé :

+ Suivez le [Didacticiel : Entraîner automatiquement un modèle de régression avec Azure Machine Learning](tutorial-auto-train-models.md)

+ Configurez les paramètres pour l’expérience d’apprentissage automatique :
  + Dans Azure Machine Learning Studio, [suivez ces étapes](how-to-use-automated-ml-for-ml-models.md).
  + Avec le Kit de développement logiciel (SDK) Python, [procédez comme suit](how-to-configure-auto-train.md).

+ Découvrez comment effectuer l’apprentissage automatique à l’aide de données de série chronologique [en procédant comme suit](how-to-auto-train-forecast.md).

+ Essayez des [exemples de Jupyter Notebook pour le Machine Learning automatisé](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/).

* Le machine learning automatisé est également disponible dans d’autres solutions Microsoft comme [ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated) et [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
