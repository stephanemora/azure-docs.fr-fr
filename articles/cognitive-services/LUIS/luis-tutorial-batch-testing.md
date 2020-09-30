---
title: 'Tutoriel : Tests par lots pour détecter les problèmes - LUIS'
description: Ce tutoriel montre comment utiliser des tests par lots pour valider la qualité de votre application Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2020
ms.openlocfilehash: 7dd181f8cd398dd683296b446028b398a9800b53
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91298310"
---
# <a name="tutorial-batch-test-data-sets"></a>Tutoriel : Tester des jeux de données par lot

Ce tutoriel montre comment utiliser des tests par lots pour valider la qualité de votre application Language Understanding (LUIS).

Les tests de lots permettent de valider l’état du modèle actif entraîné avec un ensemble connu d’entités et d’énoncés étiquetés. Dans le fichier de lot au format JSON, ajoutez les énoncés et définissez les étiquettes d’entités que vous voulez prédire au sein de l’énoncé.

Exigences des tests de lots :

* 1 000 énoncés maximum par test.
* Pas de doublons.
* Types d’entités autorisés : seules les entités issues du machine learning.

Si vous utilisez une application autre que ce tutoriel, n’utilisez *pas* les exemples d’énoncés déjà ajoutés à votre application.

**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importer l’exemple d’application
> * Créer un fichier de test par lot
> * Exécuter un test par lot
> * Examiner les résultats du test

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importer l’exemple d’application

Importez une application qui prend une commande de pizza telle que `1 pepperoni pizza on thin crust`.

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Connectez-vous au [portail LUIS](https://www.luis.ai) et sélectionnez vos **abonnement** et **ressource de création** pour voir les applications affectées à cette dernière.
1. Importez le code JSON dans une nouvelle application appelée `Pizza app`.


1. Sélectionnez **Entraîner** dans le coin supérieur droit de la barre de navigation pour entraîner l’application.

## <a name="what-should-the-batch-file-utterances-include"></a>Que doivent inclure les énoncés des fichiers de commandes

Le fichier de commandes doit inclure des énoncés avec les entités issues du machine learning de niveau supérieur étiquetées, notamment les positions de début et de fin. Les énoncés ne doivent pas faire partie des exemples déjà présents dans l’application. Il doit s’agir d’énoncés que vous souhaitez prédire de manière positive pour l’intention et les entités.

Vous pouvez séparer les tests par l’intention et/ou l’entité ou avoir tous les tests (jusqu’à 1 000 énoncés) dans le même fichier.

## <a name="batch-file"></a>Fichier de commandes

L’exemple JSON comprend un énoncé avec une entité étiquetée pour illustrer à quoi ressemble un fichier de test. Dans vos propres tests, vous devez avoir de nombreux énoncés avec l’intention correcte et l’entité issue du machine learning étiquetée.

1. Créez `pizza-with-machine-learned-entity-test.json` dans un éditeur de texte ou [téléchargez-le](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true).

2. Dans le fichier de commandes au format JSON, ajoutez un énoncé avec l’**intention** à prédire dans le test.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Exécuter le test par lot

1. Sélectionnez **Test** dans la barre de navigation supérieure.

2. Sélectionnez le **panneau Test par lot** dans le panneau de droite.

3. Sélectionnez **Importer le jeu de données**.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de l’application LUIS avec Importer le jeu de données en surbrillance](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Choisissez l’emplacement du fichier `pizza-with-machine-learned-entity-test.json`.

5. Nommez le jeu de données `pizza test` et sélectionnez **Terminé**.

    > [!div class="mx-imgBorder"]
    > ![Sélectionner un fichier](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Sélectionnez le bouton **Exécuter**.

7. Sélectionnez **Afficher les résultats**.

8. Passez en revue les résultats dans le graphe et la légende.

## <a name="review-batch-results-for-intents"></a>Passer en revue les résultats du test par lot pour les intentions

Les résultats des tests montrent sous forme de graphique comment les énoncés de test ont été prédits par rapport à la version active.

Le graphique de lot présente quatre quadrants de résultats. À droite du graphique se trouve un filtre. Le filtre contient des intentions et des entités. Lorsque vous sélectionnez un point ou une [section du graphique](luis-concept-batch-test.md#batch-test-results), le ou les énoncés associés s’affichent sous le graphique.

Au passage de la souris sur le graphique, la roulette permet d’agrandir ou de réduire l’affichage du graphique, ce qui est utile en présence de nombreux points très rapprochés.

Le graphique est divisé en quatre quadrants, dont deux s’affichent en rouge.

1. Sélectionnez l’intention **ModifyOrder** dans la liste de filtres.

    > [!div class="mx-imgBorder"]
    > ![Sélectionner l’intention ModifyOrder dans la liste de filtres](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    L’énoncé est prédit comme un **vrai positif** ce qui signifie que l’énoncé correspond bien à sa prédiction positive listée dans le fichier de commandes.

    > [!div class="mx-imgBorder"]
    > ![L’énoncé correspond bien à sa prédiction positive](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    Les coches vertes dans la liste de filtres indiquent également la réussite du test pour chaque intention. Toutes les autres intentions sont listées avec un score positif de 1/1, car l’énoncé a été testé par rapport à chaque intention, sous la forme d’un test négatif pour toutes les intentions non listées dans le test par lot.

1. Sélectionnez l’intention **Confirmation**. Cette intention n’étant pas listée dans le test par lot, il s’agit d’un test négatif de l’énoncé qui est indiqué dans le test par lot.

    > [!div class="mx-imgBorder"]
    > ![L’énoncé a été correctement prédit négatif pour l’intention non listée dans le fichier de commandes](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Le test négatif a réussi, comme indiqué avec le texte vert dans le filtre et la grille.

## <a name="review-batch-test-results-for-entities"></a>Passer en revue les résultats du test par lot pour les entités

L’entité ModifyOrder, en tant qu’entité de machine avec des sous-entités, s’affiche si l’entité de niveau supérieur correspond et montre la manière dont les sous-entités sont prédites.

1. Sélectionnez l’entité **ModifyOrder** dans la liste de filtres, puis le cercle dans la grille.

1. La prédiction d’entité s’affiche sous le graphique. L’affichage comprend des lignes pleines pour les prédictions qui correspondent à l’attente et des lignes en pointillés pour celles qui ne correspondent pas.

    > [!div class="mx-imgBorder"]
    > ![Le parent d’entité a été correctement prédit dans le fichier de commandes](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>Recherche d’erreurs avec un test par lot

Ce tutoriel vous a montré comment exécuter un test et interpréter les résultats. Il n’a pas couvert le concept de test ni la façon de répondre aux tests ayant échoué.

* Veillez à couvrir à la fois les énoncés positifs et négatifs dans votre test, dont les énoncés qui peuvent être prédits pour une intention différente, mais associée.
* Pour les énoncés qui échouent, effectuez les tâches suivantes, puis réexécutez les tests :
    * Passez en revue les exemples actuels pour les intentions et les entités, et confirmez que les exemples d’énoncés de la version active sont corrects à la fois pour l’intention et l’étiquetage d’entité.
    * Ajoutez des fonctionnalités qui aident votre application à prédire des intentions et des entités
    * Ajoutez d’autres exemples d’énoncés positifs
    * Examinez l’équilibre des exemples d’énoncés sur les intentions

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Étape suivante

Le tutoriel a utilisé un test par lot pour valider le modèle actuel.

> [!div class="nextstepaction"]
> [En savoir plus sur les modèles](luis-tutorial-pattern.md)

