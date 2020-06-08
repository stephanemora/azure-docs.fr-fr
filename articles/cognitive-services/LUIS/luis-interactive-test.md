---
title: Tester une application dans le portail LUIS
description: Utilisez LUIS (Language Understanding) pour travailler en continu sur votre application afin d’affiner et d’améliorer sa compréhension de la langue.
ms.topic: how-to
ms.date: 05/20/2020
ms.openlocfilehash: 86ee90e2d3bb322a4f55439d105941cf43462d3e
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344150"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Tester votre application LUIS dans le portail LUIS

[Le test](luis-concept-test.md) d’une application est un processus itératif. Après avoir formé votre application LUIS, testez-la avec des exemples d’énoncés afin de voir si les intentions et les entités sont reconnues correctement. Si ce n’est pas le cas, apportez des mises à jour à l’application LUIS, puis formez-la et testez-la à nouveau.

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Effectuer l’apprentissage avant le test

1. Connectez-vous au [portail LUIS](https://www.luis.ai) et sélectionnez vos **abonnement** et **ressource de création** pour voir les applications affectées à cette dernière.
1. Ouvrez votre application en sélectionnant son nom dans la page **My Apps** (Mes applications).
1. Pour tester la version la plus récente de l’application active, sélectionnez **Effectuer l’apprentissage** dans le menu supérieur avant de réaliser le test.

## <a name="test-an-utterance"></a>Tester un énoncé

L’énoncé de test ne doit pas être exactement le même que les exemples d’énoncés donnés dans l’application. Il doit inclure le choix de mots, la longueur de l’expression et l’utilisation de l’entité que vous attendez d’un utilisateur.

1. Connectez-vous au [portail LUIS](https://www.luis.ai) et sélectionnez vos **abonnement** et **ressource de création** pour voir les applications affectées à cette dernière.
1. Ouvrez votre application en sélectionnant son nom dans la page **My Apps** (Mes applications).

1. Pour accéder au panneau déroulant **Test**, sélectionnez **Test** dans le panneau supérieur de votre application.

    > [!div class="mx-imgBorder"]
    > ![Page Effectuer l’apprentissage de l’application et la tester](./media/luis-how-to-interactive-test/test.png)

1. Entrez un énoncé dans la zone de texte, puis sélectionnez Entrée. Vous pouvez entrer autant d’énoncés de test que vous le souhaitez dans **Test**, mais uniquement un énoncé à la fois.

1. L’énoncé, son intention principale et son score sont ajoutés à la liste des énoncés sous la zone de texte.

    > [!div class="mx-imgBorder"]
    > ![Le test interactif identifie l’intention incorrecte](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-the-prediction"></a>Inspecter la prédiction

Vous inspectez les détails du résultat du test dans le panneau **Inspecter**.

1. Avec le panneau déroulant **Test** ouvert, sélectionnez **Inspecter** pour consulter un énoncé à comparer.

    > [!div class="mx-imgBorder"]
    > ![Sélectionnez le bouton Inspecter pour afficher plus de détails sur les résultats de test](./media/luis-how-to-interactive-test/inspect.png)

1. Le panneau **Inspection** s’affiche. Le panneau inclut l’intention de score la plus élevée, ainsi que toutes les entités identifiées. Le panneau affiche la prédiction de l’énoncé sélectionné.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran partielle du panneau Inspection de test](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="add-to-example-utterances"></a>Ajouter des exemples d’énoncés

À partir du panneau d’inspection, vous pouvez ajouter l’énoncé de test à une intention en sélectionnant **Ajouter aux exemples d’énoncés**.

## <a name="disable-required-features"></a>Désactiver les fonctionnalités requises

Sélectionnez ce bouton pour afficher la prédiction si la fonctionnalité de l’entité n’était pas requise.

Ce bouton permet de déterminer si l’application formée prédit correctement les entités en fonction des fonctionnalités requises. L’application formée peut mal prédire une entité acquise sur l’ordinateur en se basant sur une étiquette incorrecte d’exemples d’énoncés ou si la fonctionnalité requise ne correspond pas au texte.

## <a name="view-sentiment-results"></a>Afficher les résultats des sentiments

Si **Analyse des sentiments** est configurée sur la page **[Publier](luis-how-to-publish-app.md#enable-sentiment-analysis)** , les résultats du test incluent le sentiment trouvé dans l’énoncé.

## <a name="correct-matched-patterns-intent"></a>Corriger l’intention du modèle mis en correspondance

Si vous utilisez [Modèles](luis-concept-patterns.md) et si l’énoncé a été mis en correspondance avec un modèle, mais que l’intention prédite est incorrecte, sélectionnez le lien **Modifier** en regard du modèle, puis sélectionnez l’intention correcte.

## <a name="compare-with-published-version"></a>Comparer avec la version publiée

Vous pouvez tester la version active de votre application avec la version de [point de terminaison](luis-glossary.md#endpoint) publiée. Dans le panneau **Inspecter**, sélectionnez **Comparer avec la version publiée**. Tout test exécuté sur le modèle publié est déduit du solde de votre quota d’abonnement Azure.

> [!div class="mx-imgBorder"]
> ![Comparer avec la version publiée](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Afficher le JSON de point de terminaison dans le panneau de test
Vous pouvez afficher le JSON de point de terminaison retourné pour la comparaison en sélectionnant la **vue Afficher JSON**.

> [!div class="mx-imgBorder"]
> ![Réponse JSON publiée](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>Paramètres supplémentaires dans le panneau de test

### <a name="luis-endpoint"></a>Point de terminaison LUIS

Si vous avez plusieurs points de terminaison LUIS, utilisez le lien **Paramètres supplémentaires** dans le panneau Test publié pour modifier le point de terminaison utilisé pour le test. Si vous ne savez pas quel point de terminaison utiliser, sélectionnez la valeur par défaut **Starter_Key**.

> [!div class="mx-imgBorder"]
> ![Panneau de test avec le lien Paramètres supplémentaires mis en surbrillance](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Test par lot
Consultez les [concepts](luis-concept-batch-test.md) du test par lot et apprenez [comment](luis-how-to-batch-test.md) tester un lot d’énoncés.

## <a name="next-steps"></a>Étapes suivantes

Si le test indique que votre application LUIS ne reconnaît pas les entités et les intentions correctes, vous pouvez travailler à l’amélioration de la précision de votre application LUIS en étiquetant plus d’énoncés ou en ajoutant des fonctionnalités.

* [Étiqueter des énoncés suggérés avec LUIS](luis-how-to-review-endpoint-utterances.md)
* [Utiliser les fonctionnalités pour améliorer les performances de votre application LUIS](luis-how-to-add-features.md)
