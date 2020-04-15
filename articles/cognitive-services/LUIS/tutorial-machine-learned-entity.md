---
title: 'Didacticiel : extraire des données structurées avec une entité issue de l’apprentissage automatique – LUIS'
description: Extrayez des données structurées d’un énoncé à l’aide de l’entité issue de l’apprentissage automatique. Pour augmenter la précision de l’extraction, ajoutez des sous-composants avec des descripteurs et des contraintes.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 52bf2fb0b9f37e0c731a46c0aaf8b6c5e7f0e911
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545857"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Tutoriel : Extraire des données structurées à partir d’un énoncé utilisateur avec des entités issues du Machine Learning dans LUIS

Dans ce didacticiel, vous allez extraire des données structurées d’un énoncé à l’aide de l’entité issue de l’apprentissage automatique.

L’entité issue de l’apprentissage automatique prend en charge le concept de [décomposition du modèle](luis-concept-model.md#v3-authoring-model-decomposition) en fournissant des entités de sous-composants avec leurs descripteurs et contraintes.

**Dans ce tutoriel, vous allez découvrir comment :**

> [!div class="checklist"]
> * Importer l’exemple d’application
> * Ajouter une entité issue de l’apprentissage automatique
> * Ajouter un sous-composant
> * Ajouter un descripteur du sous-composant
> * Ajouter une contrainte du sous-composant
> * Entraîner une application
> * Tester une application
> * Publier une application
> * Obtenir une prédiction d’entité d’un point de terminaison

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Pourquoi utiliser une entité issue de l’apprentissage automatique ?

Ce didacticiel ajoute une entité issue de l’apprentissage automatique pour extraire des données d’un énoncé.

L’entité définit les données à extraire de l’énoncé. Cela inclut l’attribution aux données d’un nom, d’un type (si possible), d’une résolution en cas d’ambiguïté, et du texte exact qui les compose.

Pour définir l’entité, vous devez la créer, puis étiqueter le texte représentant l’entité dans les exemples d’énoncés de toutes les intentions. Ces exemples étiquetés enseignent à LUIS la nature de l’entité et l’emplacement où elle se trouve dans un énoncé.

## <a name="entity-decomposability-is-important"></a>La possibilité de décomposer l’entité est importante

La possibilité de décomposer l’entité est importante tant pour la prédiction d’intention que pour l’extraction de données avec l’entité.

Commencez avec une entité issue de l’apprentissage automatique, qui est l’entité de début et de niveau supérieur pour l’extraction de données. Décomposez ensuite l’entité de façon à obtenir les éléments dont l’application cliente a besoin.

Si vous ignorez à quel point votre entité doit être détaillée, lorsque vous démarrez votre application, la meilleure pratique consiste à commencer avec une entité issue de l’apprentissage automatique, puis à décomposer celle-ci en sous-composants à mesure que votre application mûrit.

Ici, vous allez créer une entité issue de l’apprentissage automatique pour représenter une commande pour une application de pizza. La commande doit contenir tous les éléments nécessaires à son traitement commande. Pour commencer, l’entité va extraire le texte relatif à la commande, notamment la taille et la quantité.

Un énoncé pour `Please deliver one large cheese pizza to me` doit extraire `one large cheese pizza` comme la commande, puis extraire également `1` et `large`.

Vous pouvez ajouter une autre décomposition, en créant par exemple des sous-composants correspondant à la garniture et au type de pâte. À l’issue de ce didacticiel, vous ne devriez pas hésiter à ajouter ces sous-composants à votre entité `Order` existante.

## <a name="import-example-json-to-begin-app"></a>Exemple d’importation .json pour commencer une application

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>Étiqueter des entités de texte dans des exemples d’énoncés

Pour extraire des détails d’une commande de pizza, créez une entité `Order` issue de l’apprentissage automatique de niveau supérieur.

1. Dans la page **intentions**, sélectionnez l’intention **OrderPizza**.

1. Dans la liste exemples d’énoncés, sélectionnez l’énoncé suivant.

    |Exemple d’énoncé de commande|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Commencez à sélectionner juste devant le texte le plus à gauche de `pickup` (1), puis allez juste au-delà du texte le plus à droite, `anchovies` (2 – cela met fin au processus d’étiquetage). Un menu contextuel s’affiche. Dans le menu contextuel, entrez le nom de l’entité en tant que `Order` (3). Sélectionnez ensuite `Order - Create new entity` dans la liste (4).

    ![Étiqueter le début et de fin du texte pour une commande complète](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Une entité n’est pas toujours l’intégralité d’un énoncé. Dans ce cas précis, `pickup` indique comment la commande doit être reçue. D’un point de vue conceptuel, `pickup` doit faire partie de l’entité étiquetée pour la commande.

1. Dans la zone **Choisir un type d’entité**, sélectionnez **Ajouter une structure** puis **Suivant**. Une structure est nécessaire pour permettre l’ajout de sous-composants tels que la taille et la quantité.

    ![Ajouter une structure à une entité](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Dans la zone **Créer une entité issue de l’apprentissage automatique**, dans la zone **Structure**, ajoutez `Size`, puis sélectionnez Entrée.
1. Pour ajouter un **descripteur**, sélectionnez le signe `+` dans la zone **Descripteurs**, puis sélectionnez **Créer une liste d’expressions**.

1. Dans la zone **Créer un descripteur de liste d’expressions**, entrez le nom `SizeDescriptor`, puis entrez les valeurs des options `small`, `medium` et `large`. Quand la zone **Suggestions** se remplit, sélectionnez `extra large` et `xl`. Sélectionnez **Terminé** pour créer la liste d’expressions.

    Ce descripteur de liste d’expressions aide le sous-composant `Size` à trouver des mots associés à la taille en lui fournissant un exemple de mot. Cette liste n’a pas besoin d’inclure toutes les mots associés à la taille, mais doit inclure des mots censés indiquer la taille.

    ![Créer un descripteur pour le sous-composant taille](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. Dans la fenêtre **Créer une entité issue de l’apprentissage automatique**, sélectionnez **Créer** pour achever la création du sous-composant `Size`.

    L’entité `Order` avec un composant `Size` est créée, mais seule l’entité `Order` a été appliquée à l’énoncé. Vous devez étiqueter le texte de l’entité `Size` dans l’exemple d’énoncé.

1. Dans le même exemple d’énoncé, étiquetez le sous-composant **taille** de `large` en sélectionnant le mot, puis l’entité **Size** dans la liste déroulante.

    ![Étiquetez l’entité Size pour le texte dans l’énoncé.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    La ligne sous le texte est continue, car l’étiquetage et la prédiction correspondent, ce qui est dû au fait que vous avez _explicitement_ étiqueté le texte.

1. Étiquetez l’entité `Order` dans les énoncés restants, ainsi que l’entité Size. Les crochets dans le texte indiquent l’entité `Order` étiquetée et l’entité `Size` à l’intérieur de celle-ci.

    |Exemples d’énoncés de commande|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Créez une entité et des sous-composants dans tous les exemples d’énoncés restants.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Comment traiter des données implicites telles que la lettre `a` impliquant une seule pizza ? Ou l’absence de `pickup` et `delivery` pour indiquer où la pizza est attendue ? Ou l’absence de taille pour indiquer votre taille par défaut, petite ou grande ? Envisagez d’inclure la gestion des données implicites dans les règles métier de l’application cliente, à la place de, ou en plus de LUIS.

1. Pour effectuer l’apprentissage de l’application, sélectionnez **Former**. La formation applique les modifications, telles que les nouvelles entités et les énoncés étiquetés, au modèle actif.

1. Une fois l’entraînement terminé, ajoutez un nouvel exemple d’énoncé à l’intention pour voir comment LUIS comprend l’entité issue du Machine Learning.

    |Exemple d’énoncé de commande|
    |--|
    |`pickup XL meat lovers pizza`|

    L’entité supérieure globale, `Order`, est étiquetée. Le sous-composant `Size` l’est également avec des lignes en pointillés.

    ![Nouvel exemple d’énoncé prédit avec une entité](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    La ligne en pointillés indique la prédiction.

1. Pour modifier la prédiction dans une entité étiquetée, sélectionnez la ligne, puis choisissez **Confirmer les prédictions d’entité**.

    ![Acceptez la prédiction en sélectionnant Confirmer la prédiction d’entité.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    À ce stade, l’entité issue de l’apprentissage automatique fonctionne, car elle peut trouver l’entité dans un nouvel exemple d’énoncé. Lorsque vous ajoutez des exemples d’énoncés, si l’entité n’est pas correctement prédite, étiquetez-la, ainsi que les sous-composants. Si l’entité est correctement prédite, veillez à confirmer les prédictions.

## <a name="add-prebuilt-number-to-help-extract-data"></a>Ajouter un numéro prédéfini pour faciliter l’extraction des données

Les informations de commande doivent également inclure le nombre d’exemplaires d’un article dans la commande, par exemple le nombre de pizzas. Pour extraire ces données, un nouveau sous-composant issu de l’apprentissage automatique doit être ajouté à `Order`, qui nécessite une contrainte de nombre prédéfini. Si vous limitez l’entité à un nombre prédéfini, elle trouve et extrait des nombres, que le texte soit un chiffre `2` ou un mot `two`.

Commencez par ajouter l’entité du numéro prédéfini à l’application.

1. Sélectionnez **Entités** dans le menu de gauche, puis sélectionnez **+ Ajouter une entité prédéfinie**.

1. Dans la zone **Ajouter des entités prédéfinies**, recherchez et sélectionnez **nombre**, puis **Terminé**.

    ![Ajouter une entité prédéfinie](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    L’entité prédéfinie est ajoutée à l’application, mais ne constitue pas encore une contrainte.

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Créer une entité de sous-composant avec une contrainte pour faciliter l’extraction de données

L’entité `Order` doit avoir un sous-composant `Quantity` pour déterminer le nombre d’articles commandés. La quantité doit être limitée à un nombre afin que les données extraites soient immédiatement accessibles à l’application cliente par nom.

Une contrainte est appliquée comme une correspondance de texte, soit avec une correspondance exacte (telle une entité de liste), soit via des expressions régulières (telle une entité d’expression régulière ou une entité prédéfinie).

Quand vous utilisez une contrainte, seul le texte correspondant à cette contrainte est extrait.

1. Sélectionnez **Entités** puis l’entité `Order`.
1. Sélectionnez **+ Ajouter un composant**, entrez le nom `Quantity`, puis sélectionnez Entrée pour ajouter le nouveau sous-composant à l’entité `Order`.
1. Après la notification de réussite, dans les **Options avancées**, sélectionnez le crayon Contrainte.
1. Dans la liste déroulante, sélectionnez le nombre prédéfini.

    ![Créer une entité Quantity avec un nombre prédéfini en tant que contrainte.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    L’entité `Quantity` est appliquée quand le texte correspond à l’entité de nombre prédéfini.

    L’entité contrainte est créée mais pas encore appliquée aux exemples d’énoncés.

    > [!NOTE]
    > Un sous-composant peut être imbriqué dans un sous-composant de niveau supérieur, jusqu’à 5 niveaux. Bien que cela ne soit pas illustré dans cet article, il est disponible à partir du portail et de l’API.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Étiqueter l’exemple d’énoncé pour que LUIS puisse en savoir plus sur l’entité

1. Sélectionnez **Intentions** dans le volet de navigation gauche, puis choisissez l’intention **OrderPizza**. Les trois nombres dans les énoncés suivants sont étiquetés, et repérés visuellement sous la ligne d’entité `Order`. Ce niveau inférieur signifie que les entités sont trouvées, mais qu’elles ne sont pas considérées comme faisant partie de l’entité `Order`.

    ![Le nombre prédéfini est trouvé mais n’est pas encore considéré comme séparé de l’entité Order.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Étiquetez les nombres avec l’entité `Quantity` en sélectionnant la `2` dans l’exemple d’énoncé, puis en sélectionnant `Quantity` dans la liste. Étiquetez le `6` et le `1` dans le même exemple d’énoncé.

    ![Étiquetez le texte avec l’entité Quantity.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Effectuer l’apprentissage de l’application pour appliquer les modifications d’entité à l’application

Sélectionnez **former** pour effectuer l’apprentissage l’application avec ces nouveaux énoncés. Après l’entraînement, le sous-composant `Quantity` est correctement prédit dans le composant `Order`. Le fait que cette prédiction soit correcte est indiqué par un trait plein.

![Effectuez l’apprentissage de l’application, puis examinez les exemples d’énoncés.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

À ce stade, la commande contient des détails qui peuvent être extraits (taille, quantité et texte de la commande totale). Il y a un affinement supplémentaire de l’entité `Order`, comme les garnitures de pizza, le type de croûte et les à côtés. Chacun de ces éléments doit être créé en tant que sous-composant de l’entité `Order`.

## <a name="test-the-app-to-validate-the-changes"></a>Tester l’application pour valider les modifications

Testez l’application à l’aide du volet **Test** interactif. Ce processus vous permet d’entrer un nouvel énoncé, puis d’afficher les résultats de la prédiction pour voir le fonctionnement de l’application active et formée. La prédiction d’intention doit être assez fiable (au-dessus de 70 %) et l’extraction d’entité doit récupérer au moins l’entité `Order`. Les détails de l’entité Order peuvent être manquants, car 5 énoncés ne suffisent pas à gérer tous les cas.

1. Sélectionnez **Tester** dans la barre de navigation supérieure.
1. Entrez l’énoncé `deliver a medium veggie pizza`, puis sélectionnez Entrée. Le modèle actif a prédit l’intention correcte avec une fiabilité supérieure à 70 %.

    ![Entrez un nouvel énoncé pour tester l’intention.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Sélectionnez **Inspecter** pour voir les prédictions d’entité.

    ![Affichez les prédictions d’entité dans le panneau de test interactif.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    La taille a été correctement identifiée. N’oubliez pas que les exemples d’énoncés dans l’intention `OrderPizza` ne comprennent pas d’exemple de taille `medium`, mais utilisent un descripteur d’une liste d’expressions `SizeDescriptor` incluant medium.

    La quantité n’est pas correctement prédite. Vous pouvez corriger cela dans votre application cliente en définissant une taille par défaut égale à un (1) si aucune taille n’est retournée dans la prédiction LUIS.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publier l’application pour y accéder à partir du point de terminaison HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Obtenir une prédiction d’intention et d’entité à partir d’un point de terminaison HTTP

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Allez à la fin de l’URL dans la barre d’adresse, puis remplacez _YOUR_QUERY_HERE_ par la requête que vous avez entrée dans le panneau de test interactif.

    `deliver a medium veggie pizza`

    Le dernier paramètre de la chaîne de requête est `query`, l’énoncé est **query**.

    ```json
    {
        "query": "deliver a medium veggie pizza",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "medium"
                        ],
                        "$instance": {
                            "Size": [
                                {
                                    "type": "Size",
                                    "text": "medium",
                                    "startIndex": 10,
                                    "length": 6,
                                    "score": 0.9955588,
                                    "modelTypeId": 1,
                                    "modelType": "Entity Extractor",
                                    "recognitionSources": [
                                        "model"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "$instance": {
                    "Order": [
                        {
                            "type": "Order",
                            "text": "a medium veggie pizza",
                            "startIndex": 8,
                            "length": 21,
                            "score": 0.7983857,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informations connexes

* [Didacticiel – intentions](luis-quickstart-intents-only.md)
* [Concept – entités](luis-concept-entity-types.md) informations conceptuelles
* [Concept – fonctionnalités](luis-concept-feature.md) informations conceptuelles
* [Guide pratique pour entraîner](luis-how-to-train.md)
* [Comment publier](luis-how-to-publish-app.md)
* [Guide pratique pour tester dans le portail LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, l’application utilise une entité issue de l’apprentissage automatique pour rechercher l’intention d’un énoncé d’utilisateur et extraire des détails de cet énoncé. L’utilisation de l’entité issue de l’apprentissage automatique vous permet d’en décomposer les détails.

> [!div class="nextstepaction"]
> [Ajouter une entité keyphrase prédéfinie](luis-quickstart-intent-and-key-phrase.md)
