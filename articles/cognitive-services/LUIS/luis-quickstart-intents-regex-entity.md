---
title: 'Tutoriel 3 : Données validées par une expression régulière - Extraire des données bien formées'
titleSuffix: Azure Cognitive Services
description: Extrayez des données mises en forme de façon homogène depuis un énoncé à l’aide de l’entité Expression régulière.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 06e212ef756fda9224b38b41c69c7c4eccfb9796
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159854"
---
# <a name="tutorial-3-extract-well-formatted-data"></a>Tutoriel 3 : Extraire des données bien formées
Dans ce tutoriel, vous modifiez l’application Ressources humaines pour extraire des données mises en forme de façon homogène à partir d’un énoncé avec l’entité **Expression régulière**.

L’objectif d’une entité est d’extraire les données importantes contenues dans l’énoncé. Dans cette application, l’utilisation de l’entité d’expression régulière consiste à extraire les numéros de formulaire de Ressources humaines (RH) à partir d’un énoncé. Bien que l’intention de l’énoncé soit toujours déterminée avec l’apprentissage automatique, ce type d’entité spécifique n’est pas issu du machine learning. 

**Les exemples d’énoncés sont les suivants :**

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
Une expression régulière est un choix approprié pour ce type de données quand :

* les données sont bien formées.

**Ce tutoriel vous montre comment effectuer les opérations suivantes :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Utiliser l’application de tutoriel existante
> * Ajouter une intention FindForm
> * Ajouter une entité d’expression régulière 
> * Former
> * Publish
> * Reconnaître les intentions et les entités à partir du point de terminaison

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Utiliser l’application existante
Continuez avec l’application créée dans le dernier tutoriel, nommée **HumanResources**. 

Si vous n’avez pas l’application HumanResources du tutoriel précédent, effectuez les étapes suivantes :

1. Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-prebuilts-HumanResources.json).

2. Importez le code JSON dans une nouvelle application.

3. À partir de la section **Gérer**, sous l’onglet **Versions**, clonez la version et nommez-la `regex`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL. 

## <a name="findform-intent"></a>Intention FindForm

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Sélectionnez **Créer une intention**. 

3. Entrez `FindForm` dans la boîte de dialogue contextuelle, puis sélectionnez **Terminé**. 

    ![Capture d’écran de la boîte de dialogue de création d’une intention avec Utilities (Utilitaires) dans la zone de recherche](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

4. Ajoutez des exemples d’énoncés à l’intention.

    |Exemples d’énoncés|
    |--|
    |Quelle est l’URL pour hrf-123456 ?|
    |Où se trouve hrf-345678 ?|
    |Quand a été effectuée la mise à jour de hrf-456098 ?|
    |John Smith a-t-il effectué la mise à jour de hrf-234639 la semaine dernière ?|
    |Combien y a-t-il de versions de hrf-345123 ?|
    |Qui a besoin d’autoriser le formulaire hrf-123456 ?|
    |Combien de personnes ont besoin de valider hrf-345678 ?|
    |Date de hrf-234123 ?|
    |Auteur de hrf-546234 ?|
    |Titre de hrf-456234 ?|

    [ ![Capture d’écran de la page d’intentions avec de nouveaux énoncés mis en surbrillance](./media/luis-quickstart-intents-regex-entity/findform-intent.png) ](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    L’application comporte une entité de nombre prédéfinie ajoutée à partir du didacticiel précédent, de sorte que chaque numéro de formulaire est étiqueté. Cela peut suffire pour votre application cliente, mais le numéro ne sera pas étiqueté avec le type de numéro. Création d’une entité avec un nom approprié permet à l’application cliente de traiter l’entité correctement lorsqu’elle est retournée à partir de LUIS.

    [!include[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="regular-expression-entity"></a>Entité d’expression régulière 
L’entité d’expression régulière correspondant au numéro du formulaire est `hrf-[0-9]{6}`. Cette expression régulière correspond aux caractères littéraux `hrf-`, mais ignore les variantes de casse et de culture. Elle correspond aux chiffres 0 à 9, pour 6 chiffres exactement.

HRF signifie `human resources form`.

LUIS segmente l’énoncé en unités lexicales quand il est ajouté à une intention. La segmentation du texte de ces énoncés en unités lexicales ajoute des espaces avant et après le trait d’union, `Where is HRF - 123456?` L’expression régulière est appliquée à l’énoncé à l’état brut, avant que ce dernier soit segmenté en unité lexicales. Comme elle est appliquée à l’état _brut_, l’expression régulière n’a pas besoin de tenir compte des limites de mot. 

Créez une entité d’expression régulière pour indiquer à LUIS ce qu’est un format de numéro HRF en effectuant les étapes suivantes :

1. Dans le panneau gauche, sélectionnez **Entités**.

2. Cliquez sur le bouton **Create new entity** (Créer une entité) sur la Page d’entités. 

3. Dans la boîte de dialogue contextuelle, entrez le nouveau nom d’entité `HRF-number`, sélectionnez **RegEx** comme type d’entité, entrez `hrf-[0-9]{6}` comme valeur **d’expression régulière**, puis sélectionnez **Terminé**.

    ![Capture d’écran de la boîte de dialogue contextuelle de définition des nouvelles propriétés de l’entité](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. Sélectionnez **Intents** (Intentions) dans le menu de gauche, puis l’intention **FindForm** pour voir l’expression régulière étiquetée dans les énoncés. 

    [![Capture d’écran d’étiquetage d’un énoncé avec une entité existante et un modèle d’expression régulière](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Dans la mesure où l’entité n’est pas une entité issue de l’apprentissage automatique, l’étiquette est appliquée aux énoncés et affichée sur le site web de LUIS dès sa création.

## <a name="train"></a>Former

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publish

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Reconnaître l’intention et les entités à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `When were HRF-123456 and hrf-234567 published in the last year?`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `FindForm` avec les deux numéros de formulaire `HRF-123456` et `hrf-234567`.

    ```JSON
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9993477
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9993477
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0206110049
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00533067342
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.004215215
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00209096959
        },
        {
          "intent": "None",
          "score": 0.0017655947
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00109490135
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.0005704638
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.000525338168
        }
      ],
      "entities": [
        {
          "entity": "last year",
          "type": "builtin.datetimeV2.daterange",
          "startIndex": 53,
          "endIndex": 61,
          "resolution": {
            "values": [
              {
                "timex": "2017",
                "type": "daterange",
                "start": "2017-01-01",
                "end": "2018-01-01"
              }
            ]
          }
        },
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        },
        {
          "entity": "-234567",
          "type": "builtin.number",
          "startIndex": 28,
          "endIndex": 34,
          "resolution": {
            "value": "-234567"
          }
        }
      ]
    }
    ```

    Les numéros dans l’énoncé sont retournés deux fois, une fois en tant que nouvelle entité `hrf-number`et une fois en tant qu’entité prédéfinie, `number`. Un énoncé peut comporter plusieurs entités et plusieurs occurrences du même type d’entité, comme le montre cet exemple. En utilisant une entité d’expression régulière, LUIS extrait les données nommées, ce qui est plus utile, sur le plan de la programmation, à l’application cliente qui reçoit la réponse JSON.


## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez créé une intention, ajouté des exemples d’énoncés, puis créé une entité d’expression régulière pour extraire des données bien formées à partir des énoncés. Une fois l’application entraînée et publiée, une requête au point de terminaison a identifié l’intention et retourné les données extraites.

> [!div class="nextstepaction"]
> [En savoir plus sur l’entité de liste](luis-quickstart-intent-and-list-entity.md)

