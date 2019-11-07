---
title: 'Sélection de caractéristiques par filtrage : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module de sélection de caractéristiques par filtrage dans Azure Machine Learning Service pour identifier les caractéristiques d’un jeu de données avec la meilleure capacité de prédiction.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: ac1421c93f1a4ca42d7f1d94bb898c423c380a57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511862"
---
# <a name="filter-based-feature-selection"></a>Sélection de caractéristiques par filtrage

Cet article explique comment utiliser le module de [sélection de caractéristiques par filtrage](filter-based-feature-selection.md) dans la version Azure Machine Learning pour les concepteurs (préversion) afin d’identifier les colonnes de votre jeu de données d’entrée qui ont la meilleure capacité de prédiction. 

En général, la *sélection de caractéristiques* fait référence au processus d’application de tests statistiques à des entrées, en fonction d’une valeur de sortie donnée, pour déterminer quelles colonnes prédisent plus précisément la valeur de sortie. Le module de [sélection de caractéristiques par filtrage](filter-based-feature-selection.md) propose plusieurs algorithmes de sélection de caractéristiques, y compris des méthodes de corrélation telles que la corrélation de Pearson et les tests du Khi-deux. 

Lorsque vous utilisez le module de [sélection de caractéristiques par filtrage](filter-based-feature-selection.md), vous fournissez un jeu de données, identifiez la colonne qui contient l’étiquette ou la variable dépendante, puis spécifiez une méthode unique à utiliser pour mesurer l’importance des caractéristiques.

Le module génère un jeu de données qui contient les meilleures colonnes de type Caractéristique, classées en fonction de leur capacité de prédiction. Il renvoie également le nom des caractéristiques et leur score selon la métrique sélectionnée.  

### <a name="what-is-filter-based-feature-selection-and-why-use-it"></a>Qu’est-ce que la sélection des caractéristiques par filtrage et pourquoi l’utiliser ?  

Ce module pour la sélection de caractéristiques fonctionne « par filtrage », car vous utilisez la métrique sélectionnée pour identifier les attributs non pertinents et filtrer les colonnes redondantes de votre modèle.  Vous choisissez une métrique statistique unique qui correspond à vos données et le module calcule un score pour chaque colonne de type Caractéristique. Les colonnes sont ensuite classées en fonction de leur score de caractéristique. 

En choisissant les caractéristiques appropriées, vous pouvez potentiellement améliorer la précision et l’efficacité de la classification. 

En général, vous n’utilisez que les colonnes avec le meilleur score pour générer votre modèle prédictif. Vous pouvez laisser dans votre jeu de données les colonnes dont le score de sélection de caractéristiques est bas ; elles seront ignorées quand vous générerez un modèle.  

### <a name="how-to-choose-a-feature-selection-metric"></a>Comment choisir une métrique de sélection de caractéristiques

La **sélection de caractéristiques par filtrage** fournit diverses métriques pour évaluer la valeur des informations contenues dans chaque colonne.  Cette section fournit une description générale de chaque métrique et de son mode d’application. Des exigences supplémentaires pour l’utilisation de chaque métrique sont indiquées dans la section [Informations techniques](#technical-notes) et dans les [instructions](#how-to-configure-filter-based-feature-selection) de configuration de chaque module.

-   **Corrélation de Pearson**  

     Le coefficient de corrélation de Pearson est également représenté dans les modèles statistiques par la valeur `r`. Pour deux variables quelles qu’elles soient, il retourne une valeur qui indique la force de la corrélation.

     Le coefficient de corrélation de Pearson est calculé en prenant la covariance de deux variables et en la divisant par le produit de leurs écarts-types. Le coefficient n’est pas affecté par les changements d’échelle des deux variables.  

-   **Test du Khi-deux**  

     Le test du Khi-deux bidirectionnel est une méthode statistique qui mesure l’écart entre les valeurs attendues et les résultats obtenus. La méthode suppose que les variables soient aléatoires et tirées d’un échantillon adéquat de variables indépendantes. La statistique Khi-deux résultante indique l’écart entre les résultats obtenus et le résultat attendu (aléatoire).  


> [!TIP]
> Si vous avez besoin d’une autre option pour une méthode de sélection des caractéristiques personnalisée, utilisez le module [Exécuter un script R](execute-r-script.md). 
##  <a name="how-to-configure-filter-based-feature-selection"></a>Comment configurer la sélection de caractéristiques par filtrage

Vous choisissez une métrique statistique standard et le module calcule la corrélation entre deux colonnes : la colonne Étiquette et une colonne de type Caractéristique.

1.  Ajoutez le module de **sélection de caractéristiques par filtrage** à votre pipeline. Ce module figure dans la catégorie **Sélection des caractéristiques** de la version pour les concepteurs.

2. Connectez un jeu de données d’entrée qui contient au moins deux colonnes qui sont des caractéristiques potentielles.  

    Pour indiquer qu’une colonne doit être analysée et qu’un score de caractéristique doit être généré, utilisez le module [Modifier les métadonnées](edit-metadata.md) pour définir l’attribut **IsFeature**. 

    > [!IMPORTANT]
    > Assurez-vous que les colonnes que vous fournissez comme entrées sont des caractéristiques potentielles. Par exemple, une colonne qui contient une valeur unique n’a pas de valeur d’information.
    >
    > Si vous savez que certaines colonnes risquent de créer des caractéristiques inadaptées, vous pouvez les supprimer de la sélection. Vous pouvez également utiliser le module [Modifier les métadonnées](edit-metadata.md) pour les définir comme **Catégorie**. 
3.  Pour la **méthode de notation des caractéristiques**, choisissez l’une des méthodes statistiques établies ci-dessous. Celle-ci sera utilisée pour le calcul des scores.  

    | Méthode              | Configuration requise                             |
    | ------------------- | ---------------------------------------- |
    | Corrélation de Pearson | L’étiquette peut être de type texte ou numérique. Les caractéristiques doivent être numériques. |
    Test du Khi-deux| Les étiquettes et les caractéristiques peuvent être de type texte ou numérique. Utilisez cette méthode pour calculer l’importance des caractéristiques pour deux colonnes Catégorie.|

    > [!TIP]
    > Si vous modifiez la métrique sélectionnée, toutes les autres sélections seront réinitialisées. Par conséquent, pensez à définir cette option en premier.
4.  Sélectionnez l’option **Operate on feature columns only** (Travailler avec les colonnes de type Caractéristique uniquement) pour générer un score uniquement pour les colonnes de type Caractéristique. 

    Si vous désélectionnez cette option, le module crée un score pour toutes les colonnes qui répondent aux autres critères, jusqu’à atteindre le nombre de colonnes spécifié dans **Number of desired features** (Nombre de caractéristiques souhaité).  

5.  Pour la **colonne cible**, cliquez sur **Launch column selector** (Lancer le sélecteur de colonne) pour afficher la colonne Étiquette par nom ou par son index (les index sont de base un).  

     Une colonne de type Étiquette est requise pour toutes les méthodes qui impliquent une corrélation statistique. Le module renvoie une erreur au moment de la conception si vous ne choisissez aucune colonne Étiquette ou plusieurs colonnes Étiquette. 

6.  Dans le champ **Number of desired features** (Nombre de caractéristiques souhaité), entrez le nombre de colonnes de type Caractéristique que vous souhaitez retourner dans les résultats.  

     - Le nombre minimal de caractéristiques que vous pouvez spécifier est 1, mais nous vous recommandons d’augmenter cette valeur.  

     - Si le nombre de caractéristiques spécifié est supérieur au nombre de colonnes dans le jeu de données, toutes les caractéristiques seront retournées, même celles avec des scores nuls.  

    - Si vous spécifiez moins de colonnes de résultat qu’il n’y a de colonnes de type Caractéristique, les caractéristiques seront classées selon leur score par ordre décroissant et seules les caractéristiques principales seront retournées. 

7.  Exécutez le pipeline, ou sélectionnez le module de [sélection de caractéristiques par filtrage](filter-based-feature-selection.md), puis cliquez sur **Exécuter la sélection**.


## <a name="results"></a>Résultats

Une fois le processus terminé :

+ Pour afficher la liste complète des colonnes de type Caractéristique qui ont été analysées ainsi que leur score, cliquez avec le bouton droit sur le module, sélectionnez **Caractéristiques**, puis cliquez sur **Visualiser**.  

+ Pour afficher le jeu de données qui est généré en fonction de vos critères de sélection de caractéristiques, cliquez avec le bouton droit sur le module, sélectionnez **Jeu de données**, puis cliquez sur **Visualiser**. 

Si le jeu de données contient moins de colonnes que prévu, vérifiez les paramètres du module et les types de données des colonnes fournies comme entrée. Par exemple, si vous avez indiqué 1 dans **Number of desired features** (Nombre de caractéristiques souhaité), le jeu de données de sortie contient seulement deux colonnes : la colonne Étiquette et la colonne de type Caractéristique la mieux classée.


##  <a name="technical-notes"></a>Notes techniques  

### <a name="implementation-details"></a>Informations d’implémentation

Si vous utilisez la corrélation de Pearson sur une caractéristique numérique et une étiquette de type Catégorie, le score de caractéristique est calculé comme suit :  

1.  Pour chaque niveau de la colonne de type Catégorie, calculez la moyenne conditionnelle de la colonne numérique.  

2.  Mettez en corrélation la colonne des moyennes conditionnelles avec la colonne numérique.  

### <a name="requirements"></a>Configuration requise  

-   Il est impossible de générer un score de sélection de caractéristiques pour une colonne de type **Étiquette** ou **Score**.  

-   Si vous tentez d’utiliser une méthode de calcul de score avec une colonne d’un type de données non pris en charge par cette méthode, soit le module génère une erreur, soit un score de zéro est attribué à la colonne.  

-   Si une colonne contient des valeurs logiques (true/false), celles-ci sont traitées comme suit : true = 1 et false = 0.  

-   Une colonne ne peut pas être une caractéristique si vous lui avez attribué le type **Étiquette** ou **Score**.  

### <a name="how-missing-values-are-handled"></a>Gestion des valeurs manquantes  

-   Vous ne pouvez pas indiquer comme cible (étiquette) une colonne dont toutes les valeurs sont manquantes.  

-   Si une colonne contient des valeurs manquantes, celles-ci sont ignorées lors du calcul du score de la colonne.  

-   Si une colonne désignée comme colonne de type Caractéristique ne contient que des valeurs manquantes, elle reçoit un score de zéro.   


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 

