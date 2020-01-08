---
title: Comprendre les résultats des ML automatisés
titleSuffix: Azure Machine Learning
description: Apprenez à visualiser et à comprendre les graphiques et les métriques pour chacune de vos exécutions de Machine Learning automatisées.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 69cf79f8258f85f2fb5e787f91aa843837d0a3a1
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75534693"
---
# <a name="understand-automated-machine-learning-results"></a>Comprendre les résultats des Machine Learning automatisés
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous apprenez à visualiser et à comprendre les graphiques et les métriques pour chacune de vos exécutions de Machine Learning automatisées. 

Pour en savoir plus :
+ [Métriques, graphiques et courbes pour les modèles de classification](#classification)
+ [Métriques et graphiques pour les modèles de régression](#regression)
+ [Interprétabilité du modèle et importance des fonctionnalités](#explain-model)

## <a name="prerequisites"></a>Conditions préalables requises

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

* Créez une expérience pour votre exécution de machine learning automatisé, que ce soit avec le SDK ou dans Azure Machine Learning Studio.

    * Utilisez le Kit de développement logiciel (SDK) pour développer un [modèle de classification](how-to-auto-train-remote.md) ou un [modèle de régression](tutorial-auto-train-models.md)
    * Utilisez [Azure Machine Learning Studio](how-to-create-portal-experiments.md) pour créer un modèle de classification ou de régression en chargeant les données appropriées.

## <a name="view-the-run"></a>Afficher l’exécution

Après avoir exécuté une expérience de machine learning automatisé, un historique des exécutions est disponible dans votre espace de travail de Machine Learning. 

1. Accédez à votre espace de travail.

1. Dans le panneau gauche de l'espace de travail, sélectionnez **Expériences**.

   ![Capture d’écran du menu Expérience](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Dans la liste des expériences, sélectionnez celle que vous voulez explorer.

   [![Liste d’expériences](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Dans la table du bas, sélectionnez l’**Exécution**.

   [![Exécution de l’expérience](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. Dans les modèles, sélectionnez le **Nom de l’algorithme** pour le modèle que vous voulez explorer plus en détail.

   [![Modèle d’expérience](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

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

Métrique|Description|Calcul|Paramètres supplémentaires
--|--|--|--
AUC_Macro| « AUC » est « Area under the Receiver Operating Characteristic Curve » (la zone sous la courbe caractéristique de fonctionnement du récepteur). « Macro » est la moyenne arithmétique de l’AUC pour chaque classe.  | [Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| « AUC » est « Area under the Receiver Operating Characteristic Curve » (la zone sous la courbe caractéristique de fonctionnement du récepteur). « Micro » est calculé globalement en combinant les vrais positifs et les faux positifs de chaque classe.| [Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | « AUC » est « Area under the Receiver Operating Characteristic Curve » (la zone sous la courbe caractéristique de fonctionnement du récepteur). « Weighted » est la moyenne arithmétique du score pour chaque classe, pondérée par le nombre d’instances « true » dans chaque classe.| [Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|La précision est le pourcentage d’étiquettes prédites qui correspondent exactement aux étiquettes réelles. |[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |None|
average_precision_score_macro|La précision moyenne résume la courbe précision-rappel comme moyenne pondérée des précisions atteintes à chaque seuil, avec l’augmentation du rappel du seuil précédent utilisé comme pondération. Macro est la moyenne arithmétique du score de précision moyen de chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|La précision moyenne résume la courbe précision-rappel comme moyenne pondérée des précisions atteintes à chaque seuil, avec l’augmentation du rappel du seuil précédent utilisé comme pondération. « Micro » est calculé globalement en combinant les vrais positifs et les faux positifs de chaque limite.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|La précision moyenne résume la courbe précision-rappel comme moyenne pondérée des précisions atteintes à chaque seuil, avec l’augmentation du rappel du seuil précédent utilisé comme pondération. « Weighted » est la moyenne arithmétique du score de précision moyen pour chaque classe, pondérée par le nombre d’instances « true » dans chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|La précision équilibrée est la moyenne arithmétique du rappel pour chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|Le score F1 est la moyenne harmonique de la précision et du rappel. « Macro » est la moyenne arithmétique du score F1 pour chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|Le score F1 est la moyenne harmonique de la précision et du rappel. « Micro » est calculé globalement en comptant le total des vrais positifs, des faux négatifs et des faux positifs.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|Le score F1 est la moyenne harmonique de la précision et du rappel. Moyenne pondérée par fréquence de classe du score F1 pour chaque classe|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|Il s’agit de la fonction de perte utilisée dans la régression logistique (multinomiale) et les extensions de celle-ci, comme les réseaux neuronaux, définie comme la probabilité logarithmique négative des étiquettes réelles, étant données les prédictions d’un classifieur probabiliste. Pour un échantillon unique avec l’étiquette vraie yt dans {0,1} et la probabilité estimée yp que yt = 1, la perte logarithmique est - -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp)).|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|None|
norm_macro_recall|Le rappel macro normalisé est tel que la performance aléatoire ait un score de 0 et la performance parfaite ait un score de 1. Ceci est réalisé par norm_macro_recall := (recall_score_macro - R)/(1 - R), où R est la valeur attendue de recall_score_macro pour des prédictions aléatoires (c’est-à-dire R=0,5 pour la classification binaire et R=(1/C) pour les problèmes de classification de classe C).|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "macro" |
precision_score_macro|La précision est le pourcentage d’éléments prédits positivement qui sont correctement étiquetés. « Macro » est la moyenne arithmétique de la précision pour chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|La précision est le pourcentage d’éléments prédits positivement qui sont correctement étiquetés. « Micro » est calculé globalement en comptant le total des vrais positifs et des faux positifs.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|La précision est le pourcentage d’éléments prédits positivement qui sont correctement étiquetés. « Weighted » est la moyenne arithmétique de la précision pour chaque classe, pondérée par le nombre d’instances « true » dans chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Le rappel est le pourcentage d’éléments d’une certaine classe correctement étiquetés. « Macro » est la moyenne arithmétique du rappel pour chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Le rappel est le pourcentage d’éléments d’une certaine classe correctement étiquetés. « Micro » est calculé globalement en comptant le total des vrais positifs, des faux négatifs et des faux positifs.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Le rappel est le pourcentage d’éléments d’une certaine classe correctement étiquetés. « Weighted » est la moyenne arithmétique du rappel pour chaque classe, pondérée par le nombre d’instances « true » dans chaque classe.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|La précision pondérée est la précision où le poids donné à chaque exemple est égal à la proportion d’instances « true » dans la classe « true » de cet exemple.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight est un vecteur égal à la proportion de cette classe pour chaque élément dans la cible|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Matrice de confusion

#### <a name="what-is-a-confusion-matrix"></a>Qu’est-ce qu’une matrice de confusion ?
Une matrice de confusion décrit les performances d’un modèle de classification. Chaque ligne affiche les instances de la classe true, ou de la classe actuelle dans votre jeu de données, et chaque colonne représente les instances de la classe qui a été prédite par le modèle. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Que fait le Machine Learning automatisé avec la matrice de confusion ?
Pour les problèmes de classification, Azure Machine Learning fournit automatiquement une matrice de confusion associée à chaque modèle généré. Pour chaque matrice de confusion, le Machine Learning automatisé affiche la fréquence de chaque étiquette Prédiction (colonne) par rapport à celle de chaque étiquette True (ligne). Plus la couleur est sombre, plus le nombre dans cette partie de la matrice est élevé. 

#### <a name="what-does-a-good-model-look-like"></a>À quoi ressemble un bon modèle ?
Nous comparons la valeur réelle du jeu de données aux valeurs prédites par le modèle. Pour cette raison, les modèles Machine Learning seront d’une plus grande justesse si la plupart de leurs valeurs se trouvent le long de la diagonale (ce qui signifie que le modèle a prédit la bonne valeur). Si un modèle a un déséquilibre de classe, la matrice de confusion permet de détecter un modèle biaisé.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Exemple 1 : Modèle de classification avec un niveau de justesse bas
![Modèle de classification avec un niveau de justesse bas](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Exemple 2 : Modèle de classification avec un niveau de justesse élevé 
![Modèle de classification avec un niveau de justesse élevé](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Exemple 3 : Modèle de classification avec un niveau de justesse élevé et un biais élevé dans les prédictions de modèle
![Modèle de classification avec un niveau de justesse élevé et un biais élevé dans les prédictions de modèle](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Graphique de rappel de précision
#### <a name="what-is-a-precision-recall-chart"></a>Qu’est-ce qu’un graphique Précision et rappel ?
La courbe Précision et rappel montre la relation qui existe entre la précision et le rappel dans un modèle. Le terme « précision » désigne la capacité d’un modèle à étiqueter correctement toutes les instances. Le rappel représente la capacité d’un classifieur à rechercher toutes les instances d’une étiquette spécifique.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Que fait le Machine Learning automatisé avec le graphique Précision et rappel ?

Avec ce graphique, vous pouvez comparer les courbes de rappel de précision pour chaque modèle afin de déterminer quel modèle présente une relation acceptable entre précision et rappel pour votre problème d’entreprise spécifique. Ce graphique montre le rappel de précision de macro-moyenne, le rappel de précision de micro-moyenne et le rappel de précision associé à toutes les classes d’un modèle. 

La macro-moyenne calcule la métrique indépendamment de chaque classe, puis elle prend la moyenne en traitant toutes les classes de manière égale. La micro-moyenne, quant à elle, agrège les contributions de toutes les classes pour calculer la moyenne. La micro-moyenne est préférable si le jeu de données contient un déséquilibre de classe.

#### <a name="what-does-a-good-model-look-like"></a>À quoi ressemble un bon modèle ?
La courbe Précision et rappel idéale dépend de l’objectif du problème métier. Voici quelques exemples :

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Exemple 1 : Modèle de classification avec un niveau faible de précision et de rappel
![Modèle de classification avec un niveau faible de précision et de rappel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Exemple 2 : Modèle de classification avec une précision et un rappel d’environ 100 % 
![Modèle de classification avec une précision et un rappel élevés](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>Graphique ROC

#### <a name="what-is-a-roc-chart"></a>Qu’est-ce qu’un graphique ROC ?
Le ROC (Receiver Operating Characteristic) est un tracé d’étiquettes bien classées et mal classées pour un modèle spécifique. La courbe ROC peut être moins informative lors de la formation de modèles sur des jeux de données présentant un biais élevé, car elle n’affiche pas les étiquettes de type faux positif.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>Que fait le Machine Learning automatisé avec un graphique ROC ?
Le Machine Learning automatisé génère la macro-moyenne Précision et rappel, la micro-moyenne Précision et rappel, ainsi que la valeur Précision et rappel associée à toutes les classes d’un modèle. 

La macro-moyenne calcule la métrique indépendamment de chaque classe, puis elle prend la moyenne en traitant toutes les classes de manière égale. La micro-moyenne, quant à elle, agrège les contributions de toutes les classes pour calculer la moyenne. La micro-moyenne est préférable si le jeu de données contient un déséquilibre de classe.

#### <a name="what-does-a-good-model-look-like"></a>À quoi ressemble un bon modèle ?
Dans l’idéal, le modèle aura un taux de vrais positifs proche de 100 %, et un taux de faux positifs proche de 0 %. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Exemple 1 : Modèle de classification avec un nombre faible d’étiquettes Vrais et un nombre élevé d’étiquettes Faux
![Modèle de classification avec un nombre faible d’étiquettes Vrais et un nombre élevé d’étiquettes Faux](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Exemple 2 : Modèle de classification avec un nombre élevé d’étiquettes Vrais et un nombre faible d’étiquettes Faux
![Modèle de classification avec un nombre élevé d’étiquettes Vrais et un nombre faible d’étiquettes Faux](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Graphique de courbes d’élévation
#### <a name="what-is-a-lift-chart"></a>Qu’est-ce qu’un graphique de courbes d’élévation ?
Les graphiques de courbes d’élévation permettent d’évaluer les performances d’un modèle de classification. Ils montrent que l’utilisation du modèle généré permet une plus grande précision.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Que fait le Machine Learning automatisé avec un graphique de courbes d’élévation ?
Vous pouvez comparer l’élévation du modèle généré automatiquement avec Azure Machine Learning par rapport à la ligne de base afin d’afficher le gain de valeur de ce modèle spécifique.
#### <a name="what-does-a-good-model-look-like"></a>À quoi ressemble un bon modèle ?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Exemple 1 : Modèle de classification avec des performances moins élevées que celles d’un modèle de sélection aléatoire
![Modèle de classification avec des performances moins élevées que celles d’un modèle de sélection aléatoire](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Exemple 2 : Modèle de classification avec des performances plus élevées que celles d’un modèle de sélection aléatoire
![Modèle de classification plus efficace](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>Graphique des gains
#### <a name="what-is-a-gains-chart"></a>Qu’est-ce qu’un graphique des gains ?

Un graphique de gains évalue les performances d’un modèle de classification pour chaque partie des données. Il montre, pour chaque centile du jeu de données, les résultats que vous pouvez attendre par rapport à un modèle de sélection aléatoire.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Que fait le Machine Learning automatisé avec un graphique des gains ?
Utilisez le graphique de gains cumulés pour choisir la limite de classification au moyen d’un pourcentage qui correspond à un gain souhaité à partir du modèle. Ces informations offrent une autre façon d’observer les résultats dans le graphique de courbes d’élévation associé.

#### <a name="what-does-a-good-model-look-like"></a>À quoi ressemble un bon modèle ?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Exemple 1 : Modèle de classification avec des gains minimaux
![Modèle de classification avec des gains minimaux](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Exemple 2 : Modèle de classification avec des gains importants
![Modèle de classification avec des gains importants](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Graphique d’étalonnage

#### <a name="what-is-a-calibration-chart"></a>Qu’est-ce qu’un graphique d’étalonnage ?
Un tracé d’étalonnage permet d’afficher le niveau de confiance d’un modèle prédictif. Pour ce faire, il affiche la relation entre la probabilité prévue et la probabilité réelle, où le terme « probabilité » représente la vraisemblance pour une instance spécifique d’appartenir à une étiquette.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Que fait le Machine Learning automatisé avec un graphique d’étalonnage ?
Pour tous les problèmes de classification, vous pouvez consulter la ligne d’étalonnage concernant la micro-moyenne, la macro-moyenne et chaque classe dans un modèle prédictif donné.

La macro-moyenne calcule la métrique indépendamment de chaque classe, puis elle prend la moyenne en traitant toutes les classes de manière égale. La micro-moyenne, quant à elle, agrège les contributions de toutes les classes pour calculer la moyenne. 
#### <a name="what-does-a-good-model-look-like"></a>À quoi ressemble un bon modèle ?
 Un modèle bien étalonné s’aligne sur la ligne y=x, où il est raisonnablement confiant dans ses prédictions. Un modèle trop confiant s’aligne sur la ligne y=0, où la probabilité prévue est présente alors qu’il n’existe aucune probabilité réelle. 


##### <a name="example-1-a-well-calibrated-model"></a>Exemple 1 : Modèle bien étalonné
![ Autre modèle bien étalonné](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Exemple 2 : Modèle trop confiant
![Modèle trop confiant](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a> Résultats de la régression

Les trois métriques et graphiques suivants sont disponibles pour chaque modèle de régression que vous créez à l’aide des fonctionnalités de Machine Learning automatisé d’Azure Machine Learning

+ [Métriques](#reg-metrics)
+ [Prédiction et True](#pvt)
+ [Histogramme des résidus](#histo)


### <a name="reg-metrics"></a> Métriques de la régression

Les métriques suivantes sont enregistrées dans chaque itération d’exécution pour une tâche de régression ou de prévision.

|Métrique|Description|Calcul|Paramètres supplémentaires
--|--|--|--|
explained_variance|La variance expliquée est la proportion selon laquelle un modèle mathématique compte pour la variation d’un jeu de données particulier. C’est le pourcentage de diminution de la variance des données d’origine par rapport à la variance des erreurs. Quand la moyenne des erreurs est 0, elle est égale à la variance expliquée.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|None|
r2_score|R2 est le coefficient de détermination ou le pourcentage de réduction dans les erreurs quadratiques comparé à un modèle de référence qui génère la moyenne. |[Calcul](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|None|
spearman_correlation|La corrélation de Spearman est une mesure non paramétrique de la monotonie de la relation entre deux jeux de données. Contrairement à la corrélation de Pearson, la corrélation de Spearman ne suppose pas que les deux jeux de données sont normalement distribués. Comme d’autres coefficients de corrélation, celle-ci varie entre -1 et +1, 0 impliquant l’absence de corrélation. Les corrélations de -1 ou +1 impliquent une relation monotone exacte. Les corrélations positives impliquent que quand x augmente, y augmente également. Les corrélations négatives impliquent que quand x augmente, y diminue.|[Calcul](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|None|
mean_absolute_error|L’erreur d’absolue moyenne est la valeur attendue de la valeur absolue de la différence entre la cible et la prédiction|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|None|
normalized_mean_absolute_error|L’erreur d’absolue moyenne normalisée est l’erreur absolue moyenne divisée par la plage des données|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Diviser par la plage de données|
median_absolute_error|L’erreur absolue médiane est la médiane de toutes les différences absolues entre la cible et la prédiction. Cette perte est robuste pour les valeurs hors norme.|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|None|
normalized_median_absolute_error|L’erreur d’absolue médiane normalisée est l’erreur absolue médiane divisée par la plage des données|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Diviser par la plage de données|
root_mean_squared_error|L’erreur quadratique moyenne racine est la racine carrée de la différence quadratique attendue entre la cible et la prédiction|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|None|
normalized_root_mean_squared_error|L’erreur quadratique moyenne racine normalisée est l’erreur quadratique moyenne racine divisée par la plage des données|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Diviser par la plage de données|
root_mean_squared_log_error|L’erreur logarithmique quadratique moyenne racine est la racine carrée de l’erreur logarithmique quadratique attendue|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|None|
normalized_root_mean_squared_log_error|L’erreur logarithmique quadratique moyenne racine normalisée est l’erreur logarithmique quadratique moyenne racine divisée par la plage des données|[Calcul](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Diviser par la plage de données|

### <a name="pvt"></a> Prédiction et True
#### <a name="what-is-a-predicted-vs-true-chart"></a>Qu’est-ce qu’un graphique Prédiction et True ?
Prédiction et True indique la relation entre une valeur prévue et sa valeur true en corrélation pour un problème de régression. Ce graphique peut servir à mesurer les performances d’un modèle, car plus les valeurs prévues sont proches de la ligne y=x, plus le modèle prédictif est précis.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Que fait le Machine Learning automatisé avec un graphique Prédictions et True ?
Après chaque exécution, vous pouvez afficher un graphique de type Prédiction et True pour chaque modèle de régression. Pour protéger la confidentialité des données, les valeurs sont réunies dans un conteneur et la taille de chaque emplacement est affichée sous la forme d’un graphique à barres au bas de la zone de graphique. Vous pouvez comparer le modèle prédictif, dont la zone la plus claire indique les marges d’erreur, par rapport à la valeur idéale du modèle.

#### <a name="what-does-a-good-model-look-like"></a>À quoi ressemble un bon modèle ?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Exemple 1 : Modèle de classification avec un niveau de justesse faible
![Modèle de régression avec une justesse faible des prédictions](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Exemple 2 : Modèle de régression avec un niveau de justesse élevé 
[![Modèle de régression avec un niveau de justesse élevé des prédictions](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a> Histogramme des résidus
#### <a name="what-is-a-residuals-chart"></a>Qu’est-ce qu’un graphique des résidus ?
Un résidu représente une valeur y observée : la valeur y prévue. Pour afficher une marge d’erreur avec un biais faible, l’histogramme des résidus doit avoir la forme d’une cloche centrée sur 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Que fait le Machine Learning automatisé avec un graphique des résidus ?
Le Machine Learning automatisé fournit automatiquement un graphique des résidus pour montrer comment sont réparties les erreurs au sein des prédictions.
#### <a name="what-does-a-good-model-look-like"></a>À quoi ressemble un bon modèle ?
Un bon modèle a généralement une courbe en cloche ou un taux d’erreurs proche de zéro.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Exemple 1 : Modèle de régression avec des erreurs comprenant un biais
![Modèle de régression avec des erreurs comprenant un biais](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Exemple 2 : Modèle de régression avec des erreurs mieux réparties
![Modèle de régression avec des erreurs mieux réparties](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a> Interprétabilité du modèle et importance des fonctionnalités
Le Machine Learning automatisé fournit un tableau de bord d’interprétation Machine Learning pour vos exécutions.
Pour plus d’informations sur l’activation des caractéristiques d’interprétabilité, consultez le [guide pratique](how-to-machine-learning-interpretability-automl.md) sur l’activation de l’interprétabilité dans des expériences de machine learning automatisé.

## <a name="next-steps"></a>Étapes suivantes

+ Apprenez-en davantage sur le [ML automatisé](concept-automated-ml.md) dans Azure Machine Learning.
+ Testez les exemples de notebooks disponibles dans [Automated Machine Learning Model Explanation ](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
