---
title: Comprendre les résultats des ML automatisés
titleSuffix: Azure Machine Learning service
description: Apprenez à visualiser et à comprendre les graphiques et les métriques pour chacune de vos exécutions de Machine Learning automatisées.
services: machine-learning
author: nilesha
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: ea85a0906ce231312c491d31a33c331480d23812
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361453"
---
# <a name="understand-automated-machine-learning-results"></a>Comprendre les résultats des Machine Learning automatisés

Dans cet article, vous apprenez à visualiser et à comprendre les graphiques et les métriques pour chacune de vos exécutions de Machine Learning automatisées. 

Pour en savoir plus :
+ [Métriques, graphiques et courbes pour les modèles de classification](#classification)
+ [Métriques et graphiques pour les modèles de régression](#regression)
+ [Interprétabilité du modèle et importance des fonctionnalités](#explain-model)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez dès aujourd'hui la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree).

* Créez une expérience Machine Learning automatisée, soit avec le kit de développement logiciel (SDK), soit dans le Portail Microsoft Azure.

    * Utilisez le Kit de développement logiciel (SDK) pour développer un [modèle de classification](how-to-auto-train-remote.md) ou un [modèle de régression](tutorial-auto-train-models.md)
    * Utilisez le [Portail Microsoft Azure](how-to-create-portal-experiments.md) pour créer un modèle de classification ou de régression en téléchargeant les données appropriées.

## <a name="view-the-run"></a>Afficher l’exécution

Après avoir exécuté une expérience de Machine Learning automatisée, vous trouverez un historique des exécutions dans votre espace de travail de service de Machine Learning. 

1. Accédez à votre espace de travail.

1. Dans le panneau gauche de l'espace de travail, sélectionnez **Expériences**.

   ![Capture d’écran du menu Expérience](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Dans la liste des expériences, sélectionnez celle que vous voulez explorer.

   ![Liste d’expériences](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)

1. Dans la table du bas, sélectionnez le **numéro d’exécution**.

   ![Exécution de l’expérience](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)

1. Dans la table des itérations, sélectionnez le **numéro d’itération** du modèle que vous souhaitez explorer.

   ![Modèle d’expérience](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)

Vous voyez aussi ces mêmes résultats lors d'une exécution avec le `RunDetails`[widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification"></a> Résultats de la classification

Les trois métriques et graphiques suivants sont disponibles pour chaque modèle de classification que vous créez à l’aide des fonctionnalités de Machine Learning automatisé d’Azure Machine Learning

+ [Métriques](#classification-metrics)
+ [Matrice de confusion](#confusion-matrix)
+ [Graphique de rappel de précision](#precision-recall-chart)
+ [ROC (Receiver operating characteristic)](#roc)
+ [Courbe d’élévation](#lift-curve)
+ [Courbe de gains](#gains-curve)
+ [Tracé d’étalonnage](#calibration-plot)

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

### <a name="confusion-matrix"></a>Matrice de confusion

Une matrice de confusion décrit les performances d’un modèle de classification. Chaque ligne affiche les instances de la classe true, et chaque colonne représente les instances de la classe prévue. La matrice de confusion montre les étiquettes bien et mal classées pour un modèle donné.

Pour les problèmes de classification, Azure Machine Learning fournit automatiquement une matrice de confusion associée à chaque modèle généré. Pour chaque matrice de confusion, le ML automatisé affiche les étiquettes bien classées en vert, et les étiquettes mal classées en rouge. La taille du cercle représente le nombre d’échantillons dans cet emplacement. En outre, la fréquence de chaque étiquette prévue et chaque étiquette true est fournie dans les graphiques à barres adjacents. 

Exemple 1 : Modèle de classification avec précision médiocre ![Modèle de classification avec précision médiocre](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

Exemple 2 : Modèle de classification avec précision élevée (idéal) ![Modèle de classification avec précision élevée](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>Graphique de rappel de précision

Avec ce graphique, vous pouvez comparer les courbes de rappel de précision pour chaque modèle afin de déterminer quel modèle présente une relation acceptable entre précision et rappel pour votre problème d’entreprise spécifique. Ce graphique montre le rappel de précision de macro-moyenne, le rappel de précision de micro-moyenne et le rappel de précision associé à toutes les classes d’un modèle.

Le terme Précision représente la capacité d’un classifieur à étiqueter correctement toutes les instances. Le rappel représente la capacité d’un classifieur à rechercher toutes les instances d’une étiquette spécifique. La courbe de rappel de précision montre la relation entre ces deux concepts. Dans l’idéal, le modèle aurait une précision de 100 % et une exactitude de 100 %.

Exemple 1 : Modèle de classification avec une précision faible et un rappel faible ![Modèle de classification avec une précision faible et un rappel faible](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

Exemple 2 : Modèle de classification avec une précision d’environ 100 % et un rappel d’environ 100 % (idéal)![Modèle de classification avec une précision élevée et un rappel élevé](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>ROC

Le ROC (Receiver Operating Characteristic) est un tracé d’étiquettes bien classées et mal classées pour un modèle spécifique. La courbe ROC peut être moins informative lors de la formation de modèles sur des jeux de données présentant un biais élevé, car elle n’affiche pas les étiquettes de type faux positif.

Exemple 1 : Modèle de classification avec des étiquettes true faibles et des étiquettes false élevées![Modèle de classification avec des étiquettes true faibles et des étiquettes false élevées](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

Exemple 2 : Modèle de classification avec des étiquettes true élevées et des étiquettes false faibles![Modèle de classification avec des étiquettes true élevées et des étiquettes false faibles](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>Courbe d’élévation

Vous pouvez comparer l’élévation du modèle généré automatiquement avec Azure Machine Learning par rapport à la ligne de base afin d’afficher le gain de valeur de ce modèle spécifique.

Les graphiques de courbes d’élévation permettent d’évaluer les performances d’un modèle de classification. Ils montrent tous les atouts d’un modèle par rapport à une utilisation sans ce modèle. 

Exemple 1 : Les performances du modèle sont inférieures à celles d’un modèle de sélection aléatoire ![Modèle de classification dont les performances sont inférieures à celles d’un modèle de sélection aléatoire](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

Exemple 2 : Les performances du modèle sont supérieures à celles d’un modèle de sélection aléatoire ![Modèle de classification qui offre de meilleures performances](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>Courbe de gains

Un graphique de gains évalue les performances d’un modèle de classification pour chaque partie des données. Il montre, pour chaque centile du jeu de données, les résultats que vous pouvez attendre par rapport à un modèle de sélection aléatoire.

Utilisez le graphique de gains cumulés pour choisir la limite de classification au moyen d’un pourcentage qui correspond à un gain souhaité à partir du modèle. Ces informations offrent une autre façon d’observer les résultats dans le graphique de courbes d’élévation associé.

Exemple 1 : Modèle de classification avec gain minimal ![Modèle de classification avec gain minimal](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

Exemple 2 : Modèle de classification avec gain significatif ![Modèle de classification avec gain significatif](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>Tracé d’étalonnage

Pour tous les problèmes de classification, vous pouvez consulter la ligne d’étalonnage concernant la micro-moyenne, la macro-moyenne et chaque classe dans un modèle prédictif donné. 

Un tracé d’étalonnage permet d’afficher le niveau de confiance d’un modèle prédictif. Pour ce faire, il affiche la relation entre la probabilité prévue et la probabilité réelle, où le terme « probabilité » représente la vraisemblance pour une instance spécifique d’appartenir à une étiquette. Un modèle bien étalonné s’aligne avec la ligne y=x, où il est raisonnablement confiant dans ses prédictions. Un modèle trop confiant s’aligne sur la ligne y=0, où la probabilité prévue est présente alors qu’il n’existe aucune probabilité réelle.

Exemple 1 : Modèle mieux étalonné ![ Modèle mieux étalonné](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

Exemple 2 : Modèle trop confiant ![Modèle trop confiant](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a> Résultats de la régression

Les trois métriques et graphiques suivants sont disponibles pour chaque modèle de régression que vous créez à l’aide des fonctionnalités de Machine Learning automatisé d’Azure Machine Learning

+ [Métriques](#reg-metrics)
+ [Prédiction et True](#pvt)
+ [Histogramme des résidus](#histo)


### <a name="reg-metrics"></a> Métriques de la régression

Les métriques suivantes sont enregistrées dans chaque itération d’exécution pour une tâche de régression ou de prévision.

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

### <a name="pvt"></a> Prédiction et True

Prédiction et True indique la relation entre une valeur prévue et sa valeur true en corrélation pour un problème de régression. Ce graphique peut servir à mesurer les performances d’un modèle, car plus les valeurs prévues sont proches de la ligne y=x, plus le modèle prédictif est précis.

Après chaque exécution, vous pouvez afficher un graphique de type Prédiction et True pour chaque modèle de régression. Pour protéger la confidentialité des données, les valeurs sont réunies dans un conteneur et la taille de chaque emplacement est affichée sous la forme d’un graphique à barres au bas de la zone de graphique. Vous pouvez comparer le modèle prédictif, dont la zone la plus claire indique les marges d’erreur, par rapport à la valeur idéale du modèle.

Exemple 1 : Modèle de régression avec faible précision dans les prédictions ![Modèle de régression avec faible précision dans les prédictions](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

Exemple 2 : Modèle de régression avec précision élevée dans les prédictions ![Modèle de régression avec précision élevée dans les prédictions](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)



### <a name="histo"></a> Histogramme des résidus

Un résidu représente une valeur y observée : la valeur y prévue. Pour afficher une marge d’erreur avec un biais faible, l’histogramme des résidus doit avoir la forme d’une cloche centrée sur 0. 

Exemple 1 : Modèle de régression avec biais dans ses erreurs ![Modèle de régression avec biais dans ses erreurs](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

Exemple 2 : Modèle de régression avec distribution plus équilibrée des erreurs ![Modèle de régression avec distribution plus équilibrée des erreurs](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a> Interprétabilité du modèle et importance des fonctionnalités

L’importance des fonctionnalités vous permet d’évaluer l’importance de chaque fonctionnalité dans la construction d’un modèle. Ce calcul est désactivé par défaut, car il peut augmenter considérablement le temps d'exécution.   Vous pouvez activer l'explication du modèle pour tous les modèles ou n'expliquer que le modèle le mieux adapté.

Vous pouvez consulter le score d’importance des fonctionnalités pour le modèle global, mais aussi pour chaque classe dans un modèle prédictif. Vous pouvez comparer l’importance d’une fonctionnalité dans chaque classe et de manière globale.

![Explication des fonctionnalités](./media/how-to-understand-automated-ml/feature-importance.gif)

Pour plus d'informations sur l'activation des fonctionnalités d'interprétabilité, voir [Configurer des expériences ML automatisées dans Python](how-to-configure-auto-train.md#explain-the-model-interpretability).  Pour obtenir un exemple décrivant le meilleur modèle, voir [Meilleure explication de modèle](how-to-auto-train-remote.md#explain).

## <a name="next-steps"></a>Étapes suivantes

+ Apprenez-en davantage sur le [ML automatisé](concept-automated-ml.md) dans Azure Machine Learning.
+ Testez l’exemple de notebook [Automated Machine Learning Model Explanation (Explication du modèle Machine Learning automatisé)](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation).
