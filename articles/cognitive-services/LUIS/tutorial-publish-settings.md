---
title: 'Tutoriel : Paramètres de publication – LUIS'
description: Dans ce tutoriel, changez les paramètres de publication pour améliorer les prédictions.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: d976d06fbf20ea0e64fa01bd94658a27d680bc86
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588777"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>Tutoriel :  Ajouter l’analyse des sentiments comme paramètre de publication

Dans ce tutoriel, changez les paramètres de publication pour extraire l’analyse des sentiments, puis interrogez le point de terminaison LUIS pour voir le sentiment renvoyé d’un énoncé utilisateur.

**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Ajouter l’analyse des sentiments comme paramètre de publication
> * Obtenir le sentiment d’un énoncé à partir du point de terminaison publié

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>L’analyse des sentiments est un paramètre de publication

Les énoncés suivants présentent des exemples de sentiments :

|Sentiments|Score|Énoncé|
|:--|:--|:--|
|négatif|0.01 |La pizza était horrible.|
|positif|0,97 |La pizza au fromage était somptueuse.|

L’analyse des sentiments est un paramètre de publication qui s’applique à chaque énoncé. Une fois configurée, votre application retourne le sentiment d’un énoncé sans que vous ayez à étiqueter les données.

Comme il s’agit d’un paramètre de publication, vous ne le voyez pas étiqueté sur les pages d’intentions ou d’entités. Vous pouvez le voir dans le volet [interactive test](luis-interactive-test.md#view-sentiment-results) (test interactif) ou quand vous effectuez un test au niveau de l’URL de point de terminaison.

## <a name="download-json-file-for-app"></a>Télécharger le fichier JSON pour l’application

Téléchargez et enregistrez le [fichier JSON de l’application](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

## <a name="import-json-file-for-app"></a>Importer le fichier JSON pour l’application

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>Effectuer l’apprentissage de l’application

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurer l’application pour inclure l’analyse des sentiments

1. Sélectionnez **Publuer** dans le menu supérieur. L’analyse des sentiments est un paramètre de publication.

1. Sélectionnez **Emplacement de production**, puis sélectionnez **Modifier les paramètres**.
1. Définissez le paramètre Analyse des sentiments sur **Activé**.

    ![Activer l'Analyse des sentiments comme paramètre de publication](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Obtenir le sentiment d’un énoncé à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Allez à la fin de l’URL dans la barre d’adresses, puis remplacez _YOUR_QUERY_HERE_ par :

    `Deliver 2 of the best cheese pizzas ever!!!`

    Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `OrderPizza` avec l’analyse des sentiments extraite.

    ```json
    {
        "query": "Deliver 2 of the best cheese pizzas ever!!!",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.4659952
                },
                "None": {
                    "score": 0.16901511
                },
                "Confirm": {
                    "score": 0.00421415
                },
                "Greeting": {
                    "score": 0.00109914376
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            },
            "sentiment": {
                "label": "positive",
                "score": 0.861665964
            }
        }
    }
    ```

    L’analyse des sentiments est positive avec un score de 86 %.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informations connexes

* L’analyse des sentiments est fournie par la fonctionnalité [Analyse de texte](../Text-Analytics/index.yml) de Cognitive Services. La fonctionnalité est limitée aux [langues prises en charge](luis-language-support.md#languages-supported) par Analyse de texte.
* [Guide pratique pour entraîner](luis-how-to-train.md)
* [Comment publier](luis-how-to-publish-app.md)
* [Guide pratique pour tester dans le portail LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Étapes suivantes
Ce tutoriel ajoute l’analyse des sentiments en tant que paramètre de publication pour extraire les valeurs de sentiment de l’énoncé dans sa globalité.

> [!div class="nextstepaction"]
> [Réviser les énoncés de point de terminaison dans l’application pour les RH](luis-tutorial-review-endpoint-utterances.md)

