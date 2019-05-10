---
title: 'Régression de forêt de décision : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module de régression de forêt de décision dans le service Azure Machine Learning pour créer un modèle de régression basé sur un ensemble d’arbres de décision.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d372adf75d46fdedb7a6f2b17e47822475d1f155
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442361"
---
# <a name="decision-forest-regression-module"></a>Module de régression de forêt de décision

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour créer un modèle de régression basé sur un ensemble d’arbres de décision.

Une fois que vous avez configuré le modèle, vous devez le former à l’aide d’un jeu de données étiquetée et [former le modèle](./train-model.md) module.  Le modèle formé peut ensuite être utilisé pour effectuer des prédictions. 

## <a name="how-it-works"></a>Fonctionnement

Arbres de décision sont des modèles non paramétriques qui effectuent une série de tests simples pour chaque instance, en parcourant une arborescence binaire de données jusqu'à ce qu’un nœud terminal (décision) est atteinte.

Arbres de décision présentent les avantages suivants :

- Ils sont efficaces dans les calculs et l’utilisation de la mémoire au cours de formation et de prédiction.

- Ils peuvent représenter des limites de décision non linéaires.

- Ils effectuer la sélection des fonctionnalités intégrées et classification et sont résilients en présence de fonctionnalités bruyantes.

Ce modèle de régression se compose d’un ensemble d’arbres de décision. Chaque arborescence dans une forêt de décision de régression génère une distribution gaussienne qu’une prédiction. Une agrégation est effectuée sur l’ensemble d’arbres à trouver une distribution gaussienne le plus proche de la distribution combinée pour tous les arbres dans le modèle.

Pour plus d’informations sur l’infrastructure théorique cet algorithme et son implémentation, consultez cet article : [Forêts de décision : Une infrastructure unifiée pour la Classification, la régression, l’Estimation de la densité, collecteur Learning et apprentissage supervisé semi-structurées](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Comment configurer le modèle de régression de forêt de décision

1. Ajouter le **Decision Forest Regression** module à l’expérience. Vous pouvez rechercher le module dans l’interface sous **Machine Learning**, **initialiser le modèle**, et **régression**.

2. Ouvrez les propriétés du module et pour **méthode de rééchantillonnage**, choisissez la méthode utilisée pour créer les arbres individuels.  Vous pouvez choisir parmi **Bagging** ou **répliquer**.

    - **Bagging**: ENSACHAGE est également appelé *agrégation bootstrap*. Chaque arborescence dans une forêt de décision de régression génère une distribution gaussienne par le biais de prédiction. L’agrégation consiste à trouver un gaussienne dont les deux premiers instants correspondent à des moments du mélange de Gaussians donné en combinant tous les Gaussians retournés par arbres individuels.

         Pour plus d’informations, consultez la page Wikipedia pour [agrégation Bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Répliquer**: Dans la réplication, chaque arborescence est formé sur exactement les mêmes données d’entrée. La détermination du fractionner le prédicat est utilisé pour chaque nœud d’arbre reste aléatoire et les arborescences seront différents.

         Pour plus d’informations sur le processus d’apprentissage avec le **répliquer** , consultez l’option [des forêts décisionnelles de vision par ordinateur et d’analyse d’Image médical. Criminisi et J. Shotton. Springer 2013. ](https://research.microsoft.com/projects/decisionforests/).

3. Spécifiez comment vous souhaitez que le modèle formé, en définissant le **créer un mode d’entraînement** option.

    - **Paramètre unique**

      Si vous savez comment vous souhaitez configurer le modèle, vous pouvez fournir un ensemble spécifique de valeurs comme arguments. Vous l’avez peut-être découvert ces valeurs par expérimentation ou les reçues en tant que guide.



4. Pour **nombre d’arbres de décision**, indique le nombre total d’arbres de décision à créer dans l’ensemble. En créant plusieurs arbres de décision, vous pouvez obtenir une meilleure couverture, mais les temps d’apprentissage augmente.

    > [!TIP]
    > Cette valeur contrôle également le nombre d’arbres affichés lorsque vous examinez le modèle formé. Si vous souhaitez afficher ou imprimer une arborescence unique, vous pouvez définir la valeur 1 ; Toutefois, cela signifie que qu’une seule arborescence sera produite (l’arborescence avec le jeu de paramètres initial) et aucune itération supplémentaire ne sera effectuée.

5. Pour **profondeur maximale des arbres de décision**, tapez un nombre pour limiter la profondeur maximale de n’importe quel arbre de décision. Augmentation de la profondeur de l’arborescence peut accroître la précision, avec comme corolaire des temps de formation surajustement et d’augmentation.

6. Pour **nombre de fractionnements aléatoires par nœud**, tapez le nombre de divisions à utiliser lors de la génération de chaque nœud de l’arborescence. Un *fractionner* signifie que les fonctionnalités de chaque niveau de l’arbre (nœud) est réparties au hasard.

7. Pour **nombre minimal d’échantillons par nœud terminal**, indiquer le nombre minimal de cas qui sont nécessaires pour créer un nœud terminal (feuille) dans une arborescence.

     En augmentant cette valeur, vous augmentez le seuil pour la création de nouvelles règles. Par exemple, la valeur par défaut de 1, même un seul cas peut entraîner une nouvelle règle à créer. Si vous augmentez la valeur à 5, les données d’apprentissage doivent contenir au moins cinq cas qui remplissent les mêmes conditions.


9. Connectez un jeu de données étiquetée, sélectionnez une colonne d’étiquette unique contenant pas plus de deux résultats et connectez-vous à [former le modèle](./train-model.md).

    - Si vous définissez **créer un mode d’entraînement** option **seul paramètre**, former le modèle à l’aide de la [former le modèle](./train-model.md) module.

   

10. Exécutez l’expérience.

### <a name="results"></a>Résultats

Une fois la formation terminée :

+ Pour afficher l’arborescence qui a été créé sur chaque itération, avec le bouton droit de la sortie du module de formation, puis sélectionnez **visualiser**.

+ Pour afficher les règles pour chaque nœud, cliquez sur chaque arbre et explorez les divisions.

+ Pour enregistrer un instantané du modèle formé, avec le bouton droit de la sortie du module de formation, puis sélectionnez **enregistrer en tant que modèle formé**. Cette copie du modèle n’est pas mis à jour lors des exécutions consécutives de l’expérience. 

## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 