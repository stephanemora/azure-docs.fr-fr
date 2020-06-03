---
title: Vectoriser du texte
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser trois modèles Word2Vec fournis pour extraire un vocabulaire et les imbrications de mots correspondantes d’un corpus de texte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: e0e796b75690bcacc6be8ef29b8b490c7faa40af
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853651"
---
# <a name="convert-word-to-vector"></a>Vectoriser du texte

Cet article explique comment utiliser le module **Vectoriser du texte** dans le Concepteur Azure Machine Learning (préversion), pour appliquer différents modèles Word2Vec (Word2Vec, FastText, Glove préentraîné) au corpus de texte que vous avez spécifié comme entrée, et générer un vocabulaire avec des imbrications de mots.

Ce module utilise la bibliothèque Gensim. Pour plus d’informations sur Gensim, consultez son [site web officiel](https://radimrehurek.com/gensim/apiref.html) qui comprend des didacticiels et des explications sur les algorithmes.

### <a name="more-about-convert-word-to-vector"></a>En savoir plus sur le module Vectoriser du texte

En général, la vectorisation de texte est un processus de traitement du langage naturel qui utilise des modèles ou techniques linguistiques pour mapper des mots à un espace vectoriel, autrement dit, pour représenter chaque mot par un vecteur de nombres réels. Par ailleurs, elle permet que des mots ayant une signification similaire aient des représentations similaires.

Vous pouvez utiliser des imbrications de mots comme entrées initiales pour accomplir en aval des tâches de traitement du langage naturel, par exemple, de classification de texte, d’analyse des sentiments, etc.

Parmi les différentes technologies d’imbrication de mots, dans ce module, nous avons implémenté trois méthodes largement utilisées, dont deux modèles d’apprentissage en ligne, Word2Vec et FastText, et un modèle préentraîné, gant-wiki-gigaword-100. Les modèles d’apprentissage en ligne sont entraînés sur vos données en entrée, tandis que les modèles préentraînés sont entraînés hors connexion sur un corpus de texte plus volumineux (par exemple, Wikipédia ou Google News) contenant généralement quelque 100 milliards mots. Ensuite, l’imbrication de mots reste constante pendant la vectorisation de mots. Les modèles préentraînés offrent des avantages tels qu’un temps d’apprentissage réduit, un meilleur encodage des vecteurs de mots et de meilleures performances globales.

+ L’approche du modèle Word2Vec est l’une des techniques les plus répandues pour apprendre les imbrications de mots à l’aide d’un réseau neuronal superficiel. La théorie est exposée dans un document disponible en téléchargement au format PDF : [Efficient Estimation of Word Representations in Vector Space, Mikolov, Tomas et al](https://arxiv.org/pdf/1301.3781.pdf). L’implémentation décrite dans ce module est basée sur une [bibliothèque gensim pour Word2Vec](https://radimrehurek.com/gensim/models/word2vec.html).

+ La théorie de FastText est exposée dans un document disponible en téléchargement au format PDF : [Enriching Word Vectors with Subword Information, Bojanowski, Piotr et al](https://arxiv.org/pdf/1607.04606.pdf). L’implémentation décrite dans ce module est basée sur une [bibliothèque gensim pour FastText](https://radimrehurek.com/gensim/models/fasttext.html).

+ Modèle préentraîné Glove : glove-wiki-gigaword-100 est une collection de vecteurs préentraînés basés sur le corpus de texte Wikipédia, qui contient 5,6 milliards de jetons et 400 000 vocables sans casse. Un fichier PDF est disponible : [GloVe: Global Vectors for Word Representation](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>Comment configurer la fonctionnalité Vectoriser du texte

Ce module requiert un jeu de données contenant une colonne de texte. Du texte prétraité est préférable.

1. Ajoutez le module **Vectoriser du texte** à votre pipeline.

2. Comme entrée du module, fournissez un jeu de données contenant une ou plusieurs colonnes de texte.

3. Pour **Colonne cible**, choisissez une seule colonne contenant du texte à traiter.

    En général, parce que ce module crée un vocabulaire à partir de texte, le contenu des diverses colonnes diffère, ce qui conduit à des contenus de vocabulaire différents. C’est pourquoi le module n’accepte qu’une colonne cible.

4. Pour la **stratégie Word2Vec**, choisissez entre `GloVe pretrained English Model`, `Gensim Word2Vec` et `Gensim FastText`.

5. Si la **stratégie Word2Vec** est `Gensim Word2Vec` ou `Gensim FastText` :

    + **Algorithme d’apprentissage Word2Vec**. Choisissez entre `Skip_gram` et `CBOW`. La différence est expliquée dans [document](https://arxiv.org/pdf/1301.3781.pdf) d’origine.

        La méthode par défaut est `Skip_gram`.

    + **Longueur d’imbrication de mot**. Spécifiez la dimensionnalité des vecteurs de mots. Correspond au paramètre `size` dans gensim.

        La valeur par défaut d’embedding_size est 100.

    + **Taille de la fenêtre contextuelle**. Spécifiez la distance maximale entre le mot prédit et le mot actif. Correspond au paramètre `window` dans gensim.

        La taille de fenêtre par défaut est 5.

    + **Nombre d’époques**. Spécifiez le nombre d’époques (itérations) sur le corpus. Correspond au paramètre `iter` dans gensim.

        Le nombre d’époques par défaut est 5.

6. Pour **Taille maximale du vocabulaire**, spécifiez le nombre maximal de mots dans le vocabulaire généré.

    S’il y a plus de mots uniques que cela, écartez les mots peu fréquents.

    La taille de vocabulaire par défaut est 10 000.

7. Pour le **Nombre minimal de mots**, indiquez un nombre minimal d’occurrences des mots, qui a pour effet que module ignore tous les mots dont la fréquence est inférieure à celle que cette valeur définit.

    La valeur par défaut est 5.

8. Envoyez le pipeline.

## <a name="examples"></a>Exemples

Le module a une sortie :

+ **Vocabulaire avec imbrications** : Contient le vocabulaire généré, ainsi que l’imbrication de chaque mot, à raison d’une dimension par colonne.

### <a name="result-examples"></a>Exemples de résultats

Pour illustrer le fonctionnement du module **Vectoriser du texte** , l’exemple suivant applique ce module avec les paramètres par défaut au jeu de données Wikipédia SP 500 prétraité fourni dans Azure Machine Learning (préversion).

#### <a name="source-dataset"></a>Jeu de données source

Le jeu de données contient une colonne de catégorie, ainsi que le texte intégral extrait de Wikipédia. Ce tableau n’affiche que quelques exemples représentatifs.

|text|
|----------|
|nasdaq 100 component s p 500 component foundation founder location city apple campus 1 infinite loop street infinite loop cupertino california cupertino california location country united states...|
|br nasdaq 100 nasdaq 100 component br s p 500 s p 500 component industry computer software foundation br founder charles geschke br john warnock location adobe systems...|
|s p 500 s p 500 component industry automotive industry automotive predecessor general motors corporation 1908 2009 successor...|
|s p 500 s p 500 component industry conglomerate company conglomerate foundation founder location city fairfield connecticut fairfield connecticut location country usa area...|
|br s p 500 s p 500 component foundation 1903 founder william s harley br arthur davidson harley davidson founder arthur davidson br walter davidson br william a davidson location...|

#### <a name="output-vocabulary-with-embeddings"></a>Vocabulaire de sortie avec les imbrications

Le tableau suivant contient la sortie de ce module obtenue en utilisant le jeu de données Wikipédia SP 500 en entrée. La colonne la plus à gauche indique le vocabulaire, son vecteur d’imbrication est représenté par les valeurs des colonnes restantes dans la même ligne.

|Vocabulaire|Dimension d’imbrication 0|Dimension d’imbrication 1|Dimension d’imbrication 2|Dimension d’imbrication 3|Dimension d’imbrication 4|Dimension d’imbrication 5|...|Dimension d’imbrication 99|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|-0.375865|0.609234|0.812797|-0.002236|0.319071|-0.591986|...|0.364276
|component|0.081302|0.40001|0.121803|0.108181|0.043651|-0.091452|...|0.636587
|s|-0.34355|-0.037092|-0.012167|0.151542|0.601019|0.084501|...|0.149419
|p|-0.133407|0.073244|0.170396|0.326706|0.213463|-0.700355|...|0.530901
foundation|-0.166819|0.10883|-0.07933|-0.073753|0.262137|0.045725|...|0.27487
founder|-0.297408|0.493067|0.316709|-0.031651|0.455416|-0.284208|...|0.22798
location|-0.375213|0.461229|0.310698|0.213465|0.200092|0.314288|...|0.14228
city|-0.460828|0.505516|-0.074294|-0.00639|0.116545|0.494368|...|-0.2403
apple|0.05779|0.672657|0.597267|-0.898889|0.099901|0.11833|...|0.4636
campus|-0.281835|0.29312|0.106966|-0.031385|0.100777|-0.061452|...|0.05978
infinite|-0.263074|0.245753|0.07058|-0.164666|0.162857|-0.027345|...|-0.0525
loop|-0.391421|0.52366|0.141503|-0.105423|0.084503|-0.018424|...|-0.0521

Dans cet exemple, nous avons utilisé le `Gensim Word2Vec` par défaut comme **Stratégie Word2Vec**, l’**Algorithme d’apprentissage** est `Skip-gram`, la **Longueur d’imbrication de mot** est 100, et nous avons 100 colonnes d’imbrication.

## <a name="technical-notes"></a>Notes techniques

Cette section contient des détails, des conseils et des réponses à des questions fréquentes.

+ Différence entre apprentissage en ligne et modèle préentraîné

    Ce module **Vectoriser du texte** contient trois stratégies différentes, deux modèles d’apprentissage en ligne et un modèle préentraîné. Le modèle d’apprentissage en ligne utilise votre jeu de données en entrée en tant que données d’apprentissage et génère du vocabulaire et des vecteurs de mots pendant l’apprentissage. Le modèle préentraîné est déjà formé par un corpus de texte bien plus volumineux, tel que Wikipédia ou Twitter, et comprend donc un ensemble de paires (mot, imbrication).  

    Si le modèle préentraîné Glove est choisi en tant que stratégie de vectorisation de mots, il synthétise un vocabulaire à partir du jeu de données en entrée et génère un vecteur d’imbrication pour chaque mot du modèle préentraîné, sans apprentissage en ligne. L’utilisation d’un modèle préentraîné peut réduire le temps de d’apprentissage et offrir de meilleures performances, en particulier lorsque la taille du jeu de données d’entrée est relativement petite.

+ Taille d’imbrication

    En général, la longueur de l’imbrication de mot est définie sur quelques centaines (par exemple, 100, 200, 300) pour obtenir de bonnes performances, car la petite taille d’imbrication signifie un petit espace vectoriel, ce qui peut entraîner des collisions d’incorporation.  

    Pour les modèles préentraînés, la longueur des imbrications de mots est fixe. Dans cette implémentation, la taille d’imbrication de glove-wiki-gigaword-100 est de 100.


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 

Pour obtenir la liste des erreurs spécifiques des modules du concepteur (préversion), consultez [Codes d’erreur d’apprentissage automatique](designer-error-codes.md).
