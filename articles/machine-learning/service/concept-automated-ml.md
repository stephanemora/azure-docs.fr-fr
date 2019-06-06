---
title: Sélection et optimisation de l’algorithme de machine learning automatisé
titleSuffix: Azure Machine Learning service
description: Découvrez comment le service Azure Machine Learning peut choisir automatiquement un algorithme pour vous et générer un modèle à partir de celui-ci pour vous permettre de gagner du temps en utilisant les paramètres et les critères que vous fournissez de façon à sélectionner le meilleur algorithme pour votre modèle.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 05/21/2019
ms.custom: seodec18
ms.openlocfilehash: 93eb0fba91ce5064d04a340e8b3e5b984ee73081
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515565"
---
# <a name="what-is-automated-machine-learning"></a>Qu’est-ce que le machine learning automatisé ?

Apprentissage automatique, également appelés ML automatisé, est le processus d’automatisation des tâches de développement d’un modèle d’apprentissage beaucoup de temps et itératifs. Il permet aux chercheurs de données, les analystes et les développeurs à générer des modèles ML à grande échelle, l’efficacité et la productivité tout en maintenant la qualité du modèle.

Développement d’un modèle d’apprentissage traditionnel consomme beaucoup de ressources, nécessitant une connaissance du domaine significatif et l’heure pour produire et comparer des dizaines de modèles. Appliquer ML automatisée lorsque vous souhaitez Azure Machine Learning pour former et optimiser un modèle à l’aide de la métrique de cible que vous spécifiez. Le service effectue ensuite une itération via les algorithmes ML associés aux sélections de fonctionnalité, où chaque itération produit un modèle avec un score de formation. Plus le score, mieux c’est le modèle est censé « ajustement » vos données.

Avec l’apprentissage automatique, vous allez ainsi accélérer le temps que nécessaire pour obtenir des modèles ML de prêt pour la production avec l’efficacité et une grande facilité.

## <a name="when-to-use-automated-ml"></a>Quand utiliser automatisée ML

ML automatisé démocratise le processus de développement de modèle d’apprentissage et permet à ses utilisateurs, quel que soit leur expertise de science des données, pour identifier un pipeline d’apprentissage automatique de bout en bout d’éventuels problèmes.

Scientifiques des données, les analystes et les développeurs pour différents secteurs peuvent utiliser ML automatisé pour :

+ Implémenter des solutions machine learning sans connaissance en programmation complète
+ Gagnez du temps et ressources
+ Tirer parti des meilleures pratiques de science des données
+ Fournir la résolution des problèmes d’agile

## <a name="how-automated-ml-works"></a>Comment automatisés works ML

À l’aide de **service Azure Machine Learning**, vous pouvez concevoir et exécuter vos expériences d’apprentissage automatisés ML avec ces étapes :

1. **Identifier le problème de ML** à résoudre : classification, de prévision ou de régression

1. **Spécifier la source et le format des données d’apprentissage étiqueté**: Tableaux Numpy ou données Pandas

1. **Configurer la cible de calcul pour l’apprentissage du modèle**, tel que votre [ordinateur local, Azure Machine Learning calcule, machines virtuelles à distance ou Azure Databricks](how-to-set-up-training-targets.md).  En savoir plus sur la formation automatisée [sur une ressource distante](how-to-auto-train-remote.md).

1. **Configurer les paramètres d’apprentissage automatique** qui déterminent le nombre d’itérations sur les différents modèles, paramètres d’hyperparamètres, des fonctions avancées de prétraitement/caractérisation et les mesures à prendre en compte pour déterminer le meilleur modèle.  Vous pouvez configurer les paramètres de l’expérience d’apprentissage automatique [dans le portail Azure](how-to-create-portal-experiments.md) ou [avec le SDK](how-to-configure-auto-train.md).

1. **Envoyer la formation à exécuter.**

  ![Apprentissage automatique](./media/how-to-automated-ml/automl-concept-diagram2.png)

Pendant l’apprentissage, le service Azure Machine Learning crée plusieurs dans des pipelines parallèles qui tentent de paramètres et des algorithmes différents. Il s’arrête une fois qu’il atteint les critères de sortie définis dans l’expérience.

Vous pouvez également inspecter les informations d’exécution connectées, qui contient des mesures collectées pendant l’exécution. L’exécution de la formation génère un objet sérialisé de Python (`.pkl` fichier) qui contient le modèle et le prétraitement des données.

Bien que la création de modèles est automatisée, vous pouvez également [Apprenez comment importantes ou pertinentes de fonctionnalités sont](how-to-configure-auto-train.md#explain) pour les modèles générés.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Prétraitement

Dans chaque automatisé expérience d’apprentissage, vos données sont prétraitées à l’aide de méthodes par défaut et éventuellement via avancées de prétraitement.

### <a name="automatic-preprocessing-standard"></a>Automatique de prétraitement (standard)

Dans chaque automatisé expérience d’apprentissage, vos données sont automatiquement mis à l’échelle ou normalisées pour aider les algorithmes fonctionnent bien.  Pendant l’apprentissage du modèle, une des techniques de mise à l’échelle ou normalisation suivantes s’appliqueront à chaque modèle.

|Mise à l’échelle&nbsp;&&nbsp;normalisation| Description |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Normaliser les fonctionnalités en supprimant la moyenne et de mise à l’échelle variance d’unité  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transforme les fonctionnalités en mise à l’échelle de chaque fonctionnalité par de cette colonne au minimum et maximum  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Mettre à l’échelle de chaque fonctionnalité par sa valeur absolue maximale |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Cette fonctionnalité Scaler par leur plage de quantiles |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Réduction de dimensionnalité linéaire à l’aide de la décomposition de valeur singulière des données à projeter vers un espace dimensionnel inférieur |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Ce transformateur effectue la réduction de dimensionnalité linéaire au moyen de décomposition tronquée valeur singulière (SVD). Contrairement aux PCA, cet estimateur ne centre pas les données avant de calculer la décomposition de valeur singulière. Cela signifie qu’il peut travailler efficacement avec des matrices de scipy.sparse |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Chaque exemple (autrement dit, chaque ligne de la matrice de données) au moins un composant non nulle est ré-mise à l’échelle indépendamment des autres exemples afin que sa norme (l1 ou l2) est égal à un |

### <a name="advanced-preprocessing-optional-featurization"></a>Advanced prétraitement : caractérisation facultative

Prétraitement avancées supplémentaires et la caractérisation sont également disponibles, tels que l’absence d’imputation des valeurs, de codage et de transformations. [En savoir plus sur la caractérisation est incluse](how-to-create-portal-experiments.md#preprocess). Activez ce paramètre avec :

+ Portail Azure : En sélectionnant le **prétraitement** case à cocher dans la **paramètres avancés** [avec ces étapes](how-to-create-portal-experiments.md).

+ SDK Python : Spécification `"preprocess": True` pour le [ `AutoMLConfig` classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

## <a name="ensemble-models"></a>Modèles d’ensemble

Vous pouvez former des modèles d’ensemble à l’aide d’apprentissage automatique avec le [algorithme de sélection Caruana ensemble avec l’initialisation Ensemble triée](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). Apprentissage d’ensemble améliore les résultats de la machine learning et les performances de prédiction en combinant plusieurs modèles au lieu d’utiliser des modèles uniques. L’itération de l’ensemble s’affiche en tant que la dernière itération de votre série de tests.

## <a name="training-metric-output"></a>Résultat de la mesure de formation

Il existe plusieurs manières d’afficher les mesures de précision de formation pour chaque itération d’exécution.

* Utiliser le widget de Jupyter.
* Utilisez le `get_metrics()` fonction sur n’importe quel `Run` objet.
* Afficher les métriques dans le portail Azure dans votre expérience.

### <a name="classification-metrics"></a>Métriques de classification

Les métriques suivantes sont enregistrées dans chaque itération d’exécution pour une tâche de classification.

|Métrique|Description|Calcul|Paramètres supplémentaires
--|--|--|--|
AUC_Macro| « AUC » est « Area under the Receiver Operating Characteristic Curve » (la zone sous la courbe caractéristique de fonctionnement du récepteur). « Macro » est la moyenne arithmétique de l’AUC pour chaque classe.  | [Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| « AUC » est « Area under the Receiver Operating Characteristic Curve » (la zone sous la courbe caractéristique de fonctionnement du récepteur). « Micro » est calculé globalement en combinant les vrais positifs et les faux positifs de chaque classe| [Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | « AUC » est « Area under the Receiver Operating Characteristic Curve » (la zone sous la courbe caractéristique de fonctionnement du récepteur). « Weighted » est la moyenne arithmétique du score pour chaque classe, pondérée par le nombre d’instances « true » dans chaque classe.| [Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|La précision est le pourcentage d’étiquettes prédites qui correspondent exactement aux étiquettes réelles. |[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Aucun|
average_precision_score_macro|La précision moyenne résume la courbe précision-rappel comme moyenne pondérée des précisions atteintes à chaque seuil, avec l’augmentation du rappel du seuil précédent utilisé comme pondération. Macro est la moyenne arithmétique du score de précision moyen de chaque classe|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|La précision moyenne résume la courbe précision-rappel comme moyenne pondérée des précisions atteintes à chaque seuil, avec l’augmentation du rappel du seuil précédent utilisé comme pondération. « Micro » est calculé globalement en combinant les vrais positifs et les faux positifs de chaque limite|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|La précision moyenne résume la courbe précision-rappel comme moyenne pondérée des précisions atteintes à chaque seuil, avec l’augmentation du rappel du seuil précédent utilisé comme pondération. « Weighted » est la moyenne arithmétique du score de précision moyen pour chaque classe, pondérée par le nombre d’instances « true » dans chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|La précision équilibrée est la moyenne arithmétique du rappel pour chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|Le score F1 est la moyenne harmonique de la précision et du rappel. « Macro » est la moyenne arithmétique du score F1 pour chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|Le score F1 est la moyenne harmonique de la précision et du rappel. « Micro » est calculé globalement en comptant le total des vrais positifs, des faux négatifs et des faux positifs|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|Le score F1 est la moyenne harmonique de la précision et du rappel. Moyenne pondérée par fréquence de classe du score F1 pour chaque classe|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|Il s’agit de la fonction de perte utilisée dans la régression logistique (multinomiale) et les extensions de celle-ci, comme les réseaux neuronaux, définie comme la probabilité logarithmique négative des étiquettes réelles, étant données les prédictions d’un classifieur probabiliste. Pour un échantillon unique avec l’étiquette vraie yt dans {0,1} et la probabilité estimée yp que yt = 1, la perte logarithmique est - -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp))|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Aucun|
norm_macro_recall|Le rappel macro normalisé est tel que la performance aléatoire ait un score de 0 et la performance parfaite ait un score de 1. Ceci est réalisé par norm_macro_recall := (recall_score_macro - R)/(1 - R), où R est la valeur attendue de recall_score_macro pour des prédictions aléatoires (c’est-à-dire R=0,5 pour la classification binaire) et R=(1/C) pour les problèmes de classification de classe C)|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "macro", puis norm_macro_recall := (recall_score_macro - R)/(1 - R), où R est la valeur attendue de recall_score_macro pour des prédictions aléatoires (c’est-à-dire R=0,5 pour la classification binaire) et R=(1/C) pour les problèmes de classification de classe C)|
precision_score_macro|La précision est le pourcentage d’éléments étiquetés comme classe certaine et qui sont réellement dans cette classe. « Macro » est la moyenne arithmétique de la précision pour chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|La précision est le pourcentage d’éléments étiquetés comme classe certaine et qui sont réellement dans cette classe. « Micro » est calculé globalement en comptant le total des vrais positifs et des faux positifs|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|La précision est le pourcentage d’éléments étiquetés comme classe certaine et qui sont réellement dans cette classe. « Weighted » est la moyenne arithmétique de la précision pour chaque classe, pondérée par le nombre d’instances « true » dans chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Le rappel est le pourcentage d’éléments qui sont réellement dans une classe certaine et qui sont correctement étiquetés. « Macro » est la moyenne arithmétique du rappel pour chaque classe|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Le rappel est le pourcentage d’éléments qui sont réellement dans une classe certaine et qui sont correctement étiquetés. « Micro » est calculé globalement en comptant le total des vrais positifs et des faux négatifs|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Le rappel est le pourcentage d’éléments qui sont réellement dans une classe certaine et qui sont correctement étiquetés. « Weighted » est la moyenne arithmétique du rappel pour chaque classe, pondérée par le nombre d’instances « true » dans chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|La précision pondérée est la précision où le poids donné à chaque exemple est égal à la proportion d’instances « true » dans la classe « true » de cet exemple|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight est un vecteur égal à la proportion de cette classe pour chaque élément dans la cible|

### <a name="regression-and-forecasting-metrics"></a>Métriques de régression et de prévision

Les métriques suivantes sont enregistrées dans chaque itération d’exécution pour une tâche de prévision ou de régression.

|Métrique|Description|Calcul|Paramètres supplémentaires
--|--|--|--|
explained_variance|La variance expliquée est la proportion selon laquelle un modèle mathématique compte pour la variation d’un jeu de données particulier. C’est le pourcentage de diminution de la variance des données d’origine par rapport à la variance des erreurs. Quand la moyenne des erreurs est 0, elle est égale à la variance expliquée.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Aucun|
r2_score|R2 est le coefficient de détermination ou le pourcentage de réduction dans les erreurs quadratiques comparé à un modèle de référence qui génère la moyenne. Quand la moyenne des erreurs est 0, elle est égale à la variance expliquée.|[Calcul](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Aucun|
spearman_correlation|La corrélation de Spearman est une mesure non paramétrique de la monotonie de la relation entre deux jeux de données. Contrairement à la corrélation de Pearson, la corrélation de Spearman ne suppose pas que les deux jeux de données sont normalement distribués. Comme d’autres coefficients de corrélation, celle-ci varie entre -1 et +1, 0 impliquant l’absence de corrélation. Les corrélations de -1 ou +1 impliquent une relation monotone exacte. Les corrélations positives impliquent que quand x augmente, y augmente également. Les corrélations négatives impliquent que quand x augmente, y diminue.|[Calcul](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Aucun|
mean_absolute_error|L’erreur d’absolue moyenne est la valeur attendue de la valeur absolue de la différence entre la cible et la prédiction|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Aucun|
normalized_mean_absolute_error|L’erreur d’absolue moyenne normalisée est l’erreur absolue moyenne divisée par la plage des données|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Diviser par la plage de données|
median_absolute_error|L’erreur absolue médiane est la médiane de toutes les différences absolues entre la cible et la prédiction. Cette perte est robuste pour les valeurs hors norme.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Aucun|
normalized_median_absolute_error|L’erreur d’absolue médiane normalisée est l’erreur absolue médiane divisée par la plage des données|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Diviser par la plage de données|
root_mean_squared_error|L’erreur quadratique moyenne racine est la racine carrée de la différence quadratique attendue entre la cible et la prédiction|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Aucun|
normalized_root_mean_squared_error|L’erreur quadratique moyenne racine normalisée est l’erreur quadratique moyenne racine divisée par la plage des données|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Diviser par la plage de données|
root_mean_squared_log_error|L’erreur logarithmique quadratique moyenne racine est la racine carrée de l’erreur logarithmique quadratique attendue|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Aucun|
normalized_root_mean_squared_log_error|L’erreur logarithmique quadratique moyenne racine normalisée est l’erreur logarithmique quadratique moyenne racine divisée par la plage des données|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Diviser par la plage de données|


## <a name="use-with-onnx-in-c-apps"></a>Utilisation avec ONNX dans C# applications

Avec Azure Machine Learning, vous pouvez utiliser ML automatisé pour générer un modèle de Python et qu’il soit converti au format ONNX. Prend en charge par le runtime ONNX C#, de sorte que vous pouvez utiliser le modèle généré automatiquement dans votre C# applications sans avoir recours aux recoder ou l’un des latences réseau présentant les points de terminaison REST. Essayez un exemple de ce flux [ce Notebook Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>ML automatisée au sein de Microsoft

ML automatisé est également disponible dans d’autres solutions de Microsoft telles que :

|Intégrations|Description|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Sélection d’un modèle automatique et de formation dans les applications .NET à l’aide de Visual Studio et Visual Studio Code avec ML.NET automatisée ML (version préliminaire).|
|[HDIsnight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Faire évoluer vos travaux de formation automatisée ML sur Spark dans les clusters HDInsight en parallèle.|
|[PowerBI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Appeler des modèles machine learning directement dans Power BI (version préliminaire).|

## <a name="next-steps"></a>Étapes suivantes

Consultez les exemples et découvrez comment générer des modèles à l’aide du machine learning automatisé :

+ Suivez le [didacticiel : Entraîner automatiquement un modèle de classification avec le Machine Learning Azure automatisé](tutorial-auto-train-models.md).

+ Configurer les paramètres de l’expérience d’apprentissage automatique :
  + Dans l’interface du portail Azure, [Utilisez ces étapes](how-to-create-portal-experiments.md).
  + Avec le SDK Python, [Utilisez ces étapes](how-to-configure-auto-train.md).

+ Découvrez comment auto former à l’aide des données de série chronologique, [Utilisez ces étapes](how-to-auto-train-forecast.md).

+ Essayer [exemples de bloc-notes Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
