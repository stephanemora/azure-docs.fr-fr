---
title: 'Tutoriel 2 : Intentions et entités prédéfinies - Utiliser des énoncés courants prédéfinis - Extraire des données courantes dans LUIS'
titleSuffix: Azure Cognitive Services
description: Ajouter des intentions et des entités prédéfinies au didacticiel de l’application de Ressources humaines pour obtenir rapidement une prédiction des intentions et l’extraction de données. Vous n’avez pas besoin d’étiqueter les énoncés avec des entités prédéfinies. L’entité est détectée automatiquement.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 3bad68d1a388a5bc8780df633313206afaadcef9
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422420"
---
# <a name="tutorial-2-identify-common-intents-and-entities"></a>Tutoriel 2 : Identifier les intentions et entités courantes
Dans ce tutoriel, vous modifiez l’application Ressources humaines. Ajouter des intentions et des entités prédéfinies au didacticiel de l’application de Ressources humaines pour obtenir rapidement une prédiction des intentions et l’extraction de données. Vous n’avez pas besoin d’étiqueter les énoncés avec des entités prédéfinies, car l’entité est détectée automatiquement.

Les modèles prédéfinis de types de données et de domaines courants vous aident à générer votre modèle rapidement et fournissent un exemple de ce à quoi ressemble un modèle. 

**Ce tutoriel vous montre comment effectuer les opérations suivantes :**

> [!div class="checklist"]
> * Utiliser l’application de tutoriel existante
> * Ajouter des intentions prédéfinies 
> * Ajouter des entités prédéfinies 
> * Former 
> * Publish 
> * Obtenir les intentions et les entités à partir du point de terminaison

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Utiliser l’application existante
Continuez avec l’application créée dans le dernier tutoriel, nommée **HumanResources**. 

Si vous n’avez pas l’application HumanResources du tutoriel précédent, effectuez les étapes suivantes :

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-intent-only-HumanResources.json).

2. Importez le code JSON dans une nouvelle application.

3. À partir de la section **Manage (Gérer)**, sous l’onglet **Versions**, clonez la version et nommez-la `prebuilts`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL. 

## <a name="add-prebuilt-intents"></a>Ajouter des intentions prédéfinies
LUIS fournit plusieurs intentions prédéfinies pour aider avec des intentions utilisateurs courantes.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Sélectionnez **Ajouter une intention prédéfinie**. 

3. Recherchez `Utilities`. 

    [![Capture d’écran de la boîte de dialogue Intentions prédéfinies avec Utilitaires dans la zone de recherche](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Sélectionnez les intentions suivantes, puis sélectionnez **Terminé** : 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>Ajouter des entités prédéfinies
LUIS fournit plusieurs entités prédéfinies pour l’extraction de données courantes. 

1. Dans le menu de navigation de gauche, sélectionnez **Entités**.

2. Sélectionnez le bouton **Gérer l’entité prédéfinie**.

3. Sélectionnez **numéro** et **datetimeV2** dans la liste des entités prédéfinies, puis sélectionnez **Terminé**.

    ![Capture d’écran de la sélection du nombre dans la boîte de dialogue des entités prédéfinies](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train"></a>Former

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publish

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obtenir l’intention et les entités à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Allez à la fin de l’URL dans la barre d’adresse du navigateur et entrez `I want to cancel on March 3`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **requête**. 

    ```JSON
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

    Le résultat a prédit l’intention Utilities.Cancel et extrait la date du 3 mars et le nombre 3. 

    Il existe deux valeurs pour le 3 mars car l’énoncé n’a pas établi si le 3 mars se trouve dans le passé ou dans le futur. C’est à l’application cliente d’émettre une hypothèse ou de demander une clarification, si cela est nécessaire. 

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

En ajoutant des intentions et des entités prédéfinies, l’application cliente peut déterminer des intentions utilisateur courantes et extraire des types de données communs. 

> [!div class="nextstepaction"]
> [Ajouter une entité de type expression régulière à l’application](luis-quickstart-intents-regex-entity.md)

