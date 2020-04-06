---
title: Allocation de Dirichlet latente
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Allocation de Dirichlet latente pour regrouper du texte non classifié en plusieurs catégories.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/11/2020
ms.openlocfilehash: 1384491489c175ffc338f80a99aa8d5050f835d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80089069"
---
# <a name="latent-dirichlet-allocation"></a>Allocation de Dirichlet latente

Cet article explique comment utiliser le module **Allocation de Dirichlet latente** dans le Concepteur Azure Machine Learning (préversion) pour regrouper du texte non classifié en plusieurs catégories. 

L’allocation de Dirichlet latente (LDA, Latent Dirichlet Allocation) est souvent utilisée dans le traitement en langage naturel pour rechercher des textes similaires. *Modélisation de rubrique* est un autre terme courant.

Ce module prend une colonne de texte et génère les sorties suivantes :

+ Le texte source, avec un score pour chaque catégorie

+ Une matrice de caractéristiques, contenant les termes extraits et les coefficients pour chaque catégorie

+ Une transformation, que vous pouvez enregistrer et réappliquer au nouveau texte utilisé comme entrée

Ce module utilise la bibliothèque scikit-learn. Pour plus d’informations sur scikit-learn, consultez le dépôt GitHub, qui comprend des tutoriels et une explication de l’algorithme.

### <a name="more-about-latent-dirichlet-allocation-lda"></a>En découvrir plus sur l’Allocation de Dirichlet latente (LDA)

En règle générale, LDA n’est pas une méthode de classification à proprement parler, mais utilise une approche génératrice. Cela signifie que vous n’avez pas besoin de fournir des étiquettes de classe connues, puis de déduire les modèles.  Au lieu de cela, l’algorithme génère un modèle probabiliste qui est utilisé pour identifier des groupes de rubriques. Vous pouvez utiliser le modèle probabiliste pour classifier les cas d’apprentissage existants ou les nouveaux cas que vous fournissez au modèle comme entrée.

Un modèle générateur peut être préférable, car il évite de faire des hypothèses importantes sur la relation entre le texte et les catégories et utilise uniquement la distribution de mots pour modéliser les rubriques de façon mathématique.

+ La théorie est abordée dans ce document, téléchargeable au format PDF : [Allocation de Dirichlet latente : Blei, Ng et Jordan](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf)

+ L’implémentation de ce module est basée sur la [bibliothèque scikit-learn](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) pour LDA.

Pour plus d’informations, consultez la section [Notes techniques](#technical-notes).

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Configuration de l’allocation de Dirichlet latente

Ce module requiert un jeu de données qui contient une colonne de texte, à l’état brut ou prétraité.

1. Ajoutez le module **Allocation de Dirichlet latente** à votre pipeline.

2. Comme entrée du module, fournissez un jeu de données contenant une ou plusieurs colonnes de texte.

3. Pour **Target columns**, choisissez une ou plusieurs colonnes contenant du texte à analyser.

    Vous pouvez choisir plusieurs colonnes, mais elles doivent être du type de données chaîne.

    En général, étant donné que LDA crée une grande matrice de caractéristiques à partir du texte, vous analysez une seule colonne de texte.

4. Pour **Number of topics to model**, tapez un entier compris entre 1 et 1000 qui indique le nombre de catégories ou de rubriques que vous souhaitez dériver du texte d’entrée.

    Par défaut, 5 rubriques sont créées.

5. Pour **N-grams**, spécifiez la longueur maximale de N-grammes générés lors du hachage.

    La valeur par défaut est 2, ce qui signifie qu’à la fois des bigrammes et des unigrammes sont générés.

6. Sélectionnez l’option **Normalize** pour convertir les valeurs de sortie en probabilités. Ainsi, à la place d’une représentation des valeurs transformées sous forme d’entiers, les valeurs contenues dans le jeu de données de la sortie et des caractéristiques sont transformées comme suit :

    + Les valeurs du jeu de données sont représentées en tant que probabilité où `P(topic|document)`.

    + Les valeurs de la matrice de rubriques de caractéristiques sont représentées en tant que probabilité où `P(word|topic)`.

    > [!NOTE] 
    > Dans le Concepteur Azure Machine Learning (préversion), étant donné que la bibliothèque que nous avions définie comme base, scikit-learn, ne prend plus en charge la sortie non normalisée *doc_topic_distr* depuis la version 0.19, dans ce module, le paramètre **Normalize** ne peut être appliqué qu’à la sortie **Feature Topic matrix**, la sortie **Transformed dataset** étant toujours normalisée.

7. Sélectionnez l’option **Show all options**, puis affectez-lui la valeur TRUE si vous souhaitez afficher, puis définir des paramètres avancés supplémentaires.

    Ces paramètres sont propres à l’implémentation scikit-learn de LDA. Vous trouverez de bons tutoriels sur LDA dans scikit-learn ainsi que dans le [document scikit-learn](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html) officiel.

    + **Rho parameter**. Fournissez une probabilité antérieure pour la densité des distributions de rubriques. Correspond au paramètre `topic_word_prior` de sklearn. Vous pouvez utiliser la valeur 1 si vous vous attendez à ce que la distribution des mots soit plate ; autrement dit, tous les mots sont supposés équiprobables. Si vous pensez que la plupart des mots apparaissent peu, vous pouvez utiliser une valeur largement inférieure.

    + **Alpha parameter**. Spécifiez une probabilité antérieure pour la densité des pondérations de rubrique par document.  Correspond au paramètre `doc_topic_prior` de sklearn.

    + **Estimated number of documents**. Tapez un nombre qui représente la meilleure estimation du nombre de documents (lignes) qui seront traités. Le module peut ainsi allouer une table de hachage d’une taille suffisante.  Correspond au paramètre `total_samples` dans scikit-learn.

    + **Size of the batch**. Tapez un nombre qui indique le nombre de lignes à inclure dans chaque lot de texte envoyé au modèle LDA. Correspond au paramètre `batch_size` dans scikit-learn.

    + **Initial value of iteration used in learning update schedule**. Spécifiez la valeur de départ qui pondère le taux d’apprentissage pour les premières itérations dans l’apprentissage en ligne. Correspond au paramètre `learning_offset` dans scikit-learn.

    + **Power applied to the iteration during updates**. Indiquez le niveau de puissance appliquée au nombre d’itérations afin de contrôler le taux d’apprentissage au cours des mises à jour en ligne. Correspond au paramètre `learning_decay` dans scikit-learn.

    + **Number of passes over the data**. Spécifiez le nombre maximal de fois que l’algorithme doit parcourir les données. Correspond au paramètre `max_iter` dans scikit-learn.

8. Sélectionnez l’option **Build dictionary of ngrams** ou **Build dictionary of ngrams prior to LDA**, si vous souhaitez créer la liste de N-grammes dans un passage initial, avant de classifier le texte.

    Si vous créez le dictionnaire initial au préalable, vous pouvez l’utiliser plus tard quand vous examinez le modèle. La possibilité de mapper les résultats sur du texte plutôt que sur des index numériques facilite généralement l’interprétation. Toutefois, l’enregistrement du dictionnaire prend plus de temps et utilise du stockage supplémentaire.

9. Pour **Maximum size of ngram dictionary**, tapez le nombre total de lignes qui peuvent être créées dans le dictionnaire de N-grammes.

    Cette option est utile pour contrôler la taille du dictionnaire. Toutefois, si le nombre de N-grammes dans l’entrée dépasse cette taille, des collisions peuvent se produire.

10. Envoyez le pipeline. Le module LDA utilise le théorème de Bayes pour déterminer les rubriques qui peuvent être associées à des mots individuels. Les mots ne sont pas exclusivement associés à des rubriques ou groupes ; au lieu de cela, chaque N-gramme a une probabilité apprise d’être associé à l’une des classes découvertes.

## <a name="results"></a>Résultats

Le module possède deux sorties :

+ **Transformed dataset** : contient le texte d’entrée et un nombre spécifié de catégories découvertes ainsi que les scores pour chaque exemple de texte pour chaque catégorie.

+ **Feature topic matrix** : la colonne la plus à gauche contient la caractéristique de texte extraite, et il existe une colonne pour chaque catégorie contenant le score de cette caractéristique dans cette catégorie.


### <a name="lda-transformation"></a>Transformation LDA

Ce module génère également la *transformation LDA* qui applique LDA au jeu de données.

Vous pouvez enregistrer cette transformation en inscrivant le jeu de données sous l’onglet **Sorties+journaux** dans le volet droit du module et le réutiliser pour d’autres jeux de données. Cela peut être utile si vous avez entraîné un grand corpus et que vous souhaitez réutiliser les coefficients ou les catégories.

### <a name="refining-an-lda-model-or-results"></a>Affinage d’un modèle ou des résultats LDA

En général, vous ne pouvez pas créer un modèle LDA unique qui répond à tous les besoins, et même un modèle conçu pour une tâche peut nécessiter de nombreuses itérations pour améliorer la justesse. Nous vous recommandons d’essayer toutes ces méthodes pour améliorer votre modèle :

+ Changer les paramètres du modèle
+ Utiliser la visualisation pour comprendre les résultats
+ Recueillir les commentaires d’experts en rubriques pour déterminer si les rubriques générées sont utiles.

Les mesures qualitatives peuvent également être utiles pour évaluer les résultats. Pour évaluer les résultats de la modélisation des rubriques, prenez en compte les aspects suivants :

+ Justesse : des éléments similaires sont-ils vraiment similaires ?
+ Diversité : le modèle peut-il distinguer les éléments similaires quand cela est nécessaire pour le problème métier ?
+ Scalabilité : cela fonctionne-t-il sur un large éventail de catégories de texte ou uniquement sur un domaine cible étroit ?

La justesse des modèles basés sur LDA peut souvent être améliorée à l’aide du traitement en langage naturel pour nettoyer, synthétiser et simplifier ou catégoriser le texte. Par exemple, les techniques suivantes, toutes prises en charge dans Azure Machine Learning, peuvent améliorer la justesse de la classification :

+ Arrêter la suppression de mots

+ Normalisation de casse

+ Lemmatisation ou recherche de radical

+ Reconnaissance d’entité nommée

Pour plus d’informations, consultez [Pré-traiter le texte](preprocess-text.md).

Dans le concepteur, vous pouvez également utiliser des bibliothèques R ou Python pour le traitement du texte : [exécuter un script R](execute-r-script.md), [exécuter un script Python](execute-python-script.md)



## <a name="technical-notes"></a>Notes techniques

Cette section contient des détails, des conseils et des réponses aux questions fréquentes concernant l’implémentation.

### <a name="implementation-details"></a>Informations d’implémentation

Par défaut, les distributions des sorties pour le jeu de données transformé et la matrice de rubriques de caractéristiques sont normalisées en tant que probabilités.

+ Le jeu de données transformé est normalisé en tant que probabilité conditionnelle des rubriques en fonction d’un document. Dans ce cas, la somme de chaque ligne est égale à 1.

+ La matrice de rubriques de caractéristiques est normalisée en tant que probabilité conditionnelle de mots en fonction d’une rubrique. Dans ce cas, la somme de chaque colonne est égale à 1.

> [!TIP]
> Parfois, le module peut retourner une rubrique vide, ce qui est le plus souvent dû à l’initialisation pseudo-aléatoire de l’algorithme.  Dans ce cas, vous pouvez essayer de changer les paramètres associés, tels que la taille maximale du dictionnaire de N-grammes ou le nombre de bits à utiliser pour le hachage des caractéristiques.

### <a name="lda-and-topic-modeling"></a>LDA et modélisation des rubriques

L’allocation de Dirichlet latente (LDA) est souvent utilisée pour la *modélisation de rubriques basée sur le contenu*, qui revient à apprendre des catégories à partir d’un texte non classifié. Dans la modélisation de rubriques basée sur le contenu, une rubrique est une distribution sur des mots.

Supposons, par exemple, que vous ayez fourni un corpus de révisions de clients incluant un grand nombre de produits. Le texte des révisions soumises par de nombreux clients au fil du temps contient de nombreux termes, dont certains sont utilisés dans plusieurs rubriques.

Une **rubrique** qui est identifiée par le processus LDA peut représenter des révisions pour un produit individuel A ou un groupe de révisions de produits. Pour LDA, la rubrique elle-même est simplement une distribution de probabilité dans le temps pour un ensemble de mots.

Les termes sont rarement exclusifs à un produit, mais peuvent faire référence à d’autres produits ou être des termes généraux qui s’appliquent à tout (« parfait », « horrible »). D’autres termes peuvent être des mots parasites.  Toutefois, il est important de comprendre que la méthode LDA ne prétend pas capturer tous les mots de l’univers ou comprendre comment les mots sont liés, à part les probabilités de cooccurrence. Elle peut uniquement regrouper des mots qui ont été utilisés dans le domaine cible.

Une fois que les index de terme ont été calculés, les lignes de texte individuelles sont comparées à l’aide d’une mesure de similarité basée sur la distance pour déterminer si deux portions de texte sont similaires.  Par exemple, vous pouvez constater que le produit a plusieurs noms qui sont fortement corrélés. Vous pouvez également constater que des termes fortement négatifs sont généralement associés à un produit particulier. Vous pouvez utiliser la mesure de similarité pour identifier des termes associés et créer des recommandations.

###  <a name="module-parameters"></a>Paramètres du module

|Nom|Type|Plage|Facultatif|Default|Description|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Target column(s)|Sélection de colonnes||Obligatoire|StringFeature|Nom ou index de la colonne cible|  
|Number of topics to model|Integer|[1;1000]|Obligatoire|5|Modéliser la distribution de documents sur N rubriques|  
|N-grams|Integer|[1;10]|Obligatoire|2|Ordre de N-grammes généré au cours du hachage|  
|Normalize|Boolean|True ou False|Obligatoire|true|Normaliser la sortie vers les probabilités.  Le jeu de données transformé est P(rubrique&#124;document) tandis que la matrice de rubriques de caractéristiques est P(mot&#124;rubrique)|  
|Show all options|Boolean|True ou False|Obligatoire|False|Présente des paramètres supplémentaires propres à l’allocation de Dirichlet latente en ligne pour scikit-learn|  
|Rho parameter|Float|[0.00001;1.0]|S’applique quand la case **Show all options** est activée|0.01|Distribution antérieure des mots de rubrique|  
|Alpha parameter|Float|[0.00001;1.0]|S’applique quand la case **Show all options** est activée|0.01|Distribution antérieure des rubriques de document|  
|Estimated number of documents|Integer|[1;int.MaxValue]|S’applique quand la case **Show all options** est activée|1 000|Estimation du nombre de documents (correspond au paramètre total_samples)|  
|Size of the batch|Integer|[1;1024]|S’applique quand la case **Show all options** est activée|32|Taille du lot|  
|Initial value of iteration used in learning rate update schedule|Integer|[0;int.MaxValue]|S’applique quand la case **Show all options** est activée|0|Valeur initiale qui pondère le taux d’apprentissage pour les premières itérations. Correspond au paramètre learning_offset|  
|Power applied to the iteration during updates|Float|[0.0;1.0]|S’applique quand la case **Show all options** est activée|0.5|Puissance appliquée au nombre d’itérations pour contrôler le taux d’apprentissage. Correspond au paramètre learning_decay |  
|Number of training iterations|Integer|[1;1024]|S’applique quand la case **Show all options** est activée|25|Nombre d’itérations d’entraînement|  
|Build dictionary of ngrams|Boolean|True ou False|S’applique quand la case **Show all options** n’est *pas* activée|True|Crée un dictionnaire de N-grammes avant le calcul de LDA. Utile pour l’inspection et l’interprétation des modèles|  
|Maximum size of ngram dictionary|Integer|[1;int.MaxValue]|S’applique quand l’option **Build dictionary of ngrams** a la valeur True|20000|Taille maximale du dictionnaire de N-grammes. Si le nombre de jetons dans l’entrée dépasse cette taille, des collisions peuvent se produire|  
|Number of bits to use for feature hashing|Integer|[1;31]|S’applique quand la case **Show all options** n’est *pas* cochée et que **Build dictionary of ngrams** a pour valeur False|12|Nombre de bits à utiliser pour le hachage des caractéristiques| 
|Build dictionary of ngrams prior to LDA|Boolean|True ou False|S’applique quand la case **Show all options** est activée|True|Crée un dictionnaire de N-grammes avant LDA. Utile pour l’inspection et l’interprétation des modèles|  
|Maximum number of ngrams in dictionary|Integer|[1;int.MaxValue]|S’applique quand la case **Show all options** est cochée et que l’option **Build dictionary of ngrams** a pour valeur True|20000|Taille maximale du dictionnaire. Si le nombre de jetons dans l’entrée dépasse cette taille, des collisions peuvent se produire|  
|Number of hash bits|Integer|[1;31]|S’applique quand la case **Show all options** est cochée et que l’option **Build dictionary of ngrams** a pour valeur False|12|Nombre de bits à utiliser durant le hachage des caractéristiques|   


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning.   
Pour obtenir la liste des erreurs propres aux modules, consultez [Exceptions et codes d’erreur pour le concepteur](designer-error-codes.md).
