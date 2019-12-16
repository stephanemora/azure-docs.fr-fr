---
title: 'Didacticiel : Révision des énoncés de point de terminaison - LUIS'
titleSuffix: Azure Cognitive Services
description: Améliorez les prédictions de l’application en vérifiant ou corrigeant les énoncés reçus par le point de terminaison HTTP de LUIS dont ce dernier n’est pas sûr. Certains énoncés peuvent devoir faire l’objet d’une vérification d’intention, d’autres d’une vérification d’entité.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: diberry
ms.openlocfilehash: 0a4d2a3345ce4f69d4492d1a782b778b1ee3bf4c
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895665"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Didacticiel : Corriger les prédictions incertaines en révisant les énoncés de point de terminaison
Dans ce tutoriel, améliorez les prédictions de l’application en vérifiant ou corrigeant les énoncés reçus via le point de terminaison HTTPS de LUIS dont ce dernier n’est pas sûr. Vous devez examiner les énoncés de point de terminaison régulièrement dans le cadre de la maintenance LUIS planifiée.

Ce processus de révision permet à LUIS de découvrir le domaine de votre application. LUIS sélectionne les énoncés qui apparaissent dans la liste de révision. Cette liste est :

* Spécifique à l’application.
* Est destinée à améliorer la précision de prédiction de l’application.
* Doit être révisée régulièrement.

En passant en revue les énoncés de point de terminaison, vous vérifiez ou corrigez l’intention prédite de l’énoncé.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importer l’exemple d’application
> * Réviser les énoncés de point de terminaison
> * Entraîner et publier une application.
> * Interroger un point de terminaison de l’application pour voir la réponse JSON de LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importer l’exemple d’application

Utilisez les étapes suivantes pour importer une application.

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json).

1. Dans le [portail LUIS en préversion](https://preview.luis-ai), importez le fichier .json dans une nouvelle application.

1. À partir de la section **Manage (Gérer)** , sous l’onglet **Versions**, clonez la version et nommez-la `review`.

    > [!TIP]
    > Le clonage dans une nouvelle version est considéré comme une bonne pratique avant la modification de votre application. Lorsque vous en avez terminé avec une version, exportez-la (au format .json ou .lu), puis vérifiez le fichier dans votre système de contrôle de code source.


1. Pour effectuer l’apprentissage de l’application, sélectionnez **Former**.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publier l’application pour y accéder à partir du point de terminaison HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Ajouter des énoncés au point de terminaison

Dans cette application, vous avez des intentions et des entités, mais vous n’avez pas d’utilisation du point de terminaison. Cette utilisation du point de terminaison est nécessaire pour améliorer l’application avec le passage en revue des énoncés du point de terminaison.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Utilisez le point de terminaison pour ajouter les énoncés suivants.

    |Énoncé du point de terminaison|Intention alignée|
    |--|--|
    |`I'm looking for a job with Natural Language Processing`|`GetJobInformation`|
    |`I want to cancel on March 3`|`Utilities.Cancel`|
    |`When were HRF-123456 and hrf-234567 published in the last year?`|`FindForm`|
    |`shift 123-45-6789 from Z-1242 to T-54672`|`MoveEmployee`|
    |`Please relocation jill-jones@mycompany.com from x-2345 to g-23456`|`MoveEmployee`|
    |`Here is my c.v. for the programmer job`|`ApplyForJob`|
    |`This is the lead welder paperwork.`|`ApplyForJob`|
    |`does form hrf-123456 cover the new dental benefits and medical plan`|`FindForm`|
    |`Jill Jones work with the media team on the public portal was amazing`|`EmployeeFeedback`|

    Il existe un seul pool d’énoncés à réviser, quelle que soit la version que vous modifiez ou la version de l’application publiée au point de terminaison.

## <a name="review-endpoint-utterances"></a>Réviser les énoncés de point de terminaison

Passez en revue les énoncés du point de terminaison pour une intention correctement alignée. Bien qu’il n’existe qu’un seul pool d’énoncés à examiner dans toutes les versions, le processus d’alignement correct de l’intention ajoute l’exemple d’énoncé seulement au _modèle actif_ actuel.

1. Dans la section **Build** du portail, sélectionnez **Réviser les énoncés de point de terminaison** dans le volet de navigation gauche. La liste est filtrée pour intention **ApplyForJob**.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran du bouton Réviser les énoncés de point de terminaison dans le volet de navigation gauche](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    Ce énoncé, `I'm looking for a job with Natural Language Processing`, n’a pas la bonne intention.

1.  Pour aligner cet énoncé, sur la ligne de l’énoncé, sélectionnez l’**intention alignée** correcte de `GetJobInformation`. Ajoutez l’énoncé modifié à l’application en sélectionnant la coche.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran du bouton Réviser les énoncés de point de terminaison dans le volet de navigation gauche](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    Passez en revue les énoncés restants dans cette intention but, en corrigeant l’intention alignée de façon appropriée. Utilisez le tableau des énoncés initiaux dans ce tutoriel pour voir l’intention alignée.

    La liste **Réviser les énoncés de point de terminaison** ne doit plus contenir les énoncés corrigés. Si d’autres énoncés apparaissent, continuez d’examiner la liste, en corrigeant les intentions alignées jusqu’à ce que la liste soit vide.

    Les corrections d’étiquetage de l’entité sont effectuées une fois l’intention alignée, dans la page Détails de l’intention.

1. Former et publier à nouveau l’application.

## <a name="get-intent-prediction-from-endpoint"></a>Obtenir une prédiction d’intention à partir du point de terminaison

Pour vérifier que les exemples d’énoncés correctement alignés ont amélioré la prédiction de l’application, essayez un énoncé proche de l’énoncé corrigé.

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Are there any natural language processing jobs in my department right now?`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**.

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.903607249
                },
                "EmployeeFeedback": {
                    "score": 0.0312187821
                },
                "ApplyForJob": {
                    "score": 0.0230276529
                },
                "MoveEmployee": {
                    "score": 0.008322801
                },
                "Utilities.Stop": {
                    "score": 0.004480808
                },
                "FindForm": {
                    "score": 0.00425248267
                },
                "Utilities.StartOver": {
                    "score": 0.004224336
                },
                "Utilities.Help": {
                    "score": 0.00373591436
                },
                "None": {
                    "score": 0.0034621188
                },
                "Utilities.Cancel": {
                    "score": 0.00230977475
                },
                "Utilities.Confirm": {
                    "score": 0.00112078607
                }
            },
            "entities": {
                "keyPhrase": [
                    "natural language processing jobs",
                    "department"
                ],
                "datetimeV2": [
                    {
                        "type": "datetime",
                        "values": [
                            {
                                "timex": "PRESENT_REF",
                                "resolution": [
                                    {
                                        "value": "2019-12-05 23:23:53"
                                    }
                                ]
                            }
                        ]
                    }
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "natural language processing jobs",
                            "startIndex": 14,
                            "length": 32,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "builtin.keyPhrase",
                            "text": "department",
                            "startIndex": 53,
                            "length": 10,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "datetimeV2": [
                        {
                            "type": "builtin.datetimeV2.datetime",
                            "text": "right now",
                            "startIndex": 64,
                            "length": 9,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
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

   Maintenant que les énoncés incertains sont correctement alignés, l’intention correcte a été prédite avec un **score élevé**.

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>La révision peut-elle être remplacée en ajoutant plus d’énoncés ?
Vous vous demandez sûrement pourquoi ne pas ajouter plus d’énoncés d’exemple. Quel est le but de la révision des énoncés de point de terminaison ? Dans une application LUIS réaliste, les énoncés de point de terminaison émanent d’utilisateurs avec des choix de mots et une disposition que vous n’avez pas encore utilisés. Si vous aviez utilisé le même choix de mots et la disposition, la prédiction d’origine aurait un pourcentage plus élevé.

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Pourquoi la meilleure intention est-elle sur la liste des énoncés ?
Certains des énoncés de point de terminaison auront un score de prédiction élevé dans la liste de révision. Vous devez quand même réviser et vérifier ces énoncés. Ils figurent dans la liste car l’intention suivante la plus haute avait un score trop proche de celui de la meilleure intention. Vous souhaitez une différence d’environ 15 % entre les deux meilleures intentions.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez révisé les énoncés envoyés au point de terminaison et dont LUIS n’était pas sûr. Une fois ces énoncés vérifiés et déplacés vers les intentions correctes en tant qu’exemples d’énoncés, LUIS améliore la précision des prédictions.

> [!div class="nextstepaction"]
> [En savoir plus sur l’utilisation de ces modèles](luis-tutorial-pattern.md)
