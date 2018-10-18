---
title: Classification des sentiments Twitter avec le package Azure Machine Learning pour l’analyse de texte et le processus TDSP (Team Data Science Process) | Microsoft Docs
description: Décrit l’utilisation du processus TDSP (Team Data Science Process) et du package Azure Machine Learning pour l’analyse de texte afin de classer des sentiments
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: deguhath
ms.openlocfilehash: acad053712d237fdb86934faa7c5c4546c630387
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394556"
---
# <a name="twitter-sentiment-classification-with-azure-machine-learning-aml-package-for-text-analytics-amlpta-and-team-data-science-process-tdsp"></a>Classification des sentiments Twitter avec le package Azure Machine Learning pour l’analyse de texte et le processus TDSP (Team Data Science Process)

## <a name="introduction"></a>Introduction
La normalisation de la structure et de la documentation des projets de science des données, qui est ancrée à un [cycle de vie de la science des données](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md) établi, est primordiale pour favoriser une collaboration efficace au sein des équipes de science des données.

Nous avions précédemment publié un [dépôt GitHub pour la structure et les modèles du projet TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Nous permettons à présent la création de projets Azure Machine Learning qui sont instanciés avec [la structure et les modèles de documentation TDSP pour Azure Machine Learning](https://github.com/amlsamples/tdsp). Vous trouverez des instructions sur l’utilisation de la structure et des modèles TDSP dans Azure Machine Learning [ici](https://docs.microsoft.com/azure/machine-learning/preview/how-to-use-tdsp-in-azure-ml). 

Dans cet exemple, nous allons présenter l’utilisation du package Azure Machine Learning pour l’analyse de texte et du processus TDSP pour développer et déployer des modèles prédictifs pour la classification des sentiments Twitter.


## <a name="use-case"></a>Cas d’utilisation
### <a name="twitter-sentiment-polarity-sample"></a>Exemple de polarité de sentiment Twitter
Cet article utilise un exemple pour illustrer comment instancier et exécuter un projet Machine Learning. Cet exemple utilise la structure et les modèles TDSP dans Azure Machine Learning Workbench. L’exemple complet est fourni dans cette procédure pas-à-pas. La tâche de modélisation prédit la polarité de sentiment (positive ou négative) en utilisant le texte des tweets. Cet article présente les tâches de modélisation des données décrites dans la procédure pas à pas. La procédure pas à pas couvre les tâches suivantes :

1. Exploration de données, apprentissage et déploiement d’un modèle d’apprentissage automatique solutionnant le problème de prédiction décrit dans la vue d’ensemble du cas d’usage. Les données de sentiment Twitter sont utilisées pour ces tâches.
2. Exécution du projet à l’aide du modèle TDSP à partir d’Azure Machine Learning pour ce projet. Pour l’exécution du projet et la génération de rapports, le cycle de vie TDSP est utilisé.
3. Opérationnalisation de la solution directement à partir d’Azure Machine Learning dans Azure Container Service.

Le projet met en évidence l’utilisation du package d’analyse de texte pour Azure Machine Learning.


## <a name="link-to-github-repository"></a>Lien vers le dépôt GitHub
Le lien vers le référentiel GitHub se trouve [ici](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction). 

### <a name="purpose"></a>Objectif
L’objectif principal de cet exemple est de montrer comment instancier et exécuter un projet Machine Learning à l’aide de la structure et des modèles TDSP dans Azure Machine Learning Work Bench. Pour cela, nous utilisons [les données de sentiment Twitter](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip). La tâche de modélisation prédit la polarité du sentiment (positive ou négative) en utilisant le texte des tweets.

### <a name="scope"></a>Étendue
- Exploration de données, apprentissage et déploiement d’un modèle Machine Learning solutionnant le problème de prédiction décrit dans la vue d’ensemble du cas d’usage.
- Exécution du projet dans Azure Machine Learning à l’aide du modèle Team Data Science Process (TDSP) dans Azure Machine Learning pour ce projet. Pour l’exécution du projet et la génération des rapports correspondants, nous allons utiliser le cycle de vie TDSP.
- Opérationnalisation de la solution directement à partir d’Azure Machine Learning dans Azure Container Service.

Le projet met en avant plusieurs fonctionnalités d’Azure Machine Learning, telles que l’instanciation et l’utilisation de la structure TDSP, l’exécution de code dans Azure Machine Learning Work Bench et une opérationnalisation simple dans Azure Container Service à l’aide de Docker et Kubernetes.

## <a name="team-data-science-process-tds"></a>TDSP
Nous utilisons les modèles de documentation et la structure de projet TDSP pour exécuter cet exemple. Il suit le [cycle de vie TDSP](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle). Le projet est créé conformément aux instructions fournies [ici](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md).


<img src="./media/predict-twitter-sentiment-amltextpackage/tdsp-lifecycle2.png" alt="tdsp-lifecycle" width="800" height="600">


## <a name="use-case-overview"></a>Vue d’ensemble d’un cas d’usage
La tâche consiste à prédire la polarité binaire des sentiments Twitter à l’aide des caractéristiques des plongements de mots extraites du texte de Twitter. Pour une description détaillée, reportez-vous à ce [référentiel](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction).

### <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Acquisition et compréhension des données](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
La première étape de cet exemple consiste à télécharger le jeu de données sentiment140 et à le diviser en jeux de données d’apprentissage et de test. Le jeu de données sentiment140 comprend le contenu réel du tweet (sans les émojis) avec la polarité de chacun des tweets (négatif = 0, positif = 4). Les tweets neutres ont été supprimés. Les données d’apprentissage obtenues comptent 1,3 million de lignes, tandis que les données de test obtenues comptent 320k lignes.

### <a name="modelinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode02modeling"></a>[Modélisation](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/02_modeling)
Cette partie de l’exemple est ensuite divisée en trois sous-parties : 
- **Ingénierie des fonctionnalités**, qui correspond à la génération de fonctionnalités à l’aide de différents algorithmes de plongement de mots, à savoir Word2Vec. 
- **Création de modèles**, qui s’occupe de l’apprentissage des différents modèles comme la _régression logistique_ et le _gradient boosting_ pour prédire les sentiments dans le texte d’entrée. 
- **Évaluation de modèles**, qui applique le modèle formé aux données de test.

#### <a name="feature-engineering"></a>Ingénierie des caractéristiques
Nous utilisons <b>Word2Vec</b> pour générer des plongements de mots. Nous commençons par utiliser l’algorithme Word2Vec dans le mode Skip-Gram, comme expliqué dans le document de [Mikolov, Tomas, et al., intitulé Distributed representations of words and phrases and their compositionality. Advances in neural information processing systems. (Représentations distribuées des mots et des phrases dans leur compositionnalité. Avances dans le domaine des systèmes de traitement d’informations neuronales) 2013.](https://arxiv.org/abs/1310.4546) pour générer des plongements de mots.

Skip-Gram est un réseau neuronal superficiel qui prend le mot cible encodé sous forme d’un vecteur chaud en tant qu’entrée et qui l’utilise pour prédire des mots à proximité. Si V est la taille du vocabulaire, alors la taille de la couche de sortie est __C*V__, où C correspond à la taille de la fenêtre de contexte. L’architecture basée sur Skip-Gram est illustrée dans la figure suivante.
 
<table class="image" align="center">
<caption align="bottom">Modèle Skip-Gram</caption>
<tr><td><img src="https://s3-ap-south-1.amazonaws.com/av-blog-media/wp-content/uploads/2017/06/05000515/Capture2-276x300.png" alt="Skip-gram model"/></td></tr>
</table>

Discuter des détails du modèle Skip-Gram et de l’algorithme Word2Vec ne s’inscrit pas dans le champ d’application de cet exemple. Les personnes intéressées sont donc invitées à consulter les liens suivants pour obtenir plus d’informations. Le code 02_A_Word2Vec.py est référencé dans les [exemples tensorflow](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py).

* [Vector Representations of Words](https://www.tensorflow.org/tutorials/word2vec) (Représentations vectorielles des mots)
* [How exactly does word2vec work? (Comment word2vec fonctionne-t-il exactement ?)](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
* [Notes on Noise Contrastive Estimation and Negative Sampling (Remarques sur l’estimation contrastive du bruit et l’échantillonnage négatif)](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)

Une fois le processus d’apprentissage terminé, deux fichiers de plongement au format TSV sont générés pour la phase de modélisation.

#### <a name="model-training"></a>Apprentissage du modèle
Une fois les vecteurs de mots générés à l’aide de l’algorithme SSWE ou Word2vec, l’étape suivante consiste à effectuer l’apprentissage des modèles de classification pour prédire la polarité du sentiment réel. Nous appliquons les deux types de fonctionnalités Word2Vec et SSWE dans deux modèles : Régression logistique et Réseaux neuronaux convolutifs. 

#### <a name="model-evaluation"></a>Évaluation de modèle
Nous fournissons le code pour charger et évaluer plusieurs modèles formés sur le jeu de données de test.


### <a name="deploymenthttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode03deployment"></a>[Déploiement](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/03_deployment)
Dans cette partie, nous fournissons des liens vers des instructions permettant d’opérationnaliser un modèle de prédiction de sentiment préformé vers un service web sur un cluster dans Azure Container Service (AKS). L’environnement d’opérationnalisation configure Docker et Kubernetes dans le cluster pour gérer le déploiement du service web. Vous trouverez plus d’informations sur le processus d’opérationnalisation [ici](https://docs.microsoft.com/azure/machine-learning/preview/model-management-service-deploy).

## <a name="conclusion"></a>Conclusion
Nous avons étudié l’apprentissage d’un modèle de plongement de mots à l’aide de Word2Vec, puis utilisé les plongements extraits en tant que caractéristiques pour effectuer l’apprentissage de deux modèles différents afin de prédire le score de sentiment des données texte de Twitter. L’un de ces modèles est déployé dans Azure Container Service (AKS). 

## <a name="next-steps"></a>Étapes suivantes
Lisez plus de documents sur le [package Azure Machine Learning pour l’analyse de texte](https://docs.microsoft.com/python/api/overview/azure-machine-learning/textanalytics?view=azure-ml-py-latest) et le [processus TDSP (Team Data Science Process)](https://aka.ms/tdsp) pour commencer.

## <a name="references"></a>Références
* [Processus Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Guide pratique pour utiliser Team Data Science Process (TDSP) dans Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)
* [Modèle de projet TDSP pour Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)
* [Documentation des services Microsoft Azure Machine Learning (préversion)](https://docs.microsoft.com/azure/machine-learning/preview/)
* [Mikolov, Tomas, et al. Distributed representations of words and phrases and their compositionality. Advances in neural information processing systems. (Représentations distribuées des mots et des phrases dans leur compositionnalité. Avances dans le domaine des systèmes de traitement d’informations neuronales) 2013.](https://arxiv.org/abs/1310.4546)
