---
title: Didacticiel pour créer une application LUIS afin d’obtenir les données d’emplacement - Azure | Microsoft Docs
description: Dans ce didacticiel, vous allez découvrir comment créer une application LUIS simple utilisant une entité hiérarchique et des intentions pour extraire les données.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: v-geberr
ms.openlocfilehash: 2547407126943161ba604fa2f5e80b9186cae57e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266496"
---
# <a name="tutorial-create-app-that-uses-hierarchical-entity"></a>Didacticiel : Créer une application utilisant une entité hiérarchique
Dans ce didacticiel, vous allez créer une application qui montre comment rechercher les ensembles de données en fonction du contexte. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendre les entités hiérarchiques et les enfants issus du contexte 
> * Créer une application LUIS pour un domaine de voyage avec l’intention Bookflight
> * Ajouter l’intention _None_ et ajouter des exemples d’énoncés
> * Ajouter une entité hiérarchique avec des enfants d’origine et de destination
> * Effectuer l’apprentissage et publier l’application
> * Interroger un point de terminaison d’application pour voir la réponse JSON LUIS, y compris les enfants hiérarchiques 

Pour cet article, vous devez disposer d’un compte [LUIS][LUIS] gratuit afin de créer votre application LUIS.

## <a name="purpose-of-the-app-with-this-entity"></a>Objectif de l’application avec cette entité
Cette application détermine si un utilisateur souhaite réserver un vol. Elle utilise l’entité hiérarchique pour déterminer les emplacements, la ville d’origine et la ville de destination, dans le texte de l’utilisateur. 

L’entité hiérarchique est adaptée à ce type de données, car les deux ensembles de données :

* sont des emplacements, généralement exprimés sous la forme de codes de villes ou d’aéroports.
* comportent généralement un choix unique de mots associés permettant de déterminer l’emplacement d’origine et l’emplacement de destination. Ces mots sont : vers, en direction de, au départ de, décollage.
* Les deux emplacements se trouvent fréquemment dans le même énoncé. 

L’objectif de l’entité **hiérarchique** est de rechercher les données associées dans l’énoncé en fonction du contexte. Prenez l’énoncé suivant :

```JSON
1 ticket from Seattle to Cairo`
```

Deux emplacements sont indiqués dans l’énoncé. L’un est la ville d’origine, Seattle, et l’autre est la ville de destination, Le Caire. Ces villes sont essentielles pour réserver un vol. Même si vous pouvez les rechercher à l’aide d’entités simples, elles sont liées l’une à l’autre et se trouvent fréquemment dans le même énoncé. Par conséquent, il est logique qu’elles soient regroupées en tant qu’enfants d’une entité hiérarchique, **« Location »**. 

Dans le cas des entités issues de l’apprentissage automatique, l’application a besoin d’exemples d’énoncés étiquetant les villes d’origine et de destination. Ainsi LUIS apprend où se trouvent les entités dans les énoncés, leur longueur et les mots associés. 

## <a name="app-intents"></a>Intentions de l’application
Les intentions sont des catégories correspondant aux souhaits de l’utilisateur. Cette application compte deux intentions : BookFlight et None. L’intention [None](luis-concept-intent.md#none-intent-is-fallback-for-app) est délibérée, pour indiquer tout élément extérieur à l’application.  

## <a name="hierarchical-entity-is-contextually-learned"></a>L’entité hiérarchique est issue du contexte 
L’objectif de l’entité est de rechercher et de classer les parties du texte figurant dans l’énoncé. Une entité [hiérarchique](luis-concept-entity-types.md) est une entité parent-enfant basée sur le contexte de l’utilisation. Une personne peut déterminer les villes d’origine et de destination dans un énoncé en fonction de l’utilisation de `to` et `from`. Voici un exemple d’utilisation contextuelle.  

Pour cette application de voyage, LUIS extrait les emplacements d’origine et de destination de manière à ce qu’une réservation standard puisse être créée et remplie. LUIS autorise des énoncés comptant des variations, des abréviations et de l’argot. 

Exemples simples d’énoncés d’utilisateurs :

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Researve a seat from New York to Paris on the first of April
```

Versions abrégées ou argotiques d’énoncés :

```
LHR tomorrow
SEA to NYC next Monday
LA to MCO spring break
```
 
L’entité hiérarchique correspond à l’emplacement d’origine et de destination. Si un seul enfant (origine ou destination) d’une entité hiérarchique est présent, il est tout de même extrait. Il n’est pas nécessaire de trouver tous les enfants pour en extraire un seul ou quelques-uns. 

## <a name="what-luis-does"></a>Action de LUIS
LUIS en a terminé lorsque l’intention et les entités de l’énoncé sont identifiées, [extraites](luis-concept-data-extraction.md#list-entity-data)et retournées au format JSON à partir du [point de terminaison](https://aka.ms/luis-endpoint-apis). L’application d’appel ou le chatbot prend cette réponse JSON et répond à la demande d’une façon qui dépend de la conception de l’application ou du chatbot. 

## <a name="create-a-new-app"></a>Créer une application
1. Connectez-vous au site web [LUIS][LUIS]. Veillez à vous connecter à la [région][LUIS-regions] où vous avez besoin de points de terminaison LUIS publiés.

2. Sur le site web [LUIS][LUIS], sélectionnez **Créer une application**.  

    [![](media/luis-quickstart-intent-and-hier-entity/app-list.png "Capture d’écran de la page Liste des applications")](media/luis-quickstart-intent-and-hier-entity/app-list.png#lightbox)

3. Dans la boîte de dialogue contextuelle, entrez le nom `MyTravelApp`. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-app.png "Capture d’écran de la boîte de dialogue contextuelle Créer une application")](media/luis-quickstart-intent-and-hier-entity/create-new-app.png#lightbox)

4. Lorsque ce processus se termine, l’application affiche la page **Intents** (Intentions) avec l’intention **None**. 

    [![](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png "Capture d’écran de la liste d’intentions avec une intention None seulement")](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>Créer une intention

1. Dans la page **Intents** (Intentions), sélectionnez **Create new intent** (Créer une intention). 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png "Capture d’écran de la liste d’intentions avec le bouton Créer une intention mis en surbrillance")](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png#lightbox)

2. Entrez le nom de la nouvelle intention `BookFlight`. Cette intention doit être sélectionnée chaque fois qu’un utilisateur souhaite réserver un vol.

    En créant une intention, vous créez la catégorie principale d’informations que vous souhaitez identifier. Attribuer un nom à la catégorie permet à toute autre application utilisant les résultats de la requête LUIS d’utiliser ce nom de catégorie pour trouver une réponse appropriée ou agir de façon adéquate. LUIS ne répond pas à ces questions, et identifie uniquement le type d’informations demandé dans un langage naturel. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png "Capture d’écran de la boîte de dialogue contextuelle Créer une intention")](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png#lightbox)

3. Ajoutez plusieurs énoncés à l’intention `BookFlight` qu’un utilisateur demandera selon vous :

    | Exemples d’énoncés|
    |--|
    |Réservation de 2 vols entre Seattle et Le Caire lundi prochain|
    |Réservation d’un billet pour Londres demain|
    |Réservation de 4 sièges entre Paris et Londres le 1er avril|

    [![](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png "Capture d’écran de saisie d’énoncé sur la page de l’intention BookFlight")](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Ajouter des énoncés à l’intention None

Pour le moment, l’application LUIS ne dispose d’aucun énoncé pour l’intention **None**. Comme elle a besoin d’énoncés auxquels vous ne souhaitez pas que l’application réponde, elle doit disposer d’énoncés dans l’intention **None**. Renseignez cette zone. 

1. Dans le panneau gauche, sélectionnez **Intents** (Intentions). 

    [![](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png "Capture d’écran de la page d’intentions BookFlight avec le bouton Intents (Intensions) mis en surbrillance")](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png#lightbox)

2. Sélectionnez l’intention **None**. Ajoutez trois énoncés que votre utilisateur est susceptible d’entrer, mais qui ne sont pas pertinents pour votre application.

    | Exemples d’énoncés|
    |--|
    |Annuler|
    |Au revoir|
    |Que se passe-t-il ?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>Lorsque l’énoncé est prédit pour l’intention None
Dans votre application d’appel de LUIS, comme un chatbot, si LUIS retourne l’intention **None** pour un énoncé, votre robot peut demander si l’utilisateur souhaite mettre fin à la conversation. Le robot peut également donner d’autres directions pour poursuivre la conversation si l’utilisateur ne souhaite pas y mettre un terme. 

Les entités fonctionnent dans l’intention **None**. Si l’intention à notation supérieure est **None**, mais qu’une entité significative pour votre chatbot est extraite, votre chatbot peut y faire suite avec une question axée sur l’intention du client. 

## <a name="create-a-location-entity-from-the-intent-page"></a>Créer une entité d’emplacement à partir de la page Intents (Intentions)
Maintenant que les deux intentions disposent d’énoncés, LUIS doit comprendre ce qu’est un emplacement. Revenez à l’intention `BookFlight` et étiquetez (marquez) le nom de la ville dans un énoncé en procédant comme suit :

1. Revenez à l’intention `BookFlight` en sélectionnant **Intents** (Intentions) dans le volet gauche.

2. Dans la liste des intentions, sélectionnez `BookFlight`.

3. Dans l’énoncé `Book 2 flights from Seattle to Cairo next Monday`, sélectionnez le mot `Seattle`. Un menu déroulant s’affiche avec une zone de texte dans sa partie supérieure permettant de créer une entité. Entrez le nom de l’entité `Location` dans la zone de texte, puis sélectionnez **Créer une entité** dans le menu déroulant. 

    [![](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png "Capture d’écran de la page Intent (Intention) de BookFlight, création d’une entité à partir du texte sélectionné")](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png#lightbox)

4. Dans la fenêtre contextuelle, sélectionnez le type d’entité **hiérarchique** avec `Origin` et `Destination` comme entités enfants. Sélectionnez **Terminé**.

    [![](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png "Capture d’écran de la boîte de dialogue contextuelle de création d’une entité pour une nouvelle entité Emplacement")](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png#lightbox)

    L’étiquette de `Seattle` est marquée comme `Location` car LUIS ne sait pas si le terme désigne l’origine ou la destination ou aucun des deux. Sélectionnez `Seattle`, puis Emplacement, suivez le menu à droite et cliquez sur `Origin`.

5. Maintenant que l’entité est créée et qu’un énoncé est étiqueté, étiquetez les autres villes en sélectionnant le nom de la ville, puis l’emplacement, puis suivez le menu à droite et cliquez sur `Origin` ou `Destination`.

    [![](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png "Capture d’écran de l’entité Bookflight avec le texte de l’énoncé sélectionné pour la sélection de l’entité")](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png#lightbox)

## <a name="train-the-luis-app"></a>Effectuer l’apprentissage de l’application LUIS
LUIS ne connaît pas les modifications apportées aux intentions et aux entités (modèle) tant que son apprentissage n’a pas été effectué. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Effectuer l’apprentissage**.

    ![Effectuer l’apprentissage de l’application](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. L’apprentissage est terminé lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

    ![Apprentissage réussi](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publier l’application pour obtenir l’URL de point de terminaison
Pour obtenir une prédiction LUIS dans un chatbot ou une autre application, vous devez publier l’application. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Publier**. 

    [![](media/luis-quickstart-intent-and-hier-entity/publish.png "Capture d’écran de l’intention Bookflight avec le bouton Publier mis en surbrillance")](media/luis-quickstart-intent-and-hier-entity/publish.png#lightbox)

2. Sélectionnez l’emplacement Production et le bouton **Publier**.

    [![](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png "Capture d’écran de la page Publier avec le bouton Publier vers l’emplacement Production mis en surbrillance")](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png#lightbox)

3. La publication est terminée lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Interroger le point de terminaison avec un autre énoncé
1. Dans la page **Publier**, sélectionnez le lien **Point de terminaison** en bas de la page. Cette action ouvre une autre fenêtre de navigateur avec l’URL de point de terminaison affichée dans la barre d’adresses. 

    [![](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png "Capture d’écran de la page Publier avec l’URL du point de terminaison mise en surbrillance")](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png#lightbox)

2. Accédez à la fin de l’URL dans l’adresse, puis entrez `1 ticket to Portland on Friday`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **q**uery. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `BookFlight` avec l’entité hiérarchique extraite.

```
{
  "query": "1 ticket to Portland on Friday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9998226
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.9998226
    },
    {
      "intent": "None",
      "score": 0.221926212
    }
  ],
  "entities": [
    {
      "entity": "portland",
      "type": "Location::Destination",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.564448953
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Quel est l’accomplissement de cette application LUIS ?
Cette application, comptant seulement deux intentions et une entité hiérarchique, a identifié une intention de requête en langage naturel et retourné les données extraites. 

Votre chatbot a maintenant suffisamment d’informations pour déterminer l’action principale, `BookFlight`, et les informations d’emplacement trouvées dans l’énoncé. 

## <a name="where-is-this-luis-data-used"></a>Où ces données LUIS sont-elles utilisées ? 
LUIS en a fini avec cette demande. L’application d’appel, par exemple un chatbot, peut prendre le résultat topScoringIntent et les données de l’entité pour passer à l’étape suivante. LUIS n’effectue pas ce travail de programmation pour le robot ou l’application d’appel. LUIS détermine uniquement l’intention de l’utilisateur. 

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, supprimez l’application LUIS. Pour ce faire, sélectionnez le menu représentant trois points (...) à droite du nom de l’application dans la liste des applications, puis **Supprimer**. Dans la boîte de dialogue contextuelle **Supprimer l’application ?**, sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Découvrez comment ajouter une entité de liste](luis-quickstart-intent-and-list-entity.md) 

Ajoutez **l’entité prédéfinie** de [nombre](luis-how-to-add-entities.md#add-prebuilt-entity) pour extraire le nombre. 

Ajouter [l’entité prédéfinie](luis-how-to-add-entities.md#add-prebuilt-entity) **datetimeV2** pour extraire les informations de date.


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
