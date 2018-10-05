---
title: Passer en revue les énoncés de point de terminaison pour Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: La fonctionnalité la plus remarquable de LUIS est le concept d’apprentissage actif. Une fois que votre application LUIS a des requêtes de point de terminaison, l’apprentissage actif améliore la qualité des résultats en sélectionnant les énoncés pour lesquels il a des doutes. Si vous étiquetez ces énoncés, formez et publiez, LUIS identifie alors les énoncés de manière plus précise.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: a5e0dabe251d14389923df3efe41f6ba80f41bdd
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47030793"
---
# <a name="review-endpoint-utterances"></a>Réviser les énoncés de point de terminaison

La fonctionnalité de contact de LUIS est le [concept](luis-concept-review-endpoint-utterances.md) d’apprentissage actif. Lorsque votre LUIS dispose de requêtes du point de terminaison, LUIS utilise l’apprentissage actif pour améliorer la qualité des résultats. Dans le processus d’apprentissage actif, LUIS examine tous les énoncés de point de terminaison et sélectionne les énoncés dont il n’est pas sûr. Si vous étiquetez ces énoncés, formez et publiez, LUIS identifie alors les énoncés de manière plus précise. 

## <a name="filter-utterances"></a>Filtrer les énoncés
1. Ouvrez votre application (par exemple, TravelAgent) en sélectionnant son nom sur la page **Mes applications**, puis sélectionnez **Générer** dans la barre supérieure.

2. Sous **Improve app performance** (Améliorer les performances de l’application), sélectionnez **Review endpoint utterances** (Réviser les énoncés de point de terminaison).

3. Sur la page **Review endpoint utterances** (Réviser les énoncés de point de terminaison), sélectionnez dans la zone de texte **Filter list by intent or entity** (Filtrer la liste par intention ou entité). Cette liste déroulante répertorie toutes les intentions sous **INTENTS** (INTENTIONS) et toutes les entités sous **ENTITIES** (ENTITÉS).

    ![Filtre des énoncés](./media/label-suggested-utterances/filter.png)

4. Sélectionnez une catégorie (intentions ou entités) dans la liste déroulante et passez en revue les énoncés.

    ![Énoncés d’intention](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Étiqueter des entités
LUIS remplace les jetons d’entité (mots) par des noms d’entités mis en surbrillance en bleu. Si un énoncé contient des entités sans étiquette, étiquetez-les dans l’énoncé. 

1. Sélectionnez le ou les mots dans l’énoncé. 

2. Sélectionnez une entité dans la liste.

    ![Étiqueter l’entité](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Aligner un énoncé unique

Chaque énoncé contient une intention suggérée affichée dans la colonne **Aligned intent** (Intention alignée). 

1. Si vous acceptez cette suggestion, sélectionnez la coche.

    ![Conserver l’intention alignée](./media/label-suggested-utterances/align-intent-check.png)

2. Si n’acceptez pas la suggestion, sélectionnez l’intention appropriée dans la liste déroulante de l’intention alignée, puis sélectionnez la coche à droite de l’intention alignée. 

    ![Intention alignée](./media/label-suggested-utterances/align-intent.png)

3. Lorsque vous avez sélectionné la coche, l’énoncé est supprimé de la liste. 

## <a name="align-several-utterances"></a>Aligner plusieurs énoncés

Pour aligner plusieurs énoncés, cochez la case à gauche des énoncés, puis sélectionnez le bouton **Ajouter les éléments sélectionnés**. 

![Aligner plusieurs](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Vérifier l’intention alignée
Vous pouvez vérifier l’énoncé qui a été aligné avec l’intention appropriée en accédant à la page **Intents** Intentions, en sélectionnant le nom de l’intention et en passant en revue les énoncés. L’énoncé **Review endpoint utterances** (Réviser les énoncés de point de terminaison) figure dans la liste.

## <a name="delete-utterance"></a>Supprimer l’énoncé
Chaque énoncé peut être supprimé de la liste de révision. Une fois supprimé, il n’apparaîtra plus dans la liste. Cela est vrai même si l’utilisateur entre le même énoncé à partir du point de terminaison. 

Si vous ne savez pas si vous devez supprimer l’énoncé, déplacez-le vers l’intention None (Aucune), ou créez une nouvelle intention telle que « divers » et déplacez l’énoncé vers cette intention. 

## <a name="delete-several-utterances"></a>Supprimer plusieurs énoncés
Pour supprimer plusieurs énoncés, sélectionnez chaque élément et sélectionnez la corbeille à droite du bouton **Ajouter les éléments sélectionnés**.

![Supprimer plusieurs](./media/label-suggested-utterances/delete-several.png)

## <a name="next-steps"></a>Étapes suivantes

Pour tester l’amélioration de la performance après avoir étiqueté les énoncés suggérés, vous pouvez accéder à la console de test en sélectionnant **Test** dans le panneau supérieur. Pour obtenir des instructions sur la façon de tester votre application à l’aide de la console de test, consultez [Former et tester votre application](luis-interactive-test.md).
