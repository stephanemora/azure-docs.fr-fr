---
title: Interprétabilité des modèles dans Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Découvrez comment expliquer pourquoi votre modèle élabore des prédictions à l’aide du Kit de développement logiciel (SDK) Azure Machine Learning. Il peut être utilisé pendant la formation et l’inférence pour comprendre comment un modèle élabore des prédictions.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 35623be4f0e4495388dc58b1e4d4f6c4663a93fd
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75534317"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Interprétabilité des modèles dans Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Vue d’ensemble de l’interprétabilité des modèles

L’interprétabilité est essentielle autant pour les scientifiques des données que pour les décisionnaires en entreprise pour garantir la conformité avec les stratégies d’entreprise, les normes du secteur et les réglementations gouvernementales :
+ Les scientifiques des données doivent être capables d’expliquer leurs modèles aux dirigeants et aux parties prenantes, afin que ces derniers puissent comprendre la valeur et la précision de leurs découvertes 
+ Les décisionnaires en entreprise veulent être sûrs de pouvoir jouer la transparence pour que les utilisateurs finaux gagnent et gardent leur confiance.

La capacité à expliquer un modèle Machine Learning est importante lors de deux phases principales du développement de modèle :
+ Pendant la phase d’entraînement du cycle de développement du modèle Machine Learning. Les concepteurs et évaluateurs de modèles peuvent utiliser le résultat de l’interprétabilité d’un modèle pour vérifier des hypothèses et gagner la confiance des parties prenantes. Ils utilisent également les insights qui ressortent du modèle pour le débogage, la confirmation que le comportement du modèle correspond à leurs objectifs et la recherche de biais ou de caractéristiques sans importance.
+ Au cours de la phase d’inférence, bénéficier d’une transparence sur les modèles déployés permet aux dirigeants de comprendre comment, « une fois déployé », le modèle fonctionne et comment ses décisions traitent et influencent les gens dans la vraie vie. 

## <a name="interpretability-with-azure-machine-learning"></a>Interprétabilité avec Azure Machine Learning

Dans cet article, vous allez découvrir comment les concepts d’interprétabilité des modèles sont implémentés dans le SDK.

Grâce aux classes et aux méthodes dans le Kit de développement logiciel (SDK), vous pouvez obtenir les informations suivantes :
+ Valeurs importantes pour les fonctionnalités brutes et conçues
+ Interprétabilité sur des jeux de données réels à grande échelle, pendant la formation et l’inférence.
+ Visualisations interactives pour vous aider dans la découverte de modèles de données et d’explications au moment de la formation


Dans Machine Learning, les **fonctionnalités** sont les champs de données utilisés pour prédire un point de données cible. Par exemple, pour prédire le risque de crédit, les champs de données d’âge, de taille du compte et d’âge de compte peuvent servir. Dans ce cas, l’âge, la taille du compte et l’âge du compte sont des **fonctionnalités**. L’importance d’une fonctionnalité vous indique dans quelle mesure chaque champ de données a contribué aux prédictions du modèle. Par exemple, l’âge peut être fortement utilisé dans la prédiction tandis que la taille et l’âge du compte n’affectent pas significativement la précision de la prédiction. Ce processus permet aux scientifiques des données d’expliquer les prédictions obtenues de sorte que les parties prenantes aient une visibilité sur les points de données les plus importants dans le modèle.

À l’aide de ces outils, vous pouvez expliquer les modèles Machine Learning **globalement sur toutes les données**, ou **localement sur un point de données spécifique** à l’aide de technologies de pointe, de manière simple et évolutive.

Les classes d’interprétabilité sont accessibles via plusieurs packages de Kit de développement logiciel (SDK). En savoir plus sur l’[installation de packages de Kit de développement logiciel (SDK) pour Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* `azureml.interpret`, le package principal, qui contient des fonctionnalités prises en charge par Microsoft.

* `azureml.contrib.interpret`, fonctionnalités en préversion et expérimentales que vous pouvez essayer.

* `azureml.train.automl.automlexplainer`, package pour l’interprétation des modèles de Machine learning automatisé.

> [!IMPORTANT]
> Le contenu de l’espace de noms `contrib` n’est pas entièrement pris en charge. À mesure que les fonctionnalités expérimentales arriveront à échéance, elles seront progressivement déplacées vers l’espace de noms principal.

## <a name="how-to-interpret-your-model"></a>Comment interpréter votre modèle

Vous pouvez appliquer les classes et méthodes d’interprétabilité pour comprendre le comportement global du modèle ou des prédictions spécifiques. La première est appelée une explication globale, et la dernière une explication locale.

Les méthodes peuvent être également classées selon que la méthode est indépendante ou spécifique du modèle. Certaines méthodes ciblent certains types de modèles. Par exemple, l’explicatif d’arborescence de SHAP s’applique uniquement aux modèles basés sur une arborescence. Certaines méthodes traitent le modèle comme une boîte noire, telles que l’explicatif d’imitation ou l’explicatif de noyau de SHAP. Le package `interpret` utilise ces approches différentes en fonction des jeux de données, des types de modèles et des cas d’usage.

La sortie est un ensemble d’informations sur la façon dont un modèle donné élabore sa prédiction, telle que :
* Importance de fonctionnalité relative globale/locale
* Relation de fonctionnalité et prédiction globale/locale

### <a name="explainers"></a>Explicatifs

Ce package utilise les techniques d’interprétabilité développées dans la [Interpret-Community](https://github.com/interpretml/interpret-community/), package Python open source permettant d’entraîner des modèles interprétables et d’expliquer des systèmes IA de boîte noire. [Interpret-Community](https://github.com/interpretml/interpret-community/) fait office d’hôte pour les explicatifs pris en charge de ce SDK et prend actuellement en charge les techniques d’interprétabilité suivantes :

* **Explicatif d’arborescence SHAP** : L’explicatif d’arborescence [SHAP](https://github.com/slundberg/shap) cible l’algorithme d’estimation de valeur SHAP rapide de temps polynomial spécifique des arborescences et ensembles d’arborescences.
* **Explicatif approfondi SHAP** : Selon l’explication proposée par [SHAP](https://github.com/slundberg/shap), l’explicatif approfondi « est un algorithme d’approximation à vitesse élevée de valeurs SHAP dans des modèles de deep learning qui s’appuie sur une connexion avec DeepLIFT décrite dans le [document NIPS de SHAP](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). Les modèles TensorFlow et Keras utilisant le serveur principal TensorFlow sont pris en charge (une prise en charge préliminaire de PyTorch est également possible) ».
* **Explicatif linéaire de SHAP** : L’explicatif linéaire de [SHAP](https://github.com/slundberg/shap) calcule les valeurs SHAP pour un modèle linéaire, en prenant éventuellement en compte les corrélations entre les caractéristiques.

* **Explicatif de noyau SHAP** : L’explicatif de noyau de [SHAP](https://github.com/slundberg/shap) utilise une régression linéaire locale spécialement pondérée pour estimer les valeurs SHAP pour n’importe quel modèle.
* **Explicatif d’imitation** : L’explicatif d’imitation repose sur l’idée d’entraînement de [modèles de substitution globaux](https://christophm.github.io/interpretable-ml-book/global.html) sur des modèles de boîte noire d’imitation. Un modèle de substitution global est un modèle intrinsèquement interprétable qui est formé pour estimer les prédictions d’un modèle de boîte noire aussi précisément que possible. Un scientifique des données peut interpréter le modèle de substitution pour déduire des conclusions sur le modèle de boîte noire. Vous pouvez utiliser un des modèles interprétables suivants comme modèle de substitution : LightGBM (LGBMExplainableModel), régression linéaire (LinearExplainableModel), modèle explicable Stochastic Gradient Descent (SGDExplainableModel) et arbre de décision (DecisionTreeExplainableModel).


* **Explicatif d’importance de fonctionnalité de permutation** : L’importance de fonctionnalité de permutation est une technique utilisée pour expliquer les modèles de classification et de régression qui s’inspirent du [document relatif aux forêts aléatoires de Breiman](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (consultez la section 10). De façon générale, il fonctionne en permutant aléatoirement les données d’une caractéristique à la fois pour l’ensemble du jeu de données et en calculant dans quelle mesure la métrique de performances d’intérêt change. Plus la modification est importante, et plus la fonctionnalité l’est également.

* **Explicatif LIME** (`contrib`) : Selon [LIME](https://github.com/marcotcr/lime), l’explicatif LIME utilise l’algorithme de pointe d’explications indépendant du modèle interprétable local (LIME, Local Interpretable Model-agnostic Explanations), pour créer des modèles de substitution locaux. Contrairement aux modèles de substitution globaux, LIME cible la formation de modèles de substitution locaux pour expliquer des prédictions individuelles.
* **Explicatif de texte HAN** (`contrib`) : L’explicatif de texte HAN utilise un Hierarchical Attention Network pour obtenir des explications sur le modèle à partir de données de texte d’un modèle de texte de boîte noire donné. Il entraîne le modèle de substitution HAN sur la base de sorties prédites d’un modèle de boîte noire donné. Après avoir effectué l’entraînement global sur le corpus de texte, il ajoute une étape de réglage pour un document spécifique afin d’améliorer la précision des explications. HAN utilise un RNN bidirectionnel avec deux couches d’attention, une sur la phrase et l’autre sur le mot. Une fois que le DNN est entraîné sur le modèle de boîte noire et réglé sur un document spécifique, l’utilisateur peut extraire les importances des mots des couches d’attention. HAN se révèle plus précis que LIME ou SHAP pour les données de texte, mais également plus coûteux en termes de temps d’entraînement. Des améliorations ont été apportées pour permettre à l’utilisateur d’initialiser le réseau à l’aide d’incorporations de mots GloVe afin de réduire le temps d’entraînement. Le temps de formation peut être amélioré significativement en exécutant HAN sur une machine virtuelle GPU Azure distante. L’implémentation de HAN est décrite dans le document [« Hierarchical Attention Networks for Document Classification (Yang et al., 2016) »](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


* **Explicatif tabulaire** : `TabularExplainer` utilise la logique suivante pour appeler les explicatifs [SHAP](https://github.com/slundberg/shap) directs :

    1. S’il s’agit d’un modèle basé sur une arborescence, appliquez SHAP `TreeExplainer`, sinon
    2. S’il s’agit d’un modèle DNN, appliquez SHAP `DeepExplainer`, sinon
    3. S’il s’agit d’un modèle linéaire, appliquez SHAP `LinearExplainer`, sinon
    3. Traitez-le comme un modèle de boîte noire et appliquez SHAP `KernelExplainer`


`TabularExplainer` a également apporté des améliorations significatives des caractéristiques et des performances par rapport aux explicatifs SHAP directs :

* **Récapitulatif du jeu de données d’initialisation**. Dans les cas où la vitesse d’explication est plus importante, nous résumons le jeu de données d’initialisation et générons un petit ensemble d’exemples représentatifs, ce qui accélère l’explication globale et locale.
* **Échantillonnage du jeu de données d’évaluation**. Si l’utilisateur transmet un grand ensemble d’exemples d’évaluation, mais que tous ne doivent pas nécessairement être évalués, le paramètre d’échantillonnage peut être défini sur true pour accélérer l’explication globale.

Le diagramme suivant illustre la structure actuelle d’explicatifs directs et méta.

[![Architecture d’interprétabilité de Machine Learning](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Modèles pris en charge

Tous les modèles formés sur des jeux de données au format Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData` ou `scipy.sparse.csr_matrix` sont pris en charge par le package `explain` d’interprétabilité du Kit de développement logiciel (SDK).

Les fonctions d’explication acceptent les modèles et les pipelines en entrée. Si un modèle est fourni, il doit implémenter la fonction de prédiction `predict` ou `predict_proba` conforme à la convention Scikit. Si un pipeline (nom du script de pipeline) est fourni, la fonction d’explication suppose que le script de pipeline en cours d’exécution retourne une prédiction. Nous prenons en charge les modèles formés via les infrastructures de Deep Learning PyTorch, TensorFlow et Keras.

### <a name="local-and-remote-compute-target"></a>Cible de calcul locale et distante

Le package `explain` est conçu pour fonctionner avec les cibles de calcul locales et distantes. Si vous les exécutez localement, les fonctions du Kit de développement logiciel (SDK) ne contactent aucun service Azure. Vous pouvez exécuter l’explication à distance sur la capacité de calcul Azure Machine Learning et consigner les informations d’explication dans des services d’historique d’exécutions d’Azure Machine Learning. Lorsque ces informations sont consignées, les rapports et visualisations de l’explication peuvent être analysés par les utilisateurs sur l’espace de travail Azure Machine Learning.


## <a name="next-steps"></a>Étapes suivantes

Consultez le [guide pratique](service/how-to-machine-learning-interpretability-aml.md) pour bénéficier de l’interprétabilité des modèles entraînés à la fois localement et sur des ressources de calcul distantes Azure Machine Learning. Pour obtenir des scénarios supplémentaires, consultez les [exemples de notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
