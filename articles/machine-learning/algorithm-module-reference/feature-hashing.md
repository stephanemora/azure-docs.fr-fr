---
title: 'Hachage des caractéristiques : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Hachage des caractéristiques dans le service Azure Machine Learning pour caractériser des données texte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 78d01cf071faed312773ebf12c75e7e6e5596e71
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210559"
---
# <a name="feature-hashing"></a>Hachage des caractéristiques

Cet article décrit un module de l’interface visuelle (préversion) pour Azure Machine Learning Service.

Utilisez le module **Hachage des caractéristiques** pour transformer un flux de texte anglais en ensemble de caractéristiques d’entier. Vous pouvez alors passer cet ensemble de caractéristiques hachées à un algorithme de machine learning pour entraîner un modèle d’analyse de texte.

La fonctionnalité de hachage des caractéristiques fournie dans ce module se base sur le framework nimbusml. Pour plus d’informations, consultez [NgramHash (classe)](https://docs.microsoft.com/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest).

## <a name="what-is-feature-hashing"></a>Qu’est-ce que le hachage des caractéristiques ?

Le hachage des caractéristiques fonctionne en convertissant des jetons uniques en entiers. Il opère sur les chaînes exactes que vous fournissez en tant qu’entrée et n’effectue aucune analyse linguistique ni prétraitement. 

Par exemple, prenez un ensemble de phrases simples en anglais comme celles-ci, suivies d’un score de sentiment. Supposons que vous voulez utiliser ce texte pour générer un modèle.

|TEXTE UTILISATEUR|SENTIMENT|
|--------------|---------------|
|I loved this book|3|
|I hated this book|1|
|This book was great|3|
|I love books|2|

En interne, le module **Hachage des caractéristiques** crée un dictionnaire de n-grammes. Par exemple, la liste des digrammes pour ce jeu de données ressemblerait à la suivante :

|TERME (digrammes)|FRÉQUENCE|
|------------|---------------|
|This book|3|
|I loved|1|
|I hated|1|
|I love|1|

Vous pouvez contrôler la taille des n-grammes à l’aide de la propriété **N-grammes**. Si vous choisissez les digrammes, les unigrammes sont également calculés. Le dictionnaire inclut également des termes uniques comme ceux-ci :

|Terme (unigrammes)|FRÉQUENCE|
|------------|---------------|
|book|3|
|I|3|
|books|1|
|was|1|

Une fois le dictionnaire généré, le module **Hachage des caractéristiques** convertit les termes du dictionnaire en valeurs de hachage. Il calcule ensuite si une caractéristique a été utilisée dans chaque cas. Pour chaque ligne de données texte, le module génère un ensemble de colonnes, une colonne pour chaque caractéristique hachée.

Par exemple, après le hachage, les colonnes de caractéristiques peuvent ressembler à ceci :

|Rating|Caractéristique de hachage 1|Caractéristique de hachage 2|Caractéristique de hachage 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5\.|0|0|0|

* Si la valeur indiquée dans la colonne est 0, la ligne ne contient pas la caractéristique hachée.
* Si la valeur est 1, la ligne contient effectivement la caractéristique.

Le hachage des caractéristiques vous permet de représenter des documents texte de longueur variable sous forme de vecteurs de caractéristiques numériques de longueur égale pour obtenir une réduction de la dimensionnalité. Si vous avez essayé d’utiliser la colonne de texte en l’état pour l’entraînement, celle-ci est traitée en tant que colonne de caractéristique catégorielle avec de nombreuses valeurs distinctes.

Les sorties numériques permettent également d’utiliser des méthodes d’apprentissage automatique courantes, notamment la classification, le clustering et l’extraction d’informations. Étant donné que les opérations de recherche peuvent utiliser des hachages d’entiers plutôt que des comparaisons de chaînes, l’obtention des poids des caractéristiques est également beaucoup plus rapide.

## <a name="configure-feature-hashing"></a>Configurer le hachage des caractéristiques

1.  Ajoutez le module **Hachage des caractéristiques** à votre expérience dans l’interface visuelle.

1. Connectez le jeu de données qui contient le texte à analyser.

    > [!TIP]
    > Étant donné que le hachage des caractéristiques n’effectue pas d’opérations lexicales comme la recherche de radical ou la troncation, vous pouvez parfois obtenir de meilleurs résultats en effectuant un prétraitement du texte avant d’appliquer le hachage des caractéristiques. 

1. Définissez des **colonnes cibles** sur les colonnes de texte à convertir en caractéristiques hachées. 

    * Le type de données des colonnes doit être une chaîne.
    
    * Le choix de plusieurs colonnes de texte peut avoir un impact significatif sur la dimensionnalité des caractéristiques. Par exemple, le nombre de colonnes pour un hachage de 10 bits passe de 1 024 pour une colonne unique à 2 048 pour deux colonnes.

1. Utilisez la **taille des bits de hachage** pour spécifier le nombre de bits à utiliser lors de la création de la table de hachage.
    
    La taille des bits par défaut s’élève à 10. Pour de nombreux problèmes, cette valeur convient. Vous aurez peut-être besoin de davantage d’espace pour éviter les collisions selon la taille du vocabulaire des n-grammes du texte d’entraînement.
    
1. Pour **N-grammes**, tapez un nombre qui définit la longueur maximale des n-grammes à ajouter au dictionnaire d’entraînement. Un n-gramme est une séquence de *n* mots, traitée comme une unité unique.

    Par exemple, si vous entrez 3, des unigrammes, des digrammes et des trigrammes sont créés.

1. Exécutez l’expérience.

## <a name="results"></a>Résultats

Une fois le traitement terminé, le module génère un jeu de données transformé dans lequel la colonne de texte d’origine a été convertie en plusieurs colonnes, chacune représentant une caractéristique dans le texte. En fonction de l’importance du dictionnaire, le jeu de données obtenu peut être volumineux :

|Nom de colonne 1|Type de colonne 2|
|-------------------|-------------------|
|TEXTE UTILISATEUR|Colonne de données d’origine|
|SENTIMENT|Colonne de données d’origine|
|TEXTE UTILISATEUR - Caractéristique de hachage 1|Colonne de caractéristique hachée|
|TEXTE UTILISATEUR - Caractéristique de hachage 2|Colonne de caractéristique hachée|
|TEXTE UTILISATEUR - Caractéristique de hachage n|Colonne de caractéristique hachée|
|TEXTE UTILISATEUR - Caractéristique de hachage 1 024|Colonne de caractéristique hachée|

Après avoir créé le jeu de données transformé, vous pouvez l’utiliser en tant qu’entrée dans le module Entraîner le modèle.
 
### <a name="best-practices"></a>Bonnes pratiques

Les bonnes pratiques suivantes peuvent vous aider à tirer le meilleur parti du module **Hachage des caractéristiques** :

* Ajoutez un module **Pré-traiter le texte** avant d’utiliser **Hachage des caractéristiques** pour prétraiter le texte d’entrée. 

* Ajoutez un module **Sélectionner des colonnes** après le module **Hachage des caractéristiques** pour supprimer les colonnes de texte du jeu de données de sortie. Vous n’avez pas besoin des colonnes de texte une fois que les caractéristiques de hachage ont été générées.
    
* Envisagez d’utiliser ces options de prétraitement de texte pour simplifier les résultats et en améliorer la précision :

    * Césure de mots
    * Arrêter la suppression de mots
    * Normalisation de casse
    * Suppression de la ponctuation et des caractères spéciaux
    * Recherche de radical  

L’ensemble optimal de méthodes de prétraitement à appliquer dans une solution individuelle dépend du domaine, du vocabulaire et des besoins métier. Faites des essais avec vos données pour déterminer quelles méthodes de traitement de texte sont les plus efficaces.

## <a name="next-steps"></a>Étapes suivantes
            
Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 
