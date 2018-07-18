---
title: Créer une entité composite pour extraire des données complexes - Azure | Microsoft Docs
description: Découvrez comment créer une entité composite dans votre application LUIS pour extraire différents types de données d’entités.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 03/28/2018
ms.author: v-geberr
ms.openlocfilehash: cb581ee60dea2b0810332933455a03a8b68e16ea
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264383"
---
# <a name="use-composite-entity-to-extract-complex-data"></a>Utiliser une entité composite pour extraire des données complexes
Cette application simple possède deux [intentions](luis-concept-intent.md) et plusieurs entités. Son objectif est de réserver des vols tels que « 1 billet Seattle-Le Caire pour vendredi » et de retourner toutes les caractéristiques de la réservation dans un seul élément de données. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
* Ajouter des entités prédéfinies datetimeV2 et un nombre
* Créer une entité composite
* Interroger LUIS et recevoir des données d’entité composite

## <a name="before-you-begin"></a>Avant de commencer
* Votre application LUIS à partir du  **[démarrage rapide hiérarchique](luis-tutorial-composite-entity.md)**. 

> [!Tip]
> Si vous n’avez pas encore d’abonnement, vous pouvez vous inscrire pour un [compte gratuit](https://azure.microsoft.com/free/).

## <a name="composite-entity-is-a-logical-grouping"></a>L’entité composite est un regroupement logique 
L’objet de l’entité est de rechercher et de classer les parties du texte figurant dans l’énoncé. Une entité [composite](luis-concept-entity-types.md) se compose d’autres types d’entités appris à partir du contexte. Pour cette application de voyage qui effectue des réservations de vols, il existe plusieurs éléments d’information, tels que les dates, les lieux et le nombre de sièges. 

Les informations existent en tant qu’entités distinctes avant la création d’un composite. Créer une entité composite lorsque des entités distinctes peuvent être regroupées logiquement et que ce regroupement logique est utile au chatbot ou à une autre application utilisant LUIS. 

Exemples simples d’énoncés d’utilisateurs :

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Reserve a seat from New York to Paris on the first of April
```
 
L’entité composite correspond au nombre de sièges, au lieu d’origine, au lieu de destination et à la date. 

## <a name="what-luis-does"></a>Action de LUIS
LUIS en a terminé lorsque l’intention et les entités de l’énoncé sont identifiées, [extraites](luis-concept-data-extraction.md#list-entity-data)et retournées au format JSON à partir du [point de terminaison](https://aka.ms/luis-endpoint-apis). L’application d’appel ou le chatbot prend cette réponse JSON et répond à la requête d’une façon qui dépend de la conception de l’application ou du chatbot. 

## <a name="add-prebuilt-entities-number-and-datetimev2"></a>Ajouter datetimeV2 et le nombre d’entités prédéfinies
1. Sélectionnez l’application `MyTravelApp` dans la liste des applications sur le site web [LUIS][LUIS].

2. Lorsque l’application s’ouvre, sélectionnez le lien de navigation gauche **Entités**.

    ![Sélectionnez le bouton d’entités](./media/luis-tutorial-composite-entity/intents-page-select-entities.png)    

3. Sélectionnez **Manage prebuilt entities** (Gérer les entités prédéfinies).

    ![Sélectionnez le bouton d’entités](./media/luis-tutorial-composite-entity/manage-prebuilt-entities-button.png)

4. Dans la zone contextuelle, sélectionnez **nombre** et **datetimeV2**.

    ![Sélectionnez le bouton d’entités](./media/luis-tutorial-composite-entity/prebuilt-entity-ddl.png)

5. Pour extraire les nouvelles entités, activez **Effectuer l'apprentissage** dans la barre de navigation supérieure.

    ![Sélectionnez le bouton Effectuer l’apprentissage](./media/luis-tutorial-composite-entity/train.png)

## <a name="use-existing-intent-to-create-composite-entity"></a>Utilisez une intention existante pour créer une entité composite
1. Sélectionnez **Intentions** dans le volet de navigation gauche. 

    ![Sélectionnez la page Intentions](./media/luis-tutorial-composite-entity/intents-from-entities-page.png)

2. Dans la liste **Intentions**, sélectionnez `BookFlight`.  

    ![Sélectionnez l’intention BookFlight dans la liste](./media/luis-tutorial-composite-entity/intent-page-with-prebuilt-entities-labeled.png)

    Le nombre et la datetimeV2 des entités prédéfinies sont étiquetés sur les énoncés.

3. Pour l’énoncé `book 2 flights from seattle to cairo next monday`, sélectionnez l’`number`entité bleue, puis sélectionnez **Inclure dans un wrapper d’entité composite** dans la liste. Une ligne verte sous les mots suit le curseur lorsqu’il se déplace vers la droite, indiquant une entité composite. Déplacez-vous ensuite vers droite pour sélectionner la dernière entité prédéfinie `datetimeV2`, puis entrez `FlightReservation` dans la zone de texte de la fenêtre contextuelle et sélectionnez **Créer un composite**. 

    ![Créer une entité composite sur la page d’intentions](./media/luis-tutorial-composite-entity/create-new-composite.png)

4. Une boîte de dialogue contextuelle s’affiche, ce qui vous permet de vérifier les enfants de l’entité composite. Sélectionnez **Terminé**.

    ![Créer une entité composite sur la page d’intentions](./media/luis-tutorial-composite-entity/validate-composite-entity.png)

## <a name="wrap-the-entities-in-the-composite-entity"></a>Inclure les entités dans un wrapper d’entité composite
Une fois l’entité composite créée, étiquetez les énoncés restants dans l’entité composite. Pour inclure les entités dans un wrapper en tant qu’entité composite, vous devez sélectionner le mot le plus à gauche, puis **Inclure dans un wrapper d’entité composite** dans la liste qui apparaît et choisissez le mot le plus à droite, puis sélectionnez l’entité composite nommée `FlightReservation`. Il s’agit d’une étape de sélections rapide et sans problèmes qui inclut les points suivants :

1. Dans l’énoncé `schedule 4 seats from paris to london for april 1`, sélectionnez 4 comme nombre d’entités prédéfinies.

    ![Sélectionnez le mot le plus à gauche](./media/luis-tutorial-composite-entity/wrap-composite-step-1.png)

2. Sélectionnez **Inclure dans un wrapper d’entité composite** dans la liste qui s’affiche.

    ![Sélectionnez-le dans la liste.](./media/luis-tutorial-composite-entity/wrap-composite-step-2.png)

3. Sélectionnez le mot le plus à droite. Une ligne verte s’affiche sous l’expression, en indiquant une entité composite.

    ![Sélectionnez le mot le plus à droite](./media/luis-tutorial-composite-entity/wrap-composite-step-3.png)

4. Sélectionnez le nom du composite `FlightReservation` dans la liste qui s’affiche.

    ![Sélectionnez l’entité composite nommée](./media/luis-tutorial-composite-entity/wrap-composite-step-4.png)

    Pour le dernier énoncé, incluez dans un wrapper `London` et `tomorrow` dans l’entité composite en suivant les mêmes instructions. 

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS
LUIS ne connaît pas les modifications apportées aux intentions et aux entités (modèle) tant que son apprentissage n’a pas été effectué. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Effectuer l’apprentissage**.

    ![Effectuer l’apprentissage de l’application](./media/luis-tutorial-composite-entity/train-button.png)

2. L’apprentissage est terminé lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

    ![Apprentissage réussi](./media/luis-tutorial-composite-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publier l’application pour obtenir l’URL de point de terminaison
Pour obtenir une prédiction LUIS dans un chatbot ou une autre application, vous devez publier l’application. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Publier**. 

2. Sélectionnez l’emplacement Production et le bouton **Publier**.

    ![publier une application](./media/luis-tutorial-composite-entity/publish-to-production.png)

3. La publication est terminée lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Interroger le point de terminaison avec un autre énoncé
1. Dans la page **Publier**, sélectionnez le lien **Point de terminaison** en bas de la page. Cette action ouvre une autre fenêtre de navigateur avec l’URL de point de terminaison affichée dans la barre d’adresses. 

    ![Sélectionnez l’URL du point de terminaison](./media/luis-tutorial-composite-entity/publish-select-endpoint.png)

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `reserve 3 seats from London to Cairo on Sunday`. Le dernier paramètre de la chaîne de requête est `q`, la requête de l’énoncé. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `BookFlight` avec l’entité hiérarchique extraite.

```
{
  "query": "reserve 3 seats from London to Cairo on Sunday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.999999046
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.999999046
    },
    {
      "intent": "None",
      "score": 0.227036044
    }
  ],
  "entities": [
    {
      "entity": "sunday",
      "type": "builtin.datetimeV2.date",
      "startIndex": 40,
      "endIndex": 45,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-03-25"
          },
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-04-01"
          }
        ]
      }
    },
    {
      "entity": "3 seats from london to cairo on sunday",
      "type": "flightreservation",
      "startIndex": 8,
      "endIndex": 45,
      "score": 0.6892485
    },
    {
      "entity": "cairo",
      "type": "Location::Destination",
      "startIndex": 31,
      "endIndex": 35,
      "score": 0.557570755
    },
    {
      "entity": "london",
      "type": "Location::Origin",
      "startIndex": 21,
      "endIndex": 26,
      "score": 0.8933808
    },
    {
      "entity": "3",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "3"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "flightreservation",
      "value": "3 seats from london to cairo on sunday",
      "children": [
        {
          "type": "builtin.datetimeV2.date",
          "value": "sunday"
        },
        {
          "type": "Location::Destination",
          "value": "cairo"
        },
        {
          "type": "builtin.number",
          "value": "3"
        },
        {
          "type": "Location::Origin",
          "value": "london"
        }
      ]
    }
  ]
}
```

Cette énoncé retourne un tableau d’entités composites, y compris l’objet **flightreservation** avec les données extraites.  

## <a name="what-has-this-luis-app-accomplished"></a>Quel est l’accomplissement de cette application LUIS ?
Cette application, comptant seulement deux intentions et une entité de composite, a identifié une intention de type requête en langage naturel et retourné les données extraites. 

Votre chatbot a maintenant suffisamment d’informations pour déterminer l’action principale, `BookFlight`, et les informations sur la réservation trouvées dans l’énoncé. 

## <a name="where-is-this-luis-data-used"></a>Où ces données LUIS sont-elles utilisées ? 
LUIS en a fini avec cette requête. L’application d’appel, par exemple un chatbot, peut prendre le résultat topScoringIntent et les données de l’entité pour passer à l’étape suivante. LUIS n’effectue pas ce travail de programmation pour le robot ou l’application d’appel. LUIS détermine uniquement l’intention de l’utilisateur. 

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur les entités](luis-concept-entity-types.md). 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
