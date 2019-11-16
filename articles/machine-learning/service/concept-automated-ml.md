---
title: Qu’est-ce que le Machine Learning (ML) automatisé / automl
titleSuffix: Azure Machine Learning
description: Découvrez comment Azure Machine Learning peut choisir automatiquement un algorithme pour vous et générer un modèle à partir de celui-ci pour vous permettre de gagner du temps en utilisant les paramètres et les critères que vous fournissez de façon à sélectionner le meilleur algorithme pour votre modèle.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 11/04/2019
ms.openlocfilehash: f8a83fccefe3310fe1a582ef44d72cfbef7e9469
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133070"
---
# <a name="what-is-automated-machine-learning"></a>Qu’est-ce que le machine learning automatisé ?

Le machine learning automatisé, également appelé ML automatisé, est le processus d’automatisation des tâches fastidieuses et itératives de développement de modèle Machine Learning. Il permet aux chercheurs de données, analystes et développeurs de créer des modèles ML à grande échelle, efficaces et productifs, tout en maintenant la qualité du modèle. L’apprentissage automatique automatisé se base sur une innovation de notre [division Microsoft Research](https://arxiv.org/abs/1705.05355).

Le développement de modèle Machine Learning traditionnel consomme beaucoup de ressources, nécessitant une connaissance significative du domaine et du temps pour produire et comparer des dizaines de modèles. Appliquez le Machine Learning automatisé lorsque vous souhaitez qu’Azure Machine Learning effectue l’apprentissage d’un modèle et le règle à votre place à l’aide de la métrique cible que vous spécifiez. Le service effectue ensuite des itérations dans les algorithmes de Machine Learning associés aux sélections de fonctionnalités, où chaque itération produit un modèle avec un score d’apprentissage. Plus le score est élevé, plus le modèle est considéré comme « adapté » à vos données.

Le Machine Learning automatisé permet de réduire le temps nécessaire pour obtenir des modèles ML prêts pour la production avec une facilité et une efficacité extraordinaires.

## <a name="when-to-use-automated-ml"></a>Quand utiliser un Machine Learning automatisé

Le ML automatisé démocratise le processus de développement de modèle Machine Learning et permet à ses utilisateurs, quel que soit leur expertise en matière de science des données, d’identifier un pipeline de Machine Learning de bout en bout pour tout problème.

Les scientifiques des données, analystes et développeurs de différents secteurs peuvent utiliser le Machine Learning automatisé pour ce qui suit :

+ Implémenter des solutions de Machine Learning sans disposer d’une connaissance approfondie de la programmation.
+ Économiser du temps et des ressources.
+ Tirer parti des meilleures pratiques en matière de science des données.
+ Fournir une résolution de problème agile.

Le tableau suivant liste les cas d’usage courants du ML automatisé. 

classification ;| régression ; | Prévision de séries chronologiques
---|---|---
[Détection des fraudes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[Prédiction des performances du processeur](https://github.com/Azure/MachineLearningNotebooks/blob/37541b10714f9337dbbae721bea494272dc7d151/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb) |[Prédiction de la demande](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
[Prédiction marketing](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[Prédiction de la durabilité des matériaux](https://github.com/Azure/MachineLearningNotebooks/blob/37541b10714f9337dbbae721bea494272dc7d151/how-to-use-azureml/automated-machine-learning/regression-concrete-strength/auto-ml-regression-concrete-strength.ipynb)|[Prévisions de ventes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)

## <a name="how-automated-ml-works"></a>Fonctionnement du Machine Learning automatisé

**Azure Machine Learning** vous permet de concevoir et d’exécuter vos expériences d’entraînement de Machine Learning automatisé en effectuant les étapes suivantes :

1. **Identifier le problème de Machine Learning** à résoudre : classification, prévision ou régression.

1. **Spécifier la source et le format des données d’apprentissage étiquetées** : tableaux NumPy ou cadre de données Pandas.

1. **Configurer la cible de calcul pour l’apprentissage du modèle** : [ordinateur local, calculs Azure Machine Learning, machines virtuelles à distance ou Azure Databricks](how-to-set-up-training-targets.md).  Apprenez-en davantage sur l’apprentissage automatisé [sur une ressource distante](how-to-auto-train-remote.md).

1. **Configurer les paramètres de Machine Learning automatisé** qui déterminent le nombre d’itérations sur les différents modèles, les réglages d’hyperparamètre, le prétraitement et la personnalisation avancés, ainsi que les métriques à examiner lors du choix du meilleur modèle.  Vous pouvez configurer les paramètres de l’expérience d’entraînement automatique dans [Azure Machine Learning Studio](https://ml.azure.com) ou [avec le SDK](how-to-configure-auto-train.md). 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **Lancer l’exécution de l’apprentissage.**

  ![Machine Learning automatisé](./media/how-to-automated-ml/automl-concept-diagram2.png)

Pendant l’entraînement, Azure Machine Learning crée un certain nombre de pipelines parallèles qui testent différents algorithmes et paramètres. Il s’arrête une fois qu’il réunit les critères de sortie définis dans l’expérience.

Vous pouvez également inspecter les informations d’exécution journalisées qui [contiennent les métriques collectées](how-to-understand-automated-ml.md) pendant l’exécution. L’exécution de l’apprentissage produit un objet sérialisé Python (fichier `.pkl`) contenant le modèle et le prétraitement des données.

Bien que la création de modèles soit automatisée, vous pouvez également [découvrir l’importance ou la pertinence des fonctionnalités](how-to-configure-auto-train.md#explain) pour les modèles générés.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Prétraitement

Dans chaque expérience de Machine Learning automatisé, vos données sont prétraitées à l’aide des méthodes par défaut et éventuellement via un prétraitement avancé.

> [!NOTE]
> Les étapes de prétraitement du Machine Learning automatisé (normalisation des fonctionnalités, gestion des données manquantes, conversion de texte en valeurs numériques, etc.) font partie du modèle sous-jacent. Lorsque vous utilisez le modèle pour des prédictions, les étapes de prétraitement qui sont appliquées pendant la formation sont appliquées automatiquement à vos données d’entrée.

### <a name="automatic-preprocessing-standard"></a>Prétraitement automatique (standard)

Dans chaque expérience de Machine Learning automatisé, vos données sont automatiquement mises à l’échelle et normalisées pour faciliter la bonne exécution des algorithmes.  Pendant l’apprentissage du modèle, l’une des techniques suivantes de mise à l’échelle ou de normalisation est appliquée à chaque modèle.

|Mise à l’échelle&nbsp;et&&nbsp;normalisation| Description |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Normaliser les fonctionnalités en supprimant la moyenne et en mettant à l’échelle de l’écart d’unité.  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformer les fonctionnalités en les mettant à l’échelle des valeurs minimale et maximale de cette colonne.  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Mettre à l’échelle chaque fonctionnalité en fonction de sa valeur absolue maximale. |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Ce processus de mise à l’échelle opère par plage de quantiles. |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Réduction de dimensionnalité linéaire à l’aide d’une décomposition de valeur singulière des données afin de les projeter vers un espace dimensionnel inférieur. |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Ce transformateur effectue une réduction de dimensionnalité linéaire au moyen d’une décomposition de valeur singulière tronquée. Contrairement à PCA, cet estimateur ne centre pas les données avant de calculer la décomposition de valeur singulière. Cela signifie qu’il peut travailler efficacement avec des matrices scipy.sparse. |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Chaque exemple (autrement dit, chaque ligne de la matrice de données) avec au moins un composant différent de zéro est remis à l’échelle indépendamment des autres exemples afin que sa norme (l1 ou l2) soit égal à un. |

### <a name="advanced-preprocessing-optional-featurization"></a>Prétraitement avancé : personnalisation facultative

Un prétraitement avancé et une personnalisation supplémentaires sont également disponibles, tels qu’une imputation de valeurs manquantes, un codage et des transformations. [En savoir plus sur la personnalisation incluse](how-to-create-portal-experiments.md#preprocess). Activez ce paramètre avec :

+ Azure Machine Learning Studio : En sélectionnant l’option permettant de **voir les paramètres de caractérisation** dans la section **Exécuter la configuration** [en effectuant ces étapes](how-to-create-portal-experiments.md).

+ Kit de développement logiciel (SDK) Python : en spécifiant `"feauturization": auto' / 'off' / FeaturizationConfig` pour la [`AutoMLConfig`classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).


## <a name="time-series-forecasting"></a>Prévision de série chronologique
L’établissement de prévisions fait partie intégrante de toute entreprise, qu’il s’agisse du chiffre d’affaires, de l’inventaire, des ventes ou de la demande des clients. Vous pouvez utiliser le Machine Learning automatisé pour combiner des techniques et approches, et obtenir une prévision de série chronologique recommandée de haute qualité.

Une expérience de série chronologique automatisée est traitée comme un problème de régression multivariable. Les valeurs de série chronologique passées « pivotent » pour devenir des dimensions supplémentaires pour le régresseur, avec d’autres prédicteurs. Contrairement aux méthodes de série chronologique classiques, cette méthode présente l’avantage d’incorporer naturellement plusieurs variables contextuelles et leurs relations entre elles pendant l’apprentissage. Le Machine Learning automatisé effectue l’apprentissage d’un modèle unique, mais souvent ramifié en interne, pour tous les éléments du jeu de données et les horizons de prédiction. Plus de données sont ainsi disponibles pour estimer les paramètres du modèle et la généralisation en séries invisibles devient possible.

Apprenez-en davantage et découvrez un exemple de [Machine Learning automatisé pour la prévision de série chronologique](how-to-auto-train-forecast.md). Ou consultez le [notebook sur la demande d’énergie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) pour obtenir des exemples de code détaillés de la configuration de prévision avancée, à savoir :

* détection et personnalisation de congé
* série chronologique et apprenants DNN (Auto-ARIMA, Prophet, ForecastTCN)
* prise en charge de plusieurs modèles via le regroupement
* validation croisée d’origine
* décalages configurables
* caractéristiques des agrégations des fenêtres dynamiques

## <a name="ensemble"></a> Modèles ensemblistes

Le Machine Learning automatisé prend en charge les modèles ensemblistes, qui sont activés par défaut. L’apprentissage ensembliste améliore les résultats de Machine Learning et les performances prédictives en combinant plusieurs modèles. Les itérations d’ensembles apparaissent comme les dernières itérations de votre exécution. Le Machine Learning automatisé utilise des méthodes ensemblistes de vote et d’empilement pour combiner les modèles :

* **Vote** : prédictions basées sur la moyenne pondérée des probabilités de classe prédites (pour les tâches de classification) ou des cibles de régression prédites (pour les tâches de régression).
* **Empilement** : l’empilement combine des modèles hétérogènes et entraîne un métamodèle basé sur la sortie de différents modèles. Actuellement, les métamodèles par défaut sont LogisticRegression pour les tâches de classification, et ElasticNet pour les tâches de régression et de prévision.

L’[algorithme de sélection d’ensemble Caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf), avec initialisation des ensembles triés, est utilisé pour déterminer les modèles qui doivent être utilisés au sein de l’ensemble. Pour résumer, cet algorithme initialise l’ensemble avec un maximum de 5 modèles ayant obtenu les meilleurs scores, puis vérifie que ces scores se situent dans une marge de plus ou moins 5 % par rapport au meilleur score, afin d’éviter un ensemble de niveau médiocre. Ensuite, pour chaque itération d’ensemble, un nouveau modèle est ajouté à l’ensemble existant et le score est calculé. Si un nouveau modèle a amélioré le score existant de l’ensemble, l’ensemble est mis à jour pour inclure le nouveau modèle.

Pour savoir comment modifier les paramètres par défaut de l’ensemble au niveau du Machine Learning automatisé, consultez [cette procédure](how-to-configure-auto-train.md#ensemble).

## <a name="imbalance"></a> Données déséquilibrées

Les données déséquilibrées se trouvent généralement dans les scénarios de classification de machine learning. Il s’agit des données dont le taux d’observations dans chaque classe est disproportionné. Ce déséquilibre peut donner une idée faussement positive de la justesse d’un modèle parce que les données d’entrée présentent un biais vers une classe, ce qui influence le modèle entraîné. 

Dans l’optique de simplifier le workflow du machine learning, le ML automatisé intègre des fonctionnalités qui permettent de gérer les données déséquilibrées, à savoir : 

- Une **colonne de pondération** : le ML automatisé peut utiliser une colonne pondérée en guise d’entrée. Les lignes de données sont alors pondérées, ce qui peut accentuer ou réduire l’importance d’une classe. Consultez cet [exemple de notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/sample-weight/auto-ml-sample-weight.ipynb). 

- Les algorithmes utilisés par le ML automatisé peuvent traiter un déséquilibre jusqu’à un rapport de 20 à 1, ce qui signifie que la classe la plus commune peut avoir 20 fois plus de lignes dans les données que la classe la moins commune.

### <a name="identify-models-with-imbalanced-data"></a>Identifier les modèles présentant des données déséquilibrées

Sachant que les algorithmes de classification sont généralement évalués selon des critères de justesse, il est judicieux de vérifier la justesse d’un modèle pour déterminer s’il a été impacté par des données déséquilibrées. Sa justesse pour certaines classes s’est-elle avérée élevée ou très faible ?

Par ailleurs, les exécutions du ML automatisé génèrent automatiquement les graphiques suivants, qui peuvent vous aider à comprendre la cohérence des classifications de votre modèle et à identifier les modèles potentiellement impactés par des données déséquilibrées.

Graphique| Description
---|---
[Matrice de confusion](how-to-understand-automated-ml.md#confusion-matrix)| Évalue les étiquettes correctement classifiées par rapport aux étiquettes réelles des données. 
[Rappel de précision](how-to-understand-automated-ml.md#precision-recall-chart)| Évalue le ratio d’étiquettes correctes par rapport au ratio d’instances d’étiquettes trouvées dans les données. 
[Courbe ROC](how-to-understand-automated-ml.md#roc)| Évalue le ratio d’étiquettes correctes par rapport au ratio d’étiquettes considérées comme étant des faux positifs.

### <a name="handle-imbalanced-data"></a>Traiter les données déséquilibrées 

Les techniques suivantes sont des options supplémentaires pour traiter les données déséquilibrées en dehors du ML automatisé. 

- Rééchantillonnage destiné à niveler le déséquilibre des classes, soit en suréchantillonnant les classes les plus petites soit en sous-échantillonnant les classes les plus grandes. Ces méthodes demandent des compétences techniques pour le traitement et l’analyse.

- Utilisation d’une métrique de performances qui gère mieux les données déséquilibrées. Par exemple, le score F1 est une moyenne pondérée de la précision et du rappel. La précision mesure l’exactitude d’un classifieur -- une faible précision indique un grand nombre de faux positifs-- ; le rappel mesure l’exhaustivité d’un classifieur -- un faible rappel indique un grand nombre de faux négatifs. 

## <a name="use-with-onnx-in-c-apps"></a>Utilisation avec ONNX dans des applications C#

Avec Azure Machine Learning, vous pouvez utiliser le Machine Learning automatisé pour générer un modèle Python et le convertir au format ONNX. Le runtime ONNX prenant en charge C#, vous pouvez utiliser le modèle généré automatiquement dans votre applications C# sans avoir besoin de recodage ou des latences réseau que les points de terminaison REST introduisent. Essayez un exemple de ce flux [dans ce Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Machine Learning automatisé dans les solutions Microsoft

Le Machine Learning automatisé est également disponible dans d’autres solutions Microsoft telles que les suivantes :

|Intégrations|Description|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Sélection et apprentissage automatiques d’un modèle dans les applications .NET en utilisant Visual Studio et Visual Studio Code avec Machine Learning automatisé ML.NET (préversion).|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Augmentation de l’échelle de vos travaux d’apprentissage par Machine Learning automatisé sur Spark dans des clusters HDInsight en parallèle.|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Appel de modèles Machine Learning directement dans Power BI (préversion).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Création de modèles Machine Learning sur vos données dans des clusters de Big Data SQL Server 2019.|

## <a name="next-steps"></a>Étapes suivantes

Consultez les exemples et découvrez comment générer des modèles à l’aide du Machine Learning automatisé :

+ Suivez le [Didacticiel : Entraîner automatiquement un modèle de régression avec le Machine Learning Azure automatisé](tutorial-auto-train-models.md).

+ Configurez les paramètres pour l’expérience d’apprentissage automatique :
  + Dans Azure Machine Learning Studio, [suivez ces étapes](how-to-create-portal-experiments.md).
  + Avec le Kit de développement logiciel (SDK) Python, [procédez comme suit](how-to-configure-auto-train.md).

+ Découvrez comment effectuer l’apprentissage automatique à l’aide de données de série chronologique [en procédant comme suit](how-to-auto-train-forecast.md).

+ Essayez des [exemples de Jupyter Notebook pour le Machine Learning automatisé](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/).
