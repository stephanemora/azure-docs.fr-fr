---
title: Choisir des algorithmes
titleSuffix: ML Studio (classic) - Azure
description: Guide pratique pour choisir des algorithmes Azure Machine Learning Studio (classique) pour l’apprentissage supervisé et non supervisé dans les expériences de clustering, de classification ou de régression.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: 2073123a61e919c10caaaea141f776e842f4d717
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427739"
---
# <a name="how-to-choose-algorithms-for-azure-machine-learning-studio-classic"></a>Guide pratique pour choisir des algorithmes pour Azure Machine Learning Studio (classique)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

La réponse à la question « Quel algorithme d’apprentissage automatique dois-je utiliser ? » est toujours « Cela dépend. ». Cela dépend de la taille, de la qualité et de la nature des données. Cela dépend de ce que vous voulez faire avec la réponse. Cela dépend de la conversion des calculs de l'algorithme en instructions pour l'ordinateur que vous utilisez. Et cela dépend du temps que vous avez. Même les scientifiques de données les plus expérimentés ne peuvent pas savoir quel algorithme fonctionnera le mieux avant d’en essayer.

Machine Learning Studio (classique) fournit des algorithmes de pointe tels que les arbres de décision optimisés évolutifs, les systèmes de recommandation bayésiens, les réseaux neuronaux profonds et les jungles de décision développés chez Microsoft Research. Des modules d’apprentissage automatique open source évolutifs tels que Vowpal Wabbit sont également inclus. Machine Learning Studio (classique) prend en charge les algorithmes de machine learning pour la classification, la régression et le clustering multiclasses et binaires. Consultez la liste complète des [Modules de Machine Learning](/azure/machine-learning/studio-module-reference/index).
La documentation fournit des informations sur chaque algorithme et sur la manière de régler les paramètres afin d’optimiser votre utilisation de l’algorithme choisi.  


## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Aide-mémoire d’algorithme Machine Learning

**[L’Aide-mémoire d’algorithme Microsoft Azure Machine Learning Studio](../algorithm-cheat-sheet.md)** vous aide à choisir l’algorithme d’apprentissage automatique adapté à vos solutions d’analyse prédictive dans la bibliothèque d’algorithmes Azure Machine Learning Studio.
Cet article explique pas à pas comment utiliser cet aide-mémoire.

> [!NOTE]
> Pour télécharger l’aide-mémoire et suivre cet article, accédez à [Aide-mémoire d'algorithme Machine Learning](../algorithm-cheat-sheet.md).
> 
> 

Ces recommandations sont des commentaires et des conseils compilés d’un grand nombre de scientifiques de données et d’experts en apprentissage automatique. Nous ne sommes pas d’accord avec toutes les opinions, mais nous avons essayé de trouver un consensus. Notre désaccord est généralement introduit par « Cela dépend... »


> [!TIP]
> Pour télécharger une vue d'ensemble infographique présentant de manière simple les concepts de base du Machine Learning afin de vous familiariser avec les algorithmes populaires utilisés pour répondre aux questions les plus courantes concernant le Machine Learning, consultez [Principes de base du Machine Learning avec exemples d'algorithmes](basics-infographic-with-algorithm-examples.md).

## <a name="flavors-of-machine-learning"></a>Types d'apprentissage automatique

### <a name="supervised"></a>Supervisé

Les algorithmes d'apprentissage supervisés font des prédictions basées sur un ensemble d'exemples. Par exemple, les historiques des cours peuvent être utiles pour estimer les prix futurs. Chaque exemple utilisé pour l'apprentissage est étiqueté avec la valeur d'intérêt : dans ce cas, le prix des actions. Un algorithme d'apprentissage supervisé recherche des modèles dans ces étiquettes de valeur. Il peut utiliser toutes les informations qui peuvent être pertinentes (le jour de la semaine, la saison, les données financières de l’entreprise, le type de secteur, l’existence d’événements géopolitiques perturbateurs), et chaque algorithme recherche différents types de modèles. Une fois que l'algorithme a trouvé le meilleur modèle possible, il l’utilise pour élaborer des prédictions pour les données de test sans étiquette : les prix futurs.

Il s’agit d’un type d’apprentissage automatique utile et apprécié. À une exception près, tous les modules dans Azure Machine Learning Studio (classique) sont des algorithmes d’apprentissage supervisé. Plusieurs types spécifiques d'apprentissage supervisé sont représentés dans Azure Machine Learning Studio (classique) : la classification, la régression et la détection d’anomalies.

* **Classification**. Lorsque les données sont utilisées pour prédire une catégorie, l’apprentissage supervisé est également appelé classification. C'est le cas lors de l'affectation d'une image en tant que photo d'un « chat » ou d’un « chien ». Quand il n’y a que deux choix, on appelle cela la **classification à deux classes** ou **binomiale**. Lorsqu'il existe plusieurs catégories, comme lors de la prévision du gagnant du championnat NCAA, ce problème est connu sous le nom de **classification à classes multiples**.
* **Régression**. Lorsque l’on prédit une valeur, comme le cours de la Bourse, l’apprentissage surveillé est appelé régression.
* **Détection des anomalies**. Parfois, l'objectif est d'identifier les points de données qui sont simplement inhabituels. Dans le cas de la détection des fraudes par exemple, toute dépense très étrange par carte de crédit est suspecte. Les variations possibles sont si nombreuses et les exemples de formation si rares, qu'il n'est pas possible de savoir à quoi ressemble une activité frauduleuse. L’approche de la détection des anomalies consiste simplement à apprendre à quoi ressemble l’activité normale (à l’aide d’un historique de transactions non frauduleuses) et à identifier les variations significatives.

### <a name="unsupervised"></a>Non supervisé

Dans l’apprentissage non supervisé, les points de données n’ont aucune étiquette associée. En effet, l’objectif d’un algorithme d’apprentissage non supervisé est d’organiser les données d’une certaine façon ou de décrire sa structure. Cela peut signifier un regroupement en clusters ou la recherche de différentes manières de visualisation des données complexes afin d’en simplifier l’affichage ou de l’organiser plus efficacement.

### <a name="reinforcement-learning"></a>Apprentissage par renforcement

Dans l’apprentissage par renforcement, l’algorithme choisit une action en réponse à chaque point de données. L’algorithme d’apprentissage reçoit également un signal de récompense quelques instants plus tard, qui indique la qualité de la décision.
En fonction de ce signal, l’algorithme modifie sa stratégie pour atteindre la récompense la plus élevée. Il n'existe actuellement aucun module d'apprentissage de renforcement dans Azure Machine Learning Studio (classique). L’apprentissage par renforcement est une approche courante en robotique, où le jeu des lectures des capteurs à un moment donné est un point de données et où l’algorithme doit choisir l’action suivante du robot. Il est également adapté aux applications d’Internet des objets.

## <a name="considerations-when-choosing-an-algorithm"></a>Considérations lors du choix d'un algorithme

### <a name="accuracy"></a>Précision

L’obtention de la réponse la plus précise possible n'est pas toujours nécessaire.
Parfois, en fonction de votre utilisation, une approximation suffit. Si tel est le cas, vous pourrez peut-être réduire le temps de traitement considérablement en utilisant des méthodes plus approximatives. Un autre avantage des méthodes plus approximatives est qu’elles ont naturellement tendance à éviter le surajustement.

### <a name="training-time"></a>Durée d’apprentissage

Le nombre de minutes ou d'heures nécessaires pour l'apprentissage d'un modèle varie beaucoup selon les algorithmes. La durée d’apprentissage est souvent étroitement liée à la précision : l’une accompagne généralement l'autre. En outre, certains algorithmes sont plus sensibles au nombre de points de données que d'autres.
Un temps limité peut guider le choix de l'algorithme, en particulier lorsque le jeu de données est vaste.

### <a name="linearity"></a>Linéarité

Un grand nombre d'algorithmes d'apprentissage automatique utilisent la linéarité. Les algorithmes de classification linéaire supposent que les classes peuvent être séparées par une ligne droite (ou son analogie de dimension supérieure). Ceux-ci incluent la régression logistique et les machines à vecteurs de support (comme implémentées dans Azure Machine Learning Studio (classique)).
Les algorithmes de régression linéaire supposent que les tendances des données suivent une ligne droite. Ces hypothèses ne sont pas erronées pour certains problèmes, mais réduisent la précision pour d’autres.

![Frontière de classe non linéaire](./media/algorithm-choice/image1.png)

***Limite de classe non linéaire*** *: l’utilisation d’un algorithme de classification linéaire entraînerait une justesse faible*

![Données avec une tendance non linéaire](./media/algorithm-choice/image2.png)

***Données avec une tendance non linéaire*** *: l’utilisation d’une méthode de régression linéaire entraînerait plus d’erreurs*

Malgré leurs limitations, les algorithmes linéaires sont très populaires comme première ligne d'attaque. Ils ont tendance à être des algorithmes simples et à apprentissage rapide.

### <a name="number-of-parameters"></a>Nombre de paramètres

Les paramètres sont les boutons que les scientifiques des données règlent lorsqu’ils configurent un algorithme. Ce sont des nombres qui affectent le comportement de l'algorithme, comme la tolérance aux erreurs ou le nombre d'itérations ou les variantes du comportement de l'algorithme. La durée d’apprentissage et la précision de l'algorithme peuvent parfois dépendre grandement du choix de paramètres appropriés. En règle générale, les algorithmes avec des paramètres à grands nombres nécessitent plus d’essais pour trouver la bonne combinaison.

Il existe également un bloc module de [balayage de paramètre](algorithm-parameters-optimize.md) dans Azure Machine Learning Studio (classique) qui essaie automatiquement toutes les combinaisons de paramètres à la précision que vous choisissez. Même si c'est un excellent moyen de vous assurer que vous avez examiné l'espace de paramétrage, le temps nécessaire pour former un modèle augmente de façon exponentielle avec le nombre de paramètres.

Avoir de nombreux paramètres indique généralement qu'un algorithme a une plus grande flexibilité. Cette méthode donne souvent une excellente précision, une fois que vous avez trouvé la bonne combinaison de paramètres.

### <a name="number-of-features"></a>Nombre de fonctionnalités

Pour certains types de données, le nombre de fonctionnalités peut être très important par rapport au nombre de points de données. C'est souvent le cas avec les données génétiques ou textuelles. Le grand nombre de fonctionnalités risque de ralentir certains algorithmes d'apprentissage et atteindre une durée d’apprentissage inutilisable. Les machines à vecteurs de support sont particulièrement bien adaptées à ce cas (voir ci-dessous).

### <a name="special-cases"></a>Cas particuliers

Certains algorithmes d'apprentissage effectuent des hypothèses particulières sur la structure des données ou les résultats souhaités. Si vous pouvez en trouver un qui répond à vos besoins, il peut vous donner des résultats plus pertinents, des prévisions plus précises ou des durées d'apprentissage plus courtes.

| **Algorithme** | **Précision** | **Durée d’apprentissage** | **Linéarité** | **Paramètres** | **Remarques** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Classification double classe.** | | | | | |
| [régression logique](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) | |● |● |5 | |
| [forêt de décision](/azure/machine-learning/studio-module-reference/two-class-decision-forest) |● |○ | |6 | |
| [jungle de décision](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) |● |○ | |6 |Faible encombrement de mémoire |
| [arbre de décision optimisé](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree) |● |○ | |6 |Encombrement de mémoire important |
| [réseau neuronal](/azure/machine-learning/studio-module-reference/two-class-neural-network) |● | | |9 |[Personnalisation supplémentaire possible](azure-ml-netsharp-reference-guide.md) |
| [perceptron moyenné](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) |○ |○ |● |4 | |
| [machines à vecteurs de support](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) | |○ |● |5 |Idéal pour les ensembles de fonctionnalités de grande taille |
| [Machine à vecteurs de support localement profonde](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) |○ | | |8 |Idéal pour les ensembles de fonctionnalités de grande taille |
| [Machine de point de Bayes](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine) | |○ |● |3 | |
| **Classification multiclasse.** | | | | | |
| [régression logique](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) | |● |● |5 | |
| [forêt de décision](/azure/machine-learning/studio-module-reference/multiclass-decision-forest) |● |○ | |6 | |
| [jungle de décision](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle) |● |○ | |6 |Faible encombrement de mémoire |
| [réseau neuronal](/azure/machine-learning/studio-module-reference/multiclass-neural-network) |● | | |9 |[Personnalisation supplémentaire possible](azure-ml-netsharp-reference-guide.md) |
| [un contre tous](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass) |- |- |- |- |Consultez les propriétés de la méthode à deux classes sélectionnée |
| **Régression** | | | | | |
| [linéaire](/azure/machine-learning/studio-module-reference/linear-regression) | |● |● |4 | |
| [linéaire bayésienne](/azure/machine-learning/studio-module-reference/bayesian-linear-regression) | |○ |● |2 | |
| [forêt de décision](/azure/machine-learning/studio-module-reference/decision-forest-regression) |● |○ | |6 | |
| [arbre de décision optimisé](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) |● |○ | |5 |Encombrement de mémoire important |
| [quantile de forêt rapide](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) |● |○ | |9 |Distributions plutôt que prédictions de points |
| [réseau neuronal](/azure/machine-learning/studio-module-reference/neural-network-regression) |● | | |9 |[Personnalisation supplémentaire possible](azure-ml-netsharp-reference-guide.md) |
| [Poisson](/azure/machine-learning/studio-module-reference/poisson-regression) | | |● |5 |Techniquement journal linéaire. Pour les décomptes prévisionnels |
| [ordinal](/azure/machine-learning/studio-module-reference/ordinal-regression) | | | |0 |Pour la prédiction de rang |
| **Détection des anomalies** | | | | | |
| [machines à vecteurs de support](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) |○ |○ | |2 |Particulièrement adapté aux ensembles de caractéristiques de grande taille |
| [Détection des anomalies reposant sur le PCA](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) | |○ |● |3 | |
| [K-moyennes](/azure/machine-learning/studio-module-reference/k-means-clustering) | |○ |● |4 |Un algorithme de clustering |

**Propriétés de l'algorithme :**

**●** : excellente précision, durée d'apprentissage courte et utilisation de la linéarité

**○** : bonne précision et durée d’apprentissage modérée

## <a name="algorithm-notes"></a>Notes de l'algorithme

### <a name="linear-regression"></a>Régression linéaire

Comme mentionné précédemment, la [régression linéaire](/azure/machine-learning/studio-module-reference/linear-regression) fait correspondre une ligne (ou plan ou hyperplan) au jeu de données. Elle est très efficace, simple et rapide, mais peut être trop simpliste pour certains problèmes.

![Données avec une tendance linéaire](./media/algorithm-choice/image3.png)

***Données avec une tendance linéaire***

### <a name="logistic-regression"></a>MLR (Microsoft Logistic Regression)

En dépit du terme « régression », la régression logique est en fait un puissant outil pour la classification à [deux classes](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) et à [classes multiples](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression). Elle est rapide et simple. Le fait qu'elle utilise une courbe en forme de S, et non une ligne droite, en fait une solution de choix pour diviser les données en groupes. La régression logique crée des limites de classes linéaires : vérifiez donc qu'une approximation linéaire vous convient.

![Régression logique pour les données à deux classes avec une seule caractéristique](./media/algorithm-choice/image4.png)

***Une régression logique pour les données à deux classes avec une seule caractéristique*** *: la limite de classe est le point auquel la courbe logistique est aussi proche des deux classes*

### <a name="trees-forests-and-jungles"></a>Arbres, forêts et jungles

Les forêts de décision ([régression](/azure/machine-learning/studio-module-reference/decision-forest-regression), [deux classes](/azure/machine-learning/studio-module-reference/two-class-decision-forest) et [classes multiples](/azure/machine-learning/studio-module-reference/multiclass-decision-forest)), les jungles de décision ([deux classes](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) et [classes multiples](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle)) et les arbres de décision renforcés ([régression](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) et [deux classes](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree)) sont tous basés sur les arbres de décision, un concept fondamental pour l’apprentissage automatique. Il existe de nombreuses variantes d'arbres de décision, mais tous font la même chose : subdiviser l'espace de la fonctionnalité en régions avec la même étiquette. Il peut s'agir des régions de catégorie ou de valeur constante, si vous effectuez une classification ou une régression.

![Arbre de décision subdivisant un espace de caractéristiques](./media/algorithm-choice/image5.png)

***Un arbre de décision divise un espace de caractéristique en régions de valeurs à peu près uniformes***

Étant donné qu’un espace de fonctionnalité peut être subdivisé en petites régions de taille arbitraire, il est facile de concevoir que la division atteigne un point de données par région. Il s’agit d’un exemple extrême de dépassement. Pour éviter ce problème, un grand nombre d’arbres sont construits, en s’assurant mathématiquement que les arbres ne soient pas corrélés. La moyenne de cette « forêt de décision » est un arbre qui permet d'éviter un surajustement. Les forêts de décision peuvent utiliser beaucoup de mémoire. Les jungles de décision sont une variante qui consomme moins de mémoire, mais avec une durée d’apprentissage légèrement plus longue.

Les arbres de décision améliorée évitent le surajustement en limitant le nombre de subdivisions et le nombre minimum de points de données autorisés dans chaque région. L'algorithme construit une séquence d'arbres, chacun d'entre eux apprenant à compenser l'erreur de l'arbre précédent. Cela fournit un apprenant très précis qui a tendance à utiliser beaucoup de mémoire. Pour une description complète, consultez le [livre d'origine de Friedman](https://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[La régression quantile de forêt rapide](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) est une variante d'arbres de décision pour les cas où vous souhaitez savoir non seulement la valeur standard (médiane) des données au sein d'une région, mais également sa distribution sous la forme de quantiles.

### <a name="neural-networks-and-perceptrons"></a>Perceptrons et réseaux neuronaux

Les réseaux neuronaux sont des algorithmes d’apprentissage inspirés du cerveau couvrant les problèmes [de classes multiples](/azure/machine-learning/studio-module-reference/multiclass-neural-network), [à deux classes](/azure/machine-learning/studio-module-reference/two-class-neural-network) et de [régression](/azure/machine-learning/studio-module-reference/neural-network-regression). Il existe de nombreux réseaux neuronaux, mais ceux d’Azure Machine Learning Studio (classique) sont tous des graphes acycliques dirigés. Cela signifie que les fonctionnalités d'entrée sont transmises vers l'avant (jamais vers l'arrière) via une séquence de couches, avant d'être transformées en sorties. Dans chaque couche, les entrées sont pondérées dans diverses combinaisons, additionnées et transmises à la couche suivante. Cette combinaison de calculs simples permet d'apprendre les tendances de données et les limites de classe sophistiquées, en toute simplicité. Les réseaux à plusieurs couches de ce type effectuent « l’apprentissage approfondi » qui alimente tant d’articles technologiques et de science-fiction.

Ces performances élevées ont toutefois un prix. L’apprentissage des réseaux neuronaux peut prendre beaucoup de temps, en particulier pour les grands jeux de données avec un grand nombre de fonctionnalités. Ils ont également plus de paramètres que la plupart des algorithmes, ce qui signifie que le balayage de paramètres allonge grandement la durée d’apprentissage.
Et pour les perfectionnistes qui souhaitent [spécifier leur propre structure de réseau](azure-ml-netsharp-reference-guide.md), les possibilités sont infinies.

![Limites apprises par les réseaux neuronaux](./media/algorithm-choice/image6.png)

***Les limites apprises par les réseaux neuronaux peuvent être complexes et irrégulières***

Le [perceptron moyenné à deux classes](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) est le réseau neuronal idéal pour répondre à l’explosion des durées d'apprentissage. Il utilise une structure de réseau qui fournit des limites de la classe linéaire. Il est presque primitif au vu des normes actuelles, mais il a un long historique d'utilisation fiable et est suffisamment petit pour apprendre rapidement.

### <a name="svms"></a>Machines à vecteurs de support (SVM)

Les machines à vecteurs de support (SVM) recherchent la limite qui sépare les classes par une marge aussi grande que possible. Lorsque les deux classes ne peuvent pas être clairement séparées, les algorithmes trouvent la meilleure limite possible. Comme présenté dans Azure Machine Learning Studio (classique), la [SVM à deux classes](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) le fait uniquement avec une ligne droite (ou noyau linéaire dans le langage SVM).
Étant donné qu’elle utilise cette approximation linéaire, elle est capable de s'exécuter assez rapidement. Elle est particulièrement utile pour les données avec de nombreuses fonctionnalités comme les données textuelles ou de génome. Dans ces cas, les SVM sont en mesure de séparer les classes plus rapidement et avec moins de surajustement que la plupart des autres algorithmes, tout en utilisant une petite quantité de mémoire.

![Frontière de classe de machine à vecteurs de support](./media/algorithm-choice/image7.png)

***Une frontière de classe de machine à vecteurs de support standard optimise la marge séparant deux classes***

Un autre produit de Microsoft Research, la [SVM localement approfondi à deux classes](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) est une variante non linéaire de SVM qui conserve la plupart de l'efficacité de la mémoire et de la vitesse de la version linéaire. Elle est idéale pour les cas où l'approche linéaire ne donne pas de réponses suffisamment précises. Les développeurs ont conservé sa rapidité en décomposant le problème en plusieurs petits problèmes SVM linéaires. Lisez la [description complète](http://proceedings.mlr.press/v28/jose13.html) pour plus d'informations sur la méthode utilisée.

À l'aide d'une extension intelligente de SVM non linéaires, la [SVM à une classe](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) dessine une limite qui encadre étroitement l'ensemble de données. Elle est utile pour la détection des anomalies. Les nouveaux points de données qui se situent bien à l'extérieur de cette limite sont assez inhabituels pour être remarqués.

### <a name="bayesian-methods"></a>Méthodes bayésiennes

Les méthodes bayésiennes ont une qualité très intéressante : elles évitent le surajustement. Pour cela, elles émettent quelques hypothèses préalables sur la distribution probable de la réponse. Un autre avantage de cette approche est qu'elle a très peu de paramètres. Azure Machine Learning Studio (classique) a des algorithmes bayésiens pour la classification ([machine de points à deux classes de Bayes](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine)) et la régression ([régression linéaire bayésienne](/azure/machine-learning/studio-module-reference/bayesian-linear-regression)).
Notez qu’ils supposent que les données peuvent être fractionnées ou ajustées avec une ligne droite.

Anecdote historique, les machines de point de Bayes ont été développées par Microsoft Research. Elles reposent sur un travail théorique exceptionnel. Pour plus d’informations, consultez [l’article d’origine dans JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) et le [blog de Chris Bishop](https://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Algorithmes spécialisés
Avoir un objectif très spécifique peut être bénéfique. La collection Azure Machine Learning Studio (classique) comprend des algorithmes spécialisés dans les domaines suivants :

- Prédiction de classement ([régression ordinale](/azure/machine-learning/studio-module-reference/ordinal-regression))
- Prédiction de nombres ([régression Poisson](/azure/machine-learning/studio-module-reference/poisson-regression))
- Détection des anomalies (un basé sur [l’analyse des principaux composants](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) et un autre basé sur les [machines à vecteurs de support](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine))
- Clustering ([K-moyennes](/azure/machine-learning/studio-module-reference/k-means-clustering))

![Détection des anomalies reposant sur le PCA](./media/algorithm-choice/image8.png)

***Détection des anomalies reposant sur le PCA*** *: la grande majorité des données correspond à une distribution typique ; les points déviant considérablement de cette distribution sont suspects*

![Jeu de données regroupé à l'aide de K-moyennes](./media/algorithm-choice/image9.png)

***Un jeu de données est regroupé dans cinq clusters à l’aide de K-moyennes.***

Il existe également un ensemble [de classifieur à classes multiples one-v-all](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass), qui subdivise le problème de classification de classe N en problèmes de classification à deux classes N-1. La précision, la durée d’apprentissage et les propriétés de linéarité sont déterminées par les classifieurs à deux classes utilisés.

![Deux classifieurs à deux classes combinés pour former un classifieur à trois classes](./media/algorithm-choice/image10.png)

***Deux classifieurs à deux classes combinés pour former un classifieur à trois classes***

Azure Machine Learning Studio (classique) inclut également l’accès à une puissante infrastructure de machine learning appelée [Vowpal Wabbit](/azure/machine-learning/studio-module-reference/train-vowpal-wabbit-version-7-4-model).
VW défie la catégorisation ici, puisqu'elle peut apprendre des problèmes de classification et de régression et même utiliser des données partiellement sans étiquette. Vous pouvez la configurer pour utiliser l'un des nombreux algorithmes d’apprentissage, les fonctions de perte et les algorithmes d'optimisation. Elle a été conçue dès le départ pour être efficace, parallèle et extrêmement rapide. Elle gère d’immenses ensembles de fonctionnalités avec peu d'effort.
Lancée et dirigée par John Langford de Microsoft Research, VW est une Formule 1 dans la course des algorithmes. VW ne convient pas à tous les problèmes, mais si c’est le cas pour le vôtre, il est avantageux de l’utiliser. Elle est également disponible en tant que [code open source autonome](https://github.com/JohnLangford/vowpal_wabbit) dans plusieurs langues.

## <a name="next-steps"></a>Étapes suivantes

* Pour télécharger une vue d'ensemble infographique présentant de manière simple les concepts de base du Machine Learning afin de vous familiariser avec les algorithmes populaires utilisés pour répondre aux questions les plus courantes concernant le Machine Learning, consultez [Principes de base du Machine Learning avec exemples d'algorithmes](basics-infographic-with-algorithm-examples.md).

* Pour obtenir la liste par catégorie de tous les algorithmes d’apprentissage automatique disponibles dans Machine Learning Studio (classique), consultez [Initialiser le modèle](/azure/machine-learning/studio-module-reference/machine-learning-initialize-model) dans Machine Learning Studio (classique) : aide sur les algorithmes et les modules.

* Pour obtenir la liste alphabétique complète des algorithmes et modules disponibles dans Machine Learning Studio (classique), consultez [Liste alphabétique des modules Machine Learning Studio (classique)](/azure/machine-learning/studio-module-reference/a-z-module-list) dans Machine Learning Studio (classique) : aide sur les algorithmes et les modules.
