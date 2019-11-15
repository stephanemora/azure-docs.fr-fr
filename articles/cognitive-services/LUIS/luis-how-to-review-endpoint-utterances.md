---
title: Passer en revue les énoncés de l’utilisateur – LUIS
titleSuffix: Azure Cognitive Services
description: Passez en revue les énoncés capturés par l’apprentissage actif pour sélectionner l’intention et marquer les entités pour les énoncés du monde réel ; acceptez les modifications, entraînez et publiez.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4a77ac26076fc1b1e4e94ee24dafb28a0e88c858
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669378"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Comment passer en revue les énoncés de point de terminaison dans le portail LUIS pour l’apprentissage actif

[L’apprentissage actif](luis-concept-review-endpoint-utterances.md) capture les requêtes de point de terminaison et sélectionne les énoncés de point de terminaison de l’utilisateur dont il n’est pas sûr. Vous passez en revue ces énoncés pour sélectionner l’intention et marquez des entités pour ces énoncés réalistes. Acceptez ces modifications dans vos exemples d’énoncés, puis formez et publiez. Ensuite, LUIS identifie les énoncés de manière plus précise.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="enable-active-learning"></a>Activer l’apprentissage actif

Pour activer l’apprentissage actif, enregistrez des requêtes de l’utilisateur. Cela s’effectue en définissant la [requête de point de terminaison](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) avec la valeur et le paramètre `log=true` querystring.

## <a name="disable-active-learning"></a>Désactiver l’apprentissage actif

Pour désactiver l’apprentissage actif, n’enregistrez pas des requêtes de l’utilisateur. Cela s’effectue en définissant la [requête de point de terminaison](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) avec la valeur et le paramètre `log=false` querystring.

## <a name="filter-utterances"></a>Filtrer les énoncés

1. Ouvrez votre application (par exemple, TravelAgent) en sélectionnant son nom sur la page **Mes applications**, puis sélectionnez **Générer** dans la barre supérieure.

1. Sous **Improve app performance** (Améliorer les performances de l’application), sélectionnez **Review endpoint utterances** (Réviser les énoncés de point de terminaison).

1. Sur la page **Review endpoint utterances** (Réviser les énoncés de point de terminaison), sélectionnez dans la zone de texte **Filter list by intent or entity** (Filtrer la liste par intention ou entité). Cette liste déroulante répertorie toutes les intentions sous **INTENTS** (INTENTIONS) et toutes les entités sous **ENTITIES** (ENTITÉS).

    ![Filtre des énoncés](./media/label-suggested-utterances/filter.png)

1. Sélectionnez une catégorie (intentions ou entités) dans la liste déroulante et passez en revue les énoncés.

    ![Énoncés d’intention](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Étiqueter des entités
LUIS remplace les jetons d’entité (mots) par des noms d’entités mis en surbrillance en bleu. Si un énoncé contient des entités sans étiquette, étiquetez-les dans l’énoncé. 

1. Sélectionnez le ou les mots dans l’énoncé. 

1. Sélectionnez une entité dans la liste.

    ![Étiqueter l’entité](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Aligner un énoncé unique

Chaque énoncé contient une intention suggérée affichée dans la colonne **Aligned intent** (Intention alignée). 

1. Si vous acceptez cette suggestion, sélectionnez la coche.

    ![Conserver l’intention alignée](./media/label-suggested-utterances/align-intent-check.png)

1. Si n’acceptez pas la suggestion, sélectionnez l’intention appropriée dans la liste déroulante de l’intention alignée, puis sélectionnez la coche à droite de l’intention alignée. 

    ![Intention alignée](./media/label-suggested-utterances/align-intent.png)

1. Lorsque vous avez sélectionné la coche, l’énoncé est supprimé de la liste. 

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
