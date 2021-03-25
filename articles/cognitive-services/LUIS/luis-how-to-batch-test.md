---
title: Guide pratique pour effectuer un test par lot - LUIS
titleSuffix: Azure Cognitive Services
description: Utilisez des jeux de tests par lot Language Understanding (LUIS) pour rechercher des énoncés avec des intentions et des entités incorrectes.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/29/2020
ms.openlocfilehash: b297330f3562babf9e83d36934827f7b92d5ea35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787010"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Test par lot avec un ensemble d’exemples d’énoncés

Les tests par lot valident votre version entraînée active afin de mesurer la précision des prédictions. Un test par lot vous permet de visualiser la précision de chaque intention et entité dans votre version active. Passez en revue les résultats des tests par lot pour prendre les mesures appropriées afin d’améliorer la précision, par exemple ajouter d’autres exemples d’énoncés à une intention si votre application échoue fréquemment dans sa tentative d’identification de l’intention ou étiqueter des entités dans l’énoncé.

## <a name="group-data-for-batch-test"></a>Regrouper les données pour le test par lot

Il est important que les énoncés utilisés pour les tests par lot soient nouveaux pour LUIS. Si vous avez un jeu de données d’énoncés, divisez les énoncés en trois jeux : exemples d’énoncés ajoutés à une intention, énoncés reçus du point de terminaison publié et énoncés utilisés pour exécuter des tests par lot sur LUIS une fois qu’il a été entraîné.

Le fichier de lots JSON doit inclure des énoncés avec les entités issues du machine learning de niveau supérieur étiquetées, notamment les positions de début et de fin. Les énoncés ne doivent pas faire partie des exemples déjà présents dans l’application. Il doit s’agir d’énoncés que vous souhaitez prédire de manière positive pour l’intention et les entités.

Vous pouvez séparer les tests par l’intention et/ou l’entité ou avoir tous les tests (jusqu’à 1 000 énoncés) dans le même fichier. 

### <a name="common-errors-importing-a-batch"></a>Erreurs courantes d’importation de lot

Si vous rencontrez des erreurs lors du chargement de votre fichier de commandes dans LUIS, vérifiez les problèmes courants suivants :

* Plus de 1 000 énoncés dans un fichier de commandes
* Objet JSON d’énoncé n’ayant pas de propriété d’entités. La propriété peut être un tableau vide.
* Mot(s) étiqueté(s) dans plusieurs entités
* Étiquettes d’entité commençant ou se terminant par un espace.

## <a name="fixing-batch-errors"></a>Correction des erreurs de lot

S’il existe des erreurs dans le test par lot, vous pouvez ajouter des énoncés à une intention et/ou étiqueter davantage d’énoncés avec l’entité afin d’aider LUIS à effectuer la distinction entre les intentions. Si vous avez ajouté des énoncés et que vous les avez étiquetés, mais que des erreurs se produisent encore dans le test par lot, essayez d’ajouter une fonctionnalité de [liste d’expressions](luis-concept-feature.md) avec un vocabulaire propre au domaine pour aider LUIS à apprendre plus rapidement.


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>Test en lots à l’aide du portail LUIS 

### <a name="import-and-train-an-example-app"></a>Importer et former un exemple d’application

Importez une application qui prend une commande de pizza telle que `1 pepperoni pizza on thin crust`.

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Connectez-vous au [portail LUIS](https://www.luis.ai) et sélectionnez vos **abonnement** et **ressource de création** pour voir les applications affectées à cette dernière.
1. Sélectionnez la flèche en regard de **nouvelle application**, puis cliquez sur **Importer en tant que JSON** pour importer le code JSON dans une nouvelle application. Nommez l'application `Pizza app`.


1. Sélectionnez **Entraîner** dans le coin supérieur droit de la barre de navigation pour entraîner l’application.


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>Fichier de test par lot

L’exemple JSON comprend un énoncé avec une entité étiquetée pour illustrer à quoi ressemble un fichier de test. Dans vos propres tests, vous devez avoir de nombreux énoncés avec l’intention correcte et l’entité issue du machine learning étiquetée.

1. Créez `pizza-with-machine-learned-entity-test.json` dans un éditeur de texte ou [téléchargez-le](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true).

2. Dans le fichier de commandes au format JSON, ajoutez un énoncé avec l’**intention** à prédire dans le test.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Exécuter le test par lot

1. Sélectionnez **Test** dans la barre de navigation supérieure.

2. Sélectionnez le **panneau Test par lot** dans le panneau de droite.

    ![Lien Test par lot](./media/luis-how-to-batch-test/batch-testing-link.png)

3. Sélectionnez **Importer**. Dans la boîte de dialogue qui s’affiche, sélectionnez **Sélectionner un fichier** et recherchez un fichier JSON qui ne contient *pas plus de 1 000* énoncés à tester.

    Les erreurs d’importation sont signalées dans une barre de notification rouge en haut du navigateur. En cas d’erreur dans l’importation, aucun jeu de données n’est créé. Pour plus d’informations, consultez [Erreurs courantes](#common-errors-importing-a-batch).

4. Choisissez l’emplacement du fichier `pizza-with-machine-learned-entity-test.json`.

5. Nommez le jeu de données `pizza test` et sélectionnez **Terminé**.

6. Sélectionnez le bouton **Run**. Après l’exécution du test par lot, sélectionnez **Afficher les résultats**. 

    > [!TIP]
    > * Si vous sélectionnez **Télécharger** vous téléchargerez le même fichier que celui que vous avez chargé.
    > * Si vous voyez que le test par lot a échoué, au moins une intention d’énoncé ne correspondait pas à la prédiction.

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>Passer en revue les résultats du test par lot pour les intentions

Pour passer en revue les résultats du test par lot, sélectionnez **Afficher les résultats**. Les résultats des tests montrent sous forme de graphique comment les énoncés de test ont été prédits par rapport à la version active.

Le graphique de lot présente quatre quadrants de résultats. À droite du graphique se trouve un filtre. Le filtre contient des intentions et des entités. Lorsque vous sélectionnez un point ou une [section du graphique](#review-batch-results-for-intents), le ou les énoncés associés s’affichent sous le graphique.

Au passage de la souris sur le graphique, la roulette permet d’agrandir ou de réduire l’affichage du graphique, ce qui est utile en présence de nombreux points très rapprochés.

Le graphique est divisé en quatre quadrants, dont deux s’affichent en rouge.

1. Sélectionnez l’intention **ModifyOrder** dans la liste de filtres. L’énoncé est prédit comme un **vrai positif** ce qui signifie que l’énoncé correspond bien à sa prédiction positive listée dans le fichier de commandes.

    > [!div class="mx-imgBorder"]
    > ![L’énoncé correspond bien à sa prédiction positive](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    Les coches vertes dans la liste de filtres indiquent également la réussite du test pour chaque intention. Toutes les autres intentions sont listées avec un score positif de 1/1, car l’énoncé a été testé par rapport à chaque intention, sous la forme d’un test négatif pour toutes les intentions non listées dans le test par lot.

1. Sélectionnez l’intention **Confirmation**. Cette intention n’étant pas listée dans le test par lot, il s’agit d’un test négatif de l’énoncé qui est indiqué dans le test par lot.

    > [!div class="mx-imgBorder"]
    > ![L’énoncé a été correctement prédit négatif pour l’intention non listée dans le fichier de commandes](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Le test négatif a réussi, comme indiqué avec le texte vert dans le filtre et la grille.

### <a name="review-batch-test-results-for-entities"></a>Passer en revue les résultats du test par lot pour les entités

L’entité ModifyOrder, en tant qu’entité de machine avec des sous-entités, s’affiche si l’entité de niveau supérieur correspond et montre la manière dont les sous-entités sont prédites.

1. Sélectionnez l’entité **ModifyOrder** dans la liste de filtres, puis le cercle dans la grille.

1. La prédiction d’entité s’affiche sous le graphique. L’affichage comprend des lignes pleines pour les prédictions qui correspondent à l’attente et des lignes en pointillés pour celles qui ne correspondent pas.

    > [!div class="mx-imgBorder"]
    > ![Le parent d’entité a été correctement prédit dans le fichier de commandes](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>Filtrer les résultats du graphique

Pour filtrer le graphique en fonction d’une intention ou d’une entité spécifique, sélectionnez l’intention ou l’entité sur le côté droit du panneau de filtrage. Les points de données et leur répartition sont mis à jour dans le graphique en fonction de votre sélection.

![Résultat du test par lot visualisé](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>Exemples de résultats de graphique

Dans le graphique dans le portail LUIS, vous pouvez effectuer les actions suivantes :
 
#### <a name="view-single-point-utterance-data"></a>Afficher les données d’énoncé d’un point unique

Dans le graphique, pointez sur un point de données pour afficher le score de certitude de la prédiction. Sélectionnez un point de données pour récupérer l’énoncé correspondant dans la liste des énoncés au bas de la page.

![Énoncé sélectionné](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>Afficher les données de section

Dans le graphique à quatre sections, sélectionnez le nom de section, comme **Faux positif** en haut à droite du graphique. Sous le graphique, tous les énoncés dans cette section s’affichent dans une liste sous le graphique.

![Énoncés sélectionnés par section](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Dans l’image précédente, l’énoncé `switch on` est étiqueté avec l’intention TurnAllOn, mais a reçu la prédiction d’intention None. Cela indique que l’intention TurnAllOn a besoin de davantage d’exemples d’énoncés pour rendre la prédiction attendue.

Les deux sections du graphique en rouge indiquent les énoncés ne correspondant pas à la prédiction attendue. Cela indique les énoncés pour lesquels LUIS a besoin de davantage de formation.

Les deux sections du graphique en vert correspondent à la prédiction attendue.

## <a name="batch-testing-using-the-rest-api"></a>Test par lot à l’aide de l’API REST 

LUIS vous permet de tester par lots à l’aide du portail LUIS et de l’API REST. Les points de terminaison de l’API REST sont répertoriés ci-dessous. Pour plus d’informations sur les tests par lots à l’aide du portail LUIS, consultez [Tutoriel : jeux de données de test par lot](). Utilisez les URL complètes ci-dessous, en remplaçant les valeurs d’espace réservé par votre propre clé et point de terminaison de prédiction LUIS. 

N’oubliez pas d’ajouter votre clé LUIS à `Ocp-Apim-Subscription-Key` dans l’en-tête et définissez `Content-Type` sur `application/json`.

### <a name="start-a-batch-test"></a>Démarrer un test par lot

Démarrez un test par lot à l’aide d’un ID de version de l’application ou d’un emplacement de publication. Envoyez une demande de **publication** à l’un des formats de point de terminaison suivants. Incluez votre fichier de commandes dans le corps de la demande.

Emplacement de publication
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

ID de version de l’application
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

Ces points de terminaison retournent un ID d’opération que vous allez utiliser pour vérifier l’état et obtenir des résultats. 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>Obtenir l’état d’un test par lot en cours

Utilisez l’ID d’opération du test par lot que vous avez démarré pour connaître son état à partir des formats de point de terminaison suivants : 

Emplacement de publication
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

ID de version de l’application
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>Obtenir les résultats d’un test par lot

Utilisez l’ID d’opération du test par lot que vous avez démarré pour obtenir ses résultats à partir des formats de point de terminaison suivants : 

Emplacement de publication
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

ID de version de l’application
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>Fichier de commandes des énoncés

Envoyez un fichier d’énoncés, appelé *jeu de données*, pour les tests par lot. Le jeu de données est un fichier au format JSON contenant au maximum 1 000 énoncés étiquetés. Vous pouvez tester jusqu’à 10 jeux de données dans une application. Si vous avez besoin d’en tester davantage, supprimez un jeu de données, puis ajoutez-en un nouveau. Toutes les entités personnalisées de ce modèle s’affichent dans le filtre d’entités de test de lot, même s’il n’y a aucune entité correspondante dans les données du fichier de commandes.

Le fichier de traitement par lot est constitué d’énoncés. Chaque énoncé doit avoir une prédiction d’intention attendue ainsi que toute [entité de machine-learning](luis-concept-entity-types.md#types-of-entities) censée être détectée.

### <a name="batch-syntax-template-for-intents-with-entities"></a>Modèle de syntaxe de lot pour les intentions avec des entités

Utilisez le modèle suivant pour démarrer votre fichier de commandes :

```JSON
{
    "LabeledTestSetUtterances": [
        {
            "text": "play a song",
            "intent": "play_music",
            "entities": [
                {
                    "entity": "song_parent",
                    "startPos": 0,
                    "endPos": 15,
                    "children": [
                        {
                            "entity": "pre_song",
                            "startPos": 0,
                            "endPos": 3
                        },
                        {
                            "entity": "song_info",
                            "startPos": 5,
                            "endPos": 15
                        }
                    ]
                }
            ]
        }
    ]
}

```

Le fichier de commandes utilise les propriétés **startPos** et **endPos** pour noter le début et la fin d’une entité. Les valeurs commencent à zéro ; elles ne doivent ni commencer ni se terminer par un espace. Les journaux d’activité de requêtes, eux, utilisent les propriétés startIndex et endIndex.

Si vous ne souhaitez pas tester d’entités, incluez la propriété `entities` et définissez la valeur en tant que tableau vide, `[]`.

### <a name="rest-api-batch-test-results"></a>Résultats du test par lot de l’API REST

Plusieurs objets sont retournés par l’API :

* Informations sur les modèles d’intentions et d’entités, tels que précision, rappel et score-F.
* Informations sur les modèles d’entités, tels que la précision, le rappel et le score F, pour chaque entité 
  * À l’aide de l’indicateur `verbose`, vous pouvez obtenir plus d’informations sur l’entité, comme `entityTextFScore` et `entityTypeFScore`.
* Fournir des énoncés avec les noms d’intentions prédits et étiquetés
* Liste d’entités positives erronées et liste d’entités de faux négatifs.

## <a name="next-steps"></a>Étapes suivantes

Si le test indique que votre application LUIS ne reconnaît pas les entités et les intentions correctes, vous pouvez travailler à l’amélioration des performances de votre application LUIS en étiquetant plus d’énoncés ou en ajoutant des fonctionnalités.

* [Étiqueter des énoncés suggérés avec LUIS](luis-how-to-review-endpoint-utterances.md)
* [Utiliser les fonctionnalités pour améliorer les performances de votre application LUIS](luis-how-to-add-features.md)
