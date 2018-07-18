---
title: Ajouter des intentions et des entités prédéfinies pour extraire des données courantes dans Language Understanding - Azure | Microsoft Docs
description: Découvrez comment utiliser des intentions et des entités prédéfinies pour extraire différents types de données d’entités.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 06/11/2018
ms.author: v-geberr
ms.openlocfilehash: 20950ced66497fb0dc96365975b37f244f677ce3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266377"
---
# <a name="use-prebuilt-intents-and-entities-to-handle-common-intents-and-data"></a>Utiliser des intentions et des données prédéfinies pour traiter des intentions et des données courantes
Ajouter des intentions et des entités prédéfinies à l’application de démarrage rapide de Ressources humaines pour obtenir rapidement une prédiction des intentions et l’extraction de données. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
* Ajouter des intentions prédéfinies 
* Ajouter des entités prédéfinies datetimeV2 et un nombre
* Former et publier
* Interroger LUIS et recevoir une réponse de prédiction

## <a name="before-you-begin"></a>Avant de commencer
Si vous n’avez pas l’application Ressources humaines du démarrage rapide du [domaine personnalisé](luis-quickstart-intents-only.md), [importez](create-new-app.md#import-new-app) le fichier JSON dans une nouvelle application du site web [LUIS][LUIS] à partir du référentiel Github [ LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json).

Si vous souhaitez conserver l’application Ressources humaines d’origine, clonez la version sur la page [Paramètres](luis-how-to-manage-versions.md#clone-a-version), et nommez-la `prebuilts`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. 

## <a name="add-prebuilt-intents"></a>Ajouter des intentions prédéfinies
LUIS fournit plusieurs intentions prédéfinies pour aider avec des intentions utilisateurs courantes.  

1. Assurez-vous que votre application figure dans la section **Générer** de LUIS. Vous pouvez modifier cette section en sélectionnant **Générer** dans la barre de menu en haut à droite. 

    [![Capture d’écran de l’application LUIS avec Générer en surbrillance dans la barre de navigation en haut à droite](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png)](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png#lightbox)

2. Sélectionnez **Ajouter une intention de domaine prédéfinie**. 

    [ ![Capture d’écran de la page des intentions avec le bouton Ajouter une intention de domaine prédéfinie mis en surbrillance](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. Recherchez `Utilities`. 

    [![Capture d’écran de la boîte de dialogue Intentions prédéfinies avec Utilitaires dans la zone de recherche](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Sélectionnez les intentions suivantes, puis sélectionnez **Terminé** : 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.Stop
    * Utilities.StartOver

## <a name="add-prebuilt-entities"></a>Ajouter des entités prédéfinies
LUIS fournit plusieurs entités prédéfinies pour l’extraction de données courantes. 

1. Dans le menu de navigation de gauche, sélectionnez **Entités**.

    [ ![Capture d’écran de la liste d’Intentions avec le bouton Entités mis en surbrillance dans le volet de navigation gauche](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. Sélectionnez le bouton **Gérer les entités prédéfinies**.

    [ ![Capture d’écran de la liste des entités avec Gérer des entités mis en surbrillance](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. Sélectionnez **numéro** et **datetimeV2** dans la liste des entités prédéfinies, puis sélectionnez **Terminé**.

    ![Capture d’écran de la sélection du nombre dans la boîte de dialogue des entités prédéfinies](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>Former et publier l’application
1. En haut à droite du site web LUIS, sélectionnez le bouton **Effectuer l’apprentissage**. 

    ![Bouton Effectuer l'apprentissage](./media/luis-quickstart-intents-only/train-button.png)

    L’apprentissage est terminé lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

    ![Barre d’état Apprentissage effectué](./media/luis-quickstart-intents-only/trained.png)

2. En haut à droite du site web LUIS, sélectionnez le bouton **Publier** pour ouvrir la page de publication. L’emplacement de production est sélectionné par défaut. Sélectionnez le bouton **Publier** à côté de l’emplacement de production souhaité. La publication est terminée lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

    Vous n’êtes pas obligé de créer une clé LUIS dans le portail Azure avant de publier ou de tester l’URL du point de terminaison. Chaque application LUIS possède une clé de démarrage gratuite pour la création. Vous bénéficiez d’une création illimitée et de [quelques accès de point de terminaison](luis-boundaries.md#key-limits). 

## <a name="query-endpoint-with-an-utterance"></a>Interroger un point de terminaison avec un énoncé
Dans la page **Publier**, sélectionnez le lien **Point de terminaison** en bas de la page. Cette action ouvre une autre fenêtre de navigateur avec l’URL de point de terminaison affichée dans la barre d’adresses. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `I want to cancel on March 3`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **requête**. 

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

En ajoutant rapidement et facilement des intentions et des entités prédéfinies, l’application cliente peut ajouter une gestion de la conversation et extraire des types de données communs. 

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur les entités](luis-concept-entity-types.md). 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
