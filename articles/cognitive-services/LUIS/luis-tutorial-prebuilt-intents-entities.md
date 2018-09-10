---
title: Ajouter des intentions et des entités prédéfinies pour extraire des données courantes dans Language Understanding - Azure | Microsoft Docs
description: Découvrez comment utiliser des intentions et des entités prédéfinies pour extraire différents types de données d’entités.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/03/2018
ms.author: diberry
ms.openlocfilehash: 0e45b659508c71a9f1220ef5e76b9a95438fa1e6
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162238"
---
# <a name="tutorial-2-add-prebuilt-intents-and-entities"></a>Didacticiel : 2. Ajouter des intentions et des entités prédéfinies
Ajouter des intentions et des entités prédéfinies au didacticiel de l’application de Ressources humaines pour obtenir rapidement une prédiction des intentions et l’extraction de données. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
* Ajouter des intentions prédéfinies 
* Ajouter des entités prédéfinies datetimeV2 et un nombre
* Former et publier
* Interroger LUIS et recevoir une réponse de prédiction

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Avant de commencer
Si vous ne disposez pas de l’application [Ressources humaines](luis-quickstart-intents-only.md) du didacticiel précédent, [importez](luis-how-to-start-new-app.md#import-new-app) le JSON dans une nouvelle application sur le site web [LUIS](luis-reference-regions.md#luis-website), à partir du dépôt Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json).

Si vous souhaitez conserver l’application Ressources humaines d’origine, clonez la version sur la page [Paramètres](luis-how-to-manage-versions.md#clone-a-version), et nommez-la `prebuilts`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. 

## <a name="add-prebuilt-intents"></a>Ajouter des intentions prédéfinies
LUIS fournit plusieurs intentions prédéfinies pour aider avec des intentions utilisateurs courantes.  

1. Assurez-vous que votre application figure dans la section **Générer** de LUIS. Vous pouvez modifier cette section en sélectionnant **Générer** dans la barre de menu en haut à droite. 

2. Sélectionnez **Ajouter une intention de domaine prédéfinie**. 

    [ ![Capture d’écran de la page des intentions avec le bouton Ajouter une intention de domaine prédéfinie mis en surbrillance](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

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

    [ ![Capture d’écran de la liste d’Intentions avec le bouton Entités mis en surbrillance dans le volet de navigation gauche](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. Sélectionnez le bouton **Gérer les entités prédéfinies**.

    [ ![Capture d’écran de la liste des entités avec Gérer des entités mis en surbrillance](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. Sélectionnez **numéro** et **datetimeV2** dans la liste des entités prédéfinies, puis sélectionnez **Terminé**.

    ![Capture d’écran de la sélection du nombre dans la boîte de dialogue des entités prédéfinies](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>Former et publier l’application

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-app-to-endpoint"></a>Publier l’application vers un point de terminaison

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-endpoint-with-an-utterance"></a>Interroger un point de terminaison avec un énoncé

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `I want to cancel on March 3`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **requête**. 

    Le résultat a prédit l’intention Utilities.Cancel et extrait la date du 3 mars et le nombre 3. 

    ```
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

    Il existe deux valeurs pour le 3 mars car l’énoncé n’a pas établi si le 3 mars se trouve dans le passé ou dans le futur. C’est à l’application d’appel de LUIS d’émettre une hypothèse ou de demander une clarification, si cela est nécessaire. 

    En ajoutant rapidement et facilement des intentions et des entités prédéfinies, l’application cliente peut ajouter une gestion de la conversation et extraire des types de données communs. 

## <a name="clean-up-resources"></a>Supprimer les ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter une entité de type expression régulière à l’application](luis-quickstart-intents-regex-entity.md)

