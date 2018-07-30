---
title: Didacticiel pour créer une application LUIS afin d’obtenir les données d’emplacement - Azure | Microsoft Docs
description: Dans ce tutoriel, vous allez découvrir comment créer une application LUIS simple utilisant une entité hiérarchique et des intentions pour extraire les données.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 07/04/2018
ms.author: diberry
ms.openlocfilehash: fb29e0a22331ce279d3dc8fc5a0044ae794d260b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226082"
---
# <a name="tutorial-5-add-hierarchical-entity"></a>Tutoriel : 5. Ajouter une entité hiérarchique
Dans ce tutoriel, vous allez créer une application qui montre comment rechercher les ensembles de données en fonction du contexte. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendre les entités hiérarchiques et les enfants issus du contexte 
> * Utiliser l’application LUIS pour le domaine des ressources humaines (RH) 
> * Ajouter une entité hiérarchique avec des enfants d’origine et de destination
> * Effectuer l’apprentissage et publier l’application
> * Interroger un point de terminaison d’application pour voir la réponse JSON LUIS, y compris les enfants hiérarchiques 

Pour cet article, vous devez disposer d’un compte [LUIS](luis-reference-regions.md#luis-website) gratuit afin de créer votre application LUIS.

## <a name="before-you-begin"></a>Avant de commencer
Si vous ne disposez pas de l’application Ressources humaines du tutoriel [liste d’entités](luis-quickstart-intent-and-list-entity.md), [importez](luis-how-to-start-new-app.md#import-new-app) le JSON dans une nouvelle application du site Web [LUIS](luis-reference-regions.md#luis-website). L’application à importer se trouve dans le référentiel Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-list-HumanResources.json).

Si vous souhaitez conserver l’application Ressources humaines d’origine, clonez la version sur la page [Paramètres](luis-how-to-manage-versions.md#clone-a-version), et nommez-la `hier`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. 

## <a name="purpose-of-the-app-with-this-entity"></a>Objectif de l’application avec cette entité
Cette application détermine lorsqu’un employé doit déménager de son emplacement d’origine (bâtiment et bureau) à un emplacement de destination (bâtiment et bureau). Elle utilise l’entité hiérarchique pour détermine les emplacements au sein de l’énoncé. 

L’entité hiérarchique est adaptée à ce type de données, car les deux ensembles de données :

* Sont liés l’un à l’autre dans le contexte de l’énoncé.
* Utilisent des formulations spécifiques pour indiquer chaque emplacement. Exemples : de/à, quitte/arrive à, loin de/vers...
* Les deux emplacements se trouvent fréquemment dans le même énoncé. 

L’objectif de l’entité **hiérarchique** est de rechercher les données associées dans l’énoncé en fonction du contexte. Prenez l’énoncé suivant :

```JSON
mv Jill Jones from a-2349 to b-1298
```
Deux emplacements sont indiqués dans l’énoncé : `a-2349` et `b-1298`. Part du principe que la lettre correspond au nom du bâtiment et le numéro au bureau dans ce bâtiment. Il est logique qu’ils soient groupés en tant qu’enfants d’une même entité hiérarchique, `Locations` car les deux groupes de données doivent être extraits de l’énoncé et sont liés l’un à l’autre. 
 
Si un seul enfant (origine ou destination) d’une entité hiérarchique est présent, il est tout de même extrait. Il n’est pas nécessaire de trouver tous les enfants pour en extraire un seul ou quelques-uns. 

## <a name="remove-prebuilt-number-entity-from-app"></a>Supprimer l’entité de nombre prédéfinie de l’application
Afin d’afficher l’énoncé complet et de marquer les enfants hiérarchiques, supprimez temporairement l’entité de nombre prédéfinie.

1. Assurez-vous que votre application Ressources humaines figure dans la section **Générer** de LUIS. Vous pouvez modifier cette section en sélectionnant **Générer** dans la barre de menu en haut à droite. 

    [![Capture d’écran de l’application LUIS avec Générer en surbrillance dans la barre de navigation en haut à droite](./media/luis-quickstart-intent-and-hier-entity/hr-first-image.png)](./media/luis-quickstart-intent-and-hier-entity/hr-first-image.png#lightbox)

2. Dans le menu gauche, sélectionnez **Entités**.

    [ ![Capture d’écran de l’application LUIS avec les boutons Entité mis en surbrillance dans le menu de gauche](./media/luis-quickstart-intent-and-hier-entity/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-entities-button.png#lightbox)


3. Sélectionnez le bouton des points de suspension (***...***) à droite de l’entité de nombre dans la liste. Sélectionnez **Supprimer**. 

    [ ![Capture d’écran de l’application LUIS sur la page de la liste d’entité, avec le bouton supprimer mis en surbrillance pour l’entité Nombre prédéfinie](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png)](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png#lightbox)


## <a name="add-utterances-to-moveemployee-intent"></a>Ajoutez des énoncés à l’intention de MoveEmployee

1. Dans le menu gauche, sélectionnez **Intents** (Intentions).

    [ ![Capture d’écran de l’application LUIS avec Intents (Intentions) mis en surbrillance dans le menu de gauche](./media/luis-quickstart-intent-and-hier-entity/hr-select-intents-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-intents-button.png#lightbox)

2. Sélectionnez **MoveEmployee** dans la liste des intentions.

    [ ![Capture d’écran de l’application LUIS avec l’intention MoveEmployee mise en surbrillance dans le menu de gauche](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png)](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png#lightbox)

3. Ajoutez les énoncés exemples suivants :

    |Exemples d’énoncés|
    |--|
    |Déménager John W. Smith **vers** a-2345|
    |Rediriger Jill Jones **vers** b-3499|
    |Organiser le déménagement de x23456 **de** hh-2345 **vers** e-0234|
    |Commencer les formalités pour le **départ** de x12345 de a-3459 **vers** f-34567|
    |Faire partir 425-555-0000 **de** g-2323 **vers** hh-2345|

    Dans le didacticiel [liste d’entités](luis-quickstart-intent-and-list-entity.md), un employé peut être désigné par son nom, adresse e-mail, numéro de poste, numéro de téléphone mobile ou numéro de sécurité sociale (USA). Ces numéros relatifs à l’employé sont utilisés dans les énoncés. Les énoncés exemples précédents incluent différentes façons de noter les emplacements d’origine et de destination, marqués en gras. Quelques énoncés n’ont que les destinations, à dessein. Cela aide LUIS à comprendre comment ces emplacements sont placés dans l’énoncé lorsque l’origine n’est pas spécifiée.

    [ ![Capture d’écran de LUIS avec les nouveaux énoncés dans l’intention MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)
     

## <a name="create-a-location-entity"></a>Créer une entité d’emplacement
LUIS a besoin de comprendre ce qu’est un emplacement en étiquetant l’origine et la destination dans les énoncés. Si vous devez voir l’énoncé dans l’affichage du jeton (brut), sélectionnez le bouton bascule dans la barre au-dessus des énoncés dénommé **Entities View** (affichage d’entités). Après avoir activé le bouton bascule , la commande s’appelle **Tokens View** (Vue des jetons).

1. Dans l’énoncé `Displace 425-555-0000 away from g-2323 toward hh-2345`, sélectionnez le mot `g-2323`. Un menu déroulant apparaît avec une zone de texte à son sommet. Entrez le nom de l’entité `Locations` dans la zone de texte, puis sélectionnez **Créer une entité** dans le menu déroulant. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png "Capture d’écran de création d’une nouvelle entité sur la page d’intention")](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png#lightbox)

2. Dans la fenêtre contextuelle, sélectionnez le type d’entité **hiérarchique** avec `Origin` et `Destination` comme entités enfants. Sélectionnez **Terminé**.

    ![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Capture d’écran de la boîte de dialogue contextuelle de création d’une entité pour une nouvelle entité Emplacement")

3. L’étiquette de `g-2323` est marquée comme `Locations` car LUIS ne sait pas si le terme désigne l’origine ou la destination ou aucun des deux. Sélectionnez `g-2323`, puis **Locations** (Emplacement), puis suivez le menu à droite et cliquez sur `Origin`.

    [![](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png "Capture d’écran de la boîte de dialogue contextuelle d’étiquetage d’une entité pour modifier l’enfant d’entité de l’emplacement")](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png#lightbox)

5. Étiquetez les autres emplacements de tous les autres énoncés en sélectionnant le bâtiment et le bureau de l’énoncé, puis Locations (Emplacements), puis le menu à droite pour choisir `Origin` ou `Destination`. Lorsque tous les emplacements sont étiquetés, les énoncés dans **Tokens View** (Vue du jeton) commencent à ressembler à un modèle. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png "Capture d’écran de l’entité Locations (emplacements) étiquetée dans les énoncés")](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png#lightbox)

## <a name="add-prebuilt-number-entity-to-app"></a>Ajouter l’entité de nombre prédéfinie dans l’application
Ajoutez l’entité de nombre prédéfinie dans l’application.

1. Dans le menu de navigation de gauche, sélectionnez **Entités**.

    [ ![Capture d’écran du bouton Entités mis en surbrillance dans le volet de navigation gauche](./media/luis-quickstart-intent-and-hier-entity/hr-select-entity-button-from-intent-page.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-entity-button-from-intent-page.png#lightbox)

2. Sélectionnez le bouton **Manage prebuilt entities** (Gérer les entités prédéfinies).

    [ ![Capture d’écran de la liste Entities (Entités) avec Manage prebuilt entities en surbrillance](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png#lightbox)

3. Sélectionnez le **numéro** de la liste des entités prédéfinies puis sélectionnez **Done** (Terminé).

    ![Capture d’écran de la sélection du nombre dans la boîte de dialogue des entités prédéfinies](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS
LUIS ne connaît pas les modifications apportées aux intentions et aux entités (modèle) tant que son apprentissage n’a pas été effectué. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Effectuer l’apprentissage**.

    ![Effectuer l’apprentissage de l’application](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. L’apprentissage est terminé lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

    ![Apprentissage réussi](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publier l’application pour obtenir l’URL de point de terminaison
Pour obtenir une prédiction LUIS dans un chatbot ou une autre application, vous devez publier l’application. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Publier**. 

2. Sélectionnez l’emplacement Production et le bouton **Publier**.

    [![](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png "Capture d’écran de la page Publier avec le bouton Publier vers l’emplacement Production mis en surbrillance")](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png#lightbox)

3. La publication est terminée lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Interroger le point de terminaison avec un autre énoncé
1. Dans la page **Publier**, sélectionnez le lien **Point de terminaison** en bas de la page. Cette action ouvre une autre fenêtre de navigateur avec l’URL de point de terminaison affichée dans la barre d’adresses. 

    [![](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png "Capture d’écran de la page Publier avec l’URL du point de terminaison mise en surbrillance")](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png#lightbox)

2. Allez à la fin de l’URL dans la barre d’adresses, puis entrez `Please relocation jill-jones@mycompany.com from x-2345 to g-23456`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `MoveEmployee` avec l’entité hiérarchique extraite.

```JSON
{
  "query": "Please relocation jill-jones@mycompany.com from x-2345 to g-23456",
  "topScoringIntent": {
    "intent": "MoveEmployee",
    "score": 0.9966052
  },
  "intents": [
    {
      "intent": "MoveEmployee",
      "score": 0.9966052
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.0325253047
    },
    {
      "intent": "FindForm",
      "score": 0.006137873
    },
    {
      "intent": "GetJobInformation",
      "score": 0.00462633232
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00415637763
    },
    {
      "intent": "ApplyForJob",
      "score": 0.00382325822
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00249120337
    },
    {
      "intent": "None",
      "score": 0.00130756292
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.00119622645
    },
    {
      "intent": "Utilities.Confirm",
      "score": 1.26910036E-05
    }
  ],
  "entities": [
    {
      "entity": "jill - jones @ mycompany . com",
      "type": "Employee",
      "startIndex": 18,
      "endIndex": 41,
      "resolution": {
        "values": [
          "Employee-45612"
        ]
      }
    },
    {
      "entity": "x - 2345",
      "type": "Locations::Origin",
      "startIndex": 48,
      "endIndex": 53,
      "score": 0.8520272
    },
    {
      "entity": "g - 23456",
      "type": "Locations::Destination",
      "startIndex": 58,
      "endIndex": 64,
      "score": 0.974032
    },
    {
      "entity": "-2345",
      "type": "builtin.number",
      "startIndex": 49,
      "endIndex": 53,
      "resolution": {
        "value": "-2345"
      }
    },
    {
      "entity": "-23456",
      "type": "builtin.number",
      "startIndex": 59,
      "endIndex": 64,
      "resolution": {
        "value": "-23456"
      }
    }
  ]
}
```

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>Auriez-vous pu utiliser une expression régulière pour chaque emplacement ?
Oui, créez l’expression régulière avec les rôles d’origine et de destination et utilisez-les dans un modèle.

Dans cet exemple, les emplacements tels que `a-1234`, suivent un format spécifiques : une ou deux lettres, suivies d’un tiret puis d’une série de 4 à 5 chiffres. Ces données peuvent être décrites comme une entité à expression régulière avec un rôle pour chaque emplacement. Les rôles sont disponibles pour les modèles. Vous pouvez créer des modèles basés sur ces énoncés, puis créer une expression régulière pour le format de l’emplacement et l’ajouter aux modèles. <!-- Go to this tutorial to see how that is done -->

## <a name="what-has-this-luis-app-accomplished"></a>Quel est l’accomplissement de cette application LUIS ?
Cette application, comptant seulement quelques intentions et une entité hiérarchique, a identifié une intention de requête en langage naturel et retourné les données extraites. 

Votre chatbot a maintenant suffisamment d’informations pour déterminer l’action principale, `MoveEmployee`, et les informations d’emplacement trouvées dans l’énoncé. 

## <a name="where-is-this-luis-data-used"></a>Où ces données LUIS sont-elles utilisées ? 
LUIS en a fini avec cette requête. L’application d’appel, par exemple un chatbot, peut prendre le résultat topScoringIntent et les données de l’entité pour passer à l’étape suivante. LUIS n’effectue pas ce travail de programmation pour le robot ou l’application d’appel. LUIS détermine uniquement l’intention de l’utilisateur. 

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, supprimez l’application LUIS. Sélectionnez les points de suspension (***...***) à droite du nom de l’application dans la liste des applications, sélectionnez **Supprimer**. Dans la boîte de dialogue contextuelle **Supprimer l’application ?**, sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Découvrez comment ajouter une entité composite](luis-tutorial-composite-entity.md) 