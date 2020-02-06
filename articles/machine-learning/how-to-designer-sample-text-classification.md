---
title: 'Concepteur : classifier des exemples de critiques de livres'
titleSuffix: Azure Machine Learning
description: Générez un classifieur par régression logistique multiclasse afin de prédire la catégorie d’entreprise avec le jeu de données wikipedia SP 500 à l’aide du concepteur Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 4d22fd39eae5d5cf207d6d44819f0ce7ab2eceb5
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963239"
---
# <a name="build-a-classifier-to-predict-company-category-using-azure-machine-learning-designer"></a>Générez un classifieur pour prédire la catégorie d’entreprise à l’aide du concepteur Azure Machine Learning.

**Exemple 7 du concepteur (préversion)**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Cet exemple montre comment utiliser des modules d’analytique de texte pour générer un pipeline de classification de texte dans le concepteur Azure Machine Learning (préversion).

L’objectif de la classification de texte est d’affecter un texte à une ou plusieurs classes ou catégories prédéfinies. Il peut s’agir d’un document, d’un article de presse, d’une requête de recherche, d’un e-mail, d’un tweet, de tickets de support, d’un commentaire de client, d’une évaluation de produit par utilisateur, etc. Les applications de classification de texte incluent la catégorisation d’articles de presse et de contenus de fils d’actualités en rubriques, en organisant les pages web en catégories hiérarchiques, en filtrant le courrier indésirable, en analysant les sentiments, en prédisant l’intention des utilisateurs à partir de requêtes de recherche, en routant des tickets de support et analysant les commentaires des clients. 

Ce pipeline effectue l’apprentissage d’un **classifieur par régression logistique multiclasse** afin de prédire la catégorie d’entreprise avec **le jeu de données wikipedia SP 500 dérivé de Wikipedia**.  

Les étapes fondamentales de la formation d’un modèle Machine Learning sont les suivantes :

1. Obtenir les données

1. Prétraitement des données de texte

1. Ingénierie des caractéristiques

   Convertissez la fonctionnalité textuelle en fonctionnalité numérique avec un module d’extraction de fonctionnalités tel que le module Hachage des caractéristiques, puis extrayez la caractéristique N-grammes des données de texte.

1. Effectuer l’apprentissage du modèle

1. Noter le Jeu de données

1. Évaluer le modèle

Voici le graphe complet final du pipeline sur lequel nous allons travailler. Nous vous fournirons le raisonnement pour tous les modules afin de prendre des décisions similaires concernant le vôtre.

[![Graphique du pipeline](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png)](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>Données

Dans ce pipeline, nous utilisons le jeu de données **Wikipédia SP 500**. Le jeu de données est dérivé de Wikipédia (https://www.wikipedia.org/) basé sur des articles de chaque entreprise cotée au S&P 500. Avant son chargement dans le concepteur Azure Machine Learning, le jeu de données a été traité comme suit :

- Extraction du contenu textuel de chaque société particulière
- Suppression de la mise en forme wiki
- Suppression des caractères non alphanumériques
- Conversion de tout le texte en minuscule
- Ajout d'autres catégories de sociétés connues

Aucun article n’ayant pu être trouvé pour certaines sociétés, le nombre d’enregistrements est inférieur à 500.

## <a name="pre-process-the-text-data"></a>Prétraitement des données de texte

Nous utilisons le module **Prétraiter le texte** pour prétraiter les données de texte, ce qui inclut la détection des phrases, leur segmentation, et ainsi de suite. Toutes les options prises en charge sont décrites dans l’article [**Prétraiter le texte**](algorithm-module-reference/preprocess-text.md). Une fois le prétraitement des données de texte effectué, nous utilisons le module **Fractionner les données** pour diviser de façon aléatoire les données d’entrée, afin que le jeu de données d’apprentissage contienne 50 % des données d’origine et que le jeu de données de test contienne 50 % des données d’origine.

## <a name="feature-engineering"></a>Ingénierie des caractéristiques
Dans cet exemple, nous allons utiliser deux méthodes effectuant l’ingénierie des caractéristiques.

### <a name="feature-hashing"></a>Hachage des caractéristiques
Nous avons utilisé le module [**Hachage des caractéristiques**](algorithm-module-reference/feature-hashing.md) pour convertir le texte brut des articles en entiers, puis utilisé les valeurs d’entiers comme caractéristiques d’entrée pour le modèle. 

Le module **Hachage des caractéristiques** permet de convertir des documents texte de longueur variable en vecteurs de caractéristiques numériques de longueur égale en utilisant la méthode de hachage 32 bits murmurhash v3 fournie par la bibliothèque Vowpal Wabbit. L’objectif de l’utilisation du hachage des caractéristiques est de réduire la dimensionnalité. Par ailleurs, le hachage des caractéristiques accélère la recherche des pondérations de caractéristiques au moment de la classification, parce qu’il utilise une comparaison de valeurs plutôt qu’une comparaison de chaînes.

Dans l’exemple de pipeline, nous avons défini le nombre de bits de hachage sur 14 et le nombre de N-grammes sur 2. Avec ces paramètres, la table de hachage peut contenir 2^14 entrées dans lesquelles chaque fonctionnalité de hachage représente une ou plusieurs caractéristiques de N-grammes, et sa valeur représente la fréquence d’occurrence de ce N-grammes dans l’instance de texte. Pour de nombreux problèmes, une table de hachage de cette taille est plus que suffisante mais, dans certains cas, un supplément d’espace peut être nécessaire pour éviter les collisions. Évaluez les performances de votre solution d’apprentissage automatique en utilisant un nombre de bits différent. 

### <a name="extract-n-gram-feature-from-text"></a>Extraire la caractéristique N-grammes du texte

Une valeur N-grammes est une séquence contiguë de n termes extraits d’une séquence de texte donnée. Un N-grammes de taille 1 est appelé unigramme, un N-grammes de taille 2 bigramme et un N-grammes de taille 3 trigramme. Les N-grammes de plus grandes tailles sont parfois référencés par la valeur de N, par exemple, « Quatre-Grammes », « Cinq-Grames », et ainsi de suite.

Nous avons utilisé le module [**Extraire la caractéristique N-grammes du texte**](algorithm-module-reference/extract-n-gram-features-from-text.md) comme solution alternative pour l’ingénierie des caractéristiques. Ce module extrait d’abord le jeu de N-grammes et en plus des N-grammes, le nombre de documents où chaque N-grammes apparaissant dans le texte est compté (DF). Dans cet exemple, la métrique TF-IDF est utilisée pour calculer les valeurs des caractéristiques. Ensuite, elle convertit les données de texte non structurées en vecteurs de caractéristiques numériques de longueur égale, chaque caractéristique représentant la métrique TF-IDF d’un N-gramme dans une instance de texte.

Après conversion des données de texte en vecteurs de caractéristiques numériques, un module **Sélectionner la colonne** est utilisé pour supprimer les données de texte du jeu de données. 

## <a name="train-the-model"></a>Effectuer l’apprentissage du modèle

Votre choix de l’algorithme dépend souvent de la configuration requise pour le cas d’usage. Étant donné que l’objectif de ce pipeline est de prédire la catégorie d’entreprise, un modèle de classifieur multiclasse constitue un bon choix. Compte tenu du fait que le nombre de caractéristiques est important et que celles-ci sont éparses, nous utilisons un modèle de **régression logistique multiclasse** pour ce pipeline.

## <a name="test-evaluate-and-compare"></a>Test, évaluation et comparaison

 Nous fractionnons le jeu de données et utilisons différents jeux de données pour former et tester le modèle, afin d’évaluer le modèle de manière plus objective.

Une fois le modèle formé, nous utilisons les modules **Noter le modèle** et **Évaluer le modèle** pour générer des résultats de prédiction et évaluer les modèles. Toutefois, avant d’utiliser le module **Noter le modèle**, il est indispensable d’effectuer l’ingénierie comme nous l’avons fait durant la formation. 

Pour le module **Hachage des caractéristiques**, il est facile d’effectuer une ingénierie des caractéristiques sur le flux de notation en tant que flux d’apprentissage. Pour traiter les données de texte d’entrée, utilisez directement le module **Hachage des caractéristiques**.

Pour le module **Extraire la caractéristique N-grammes du texte**, nous devons connecter la sortie **Result Vocabulary** (Vocabulaire de résultat) du flux de données d’apprentissage à **Input Vocabulary** (Vocabulaire d’entrée) sur le flux de données de notation, puis définir le paramètre **Vocabulary mode** (Mode vocabulaire) sur **ReadOnly** (Lecture seule).
[![Graphique de note n-Gram](./media/how-to-designer-sample-text-classification/n-gram.png)](./media/how-to-designer-sample-text-classification/n-gram.png)

Une fois l’étape d’ingénierie terminée, le module **Noter un modèle** peut être utilisé pour générer des prédictions pour le jeu de données de test en utilisant le modèle formé. Pour vérifier le résultat, sélectionnez le port de sortie du module **Noter le modèle**, puis cliquez sur **Visualiser**.

Nous transmettons ensuite les notes au module **Évaluer le modèle**, afin de générer des métriques d’évaluation. Le module **Évaluer le modèle** ayant deux ports d’entrée, nous pourrions évaluer et comparer des jeux de données notés qui sont générés avec différentes méthodes. Dans cet exemple, nous comparons les performances du résultat généré avec la méthode de hachage des caractéristiques et la méthode N-grammes.
Pour vérifier le résultat, sélectionnez le port de sortie du module **Noter le modèle**, puis cliquez sur **Visualiser**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les autres échantillons disponibles pour le concepteur :
- [Exemple 1 - Régression : Prédire le prix de véhicules automobiles](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exemple 2 - Régression : comparer des algorithmes pour prédire le prix de véhicules automobiles](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exemple 3 - Classification avec sélection des caractéristiques : Prédiction des revenus](how-to-designer-sample-classification-predict-income.md)
- [Exemple 4 - Classification : prédire le risque de crédit (sensible au coût)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exemple 5 - Classification : Prédire l’évolution](how-to-designer-sample-classification-churn.md)
- [Exemple 6 - Classification : Prédire les retards de vols](how-to-designer-sample-classification-flight-delay.md)
