---
title: Entité hiérarchique
titleSuffix: Azure Cognitive Services
description: Recherchez des ensembles de données connexes en fonction du contexte. Par exemple, les emplacements d’origine et de destination d’un déménagement physique depuis un bâtiment et un bureau vers un autre bâtiment et un autre bureau sont liés.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 91a9646e88adbfaf6d3c3fc0b06b341c647e773f
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753691"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Didacticiel : Extraire des données connexes en fonction du contexte d’un énoncé

Dans ce tutoriel, vous allez rechercher des ensembles de données connexes en fonction du contexte. Par exemple, les emplacements d’origine et de destination pour un transfert entre une ville à une autre. Les deux ensembles de données peuvent être nécessaires et ils sont liés entre eux.  

**Dans ce tutoriel, vous allez découvrir comment :**

> [!div class="checklist"]
> * Créer une application
> * Ajouter une intention 
> * Ajouter une entité hiérarchique avec des enfants d’origine et de destination
> * Former
> * Publish
> * Obtenir les intentions et les entités à partir du point de terminaison

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="hierarchical-data"></a>Données hiérarchiques

Cette application détermine les villes d’origine et de destination entre lesquelles un employé doit être déplacé. Elle utilise l’entité hiérarchique pour détermine les emplacements au sein de l’énoncé. 

L’entité hiérarchique est adaptée à ce type de données, car les deux ensembles de données, les emplacements enfants :

* Sont des entités simples.
* Sont liés l’un à l’autre dans le contexte de l’énoncé.
* Utilisent des formulations spécifiques pour indiquer chaque entité. Exemples : de/à, quitte/arrive à, loin de/vers...
* Les deux enfants se trouvent fréquemment dans le même énoncé. 
* Doivent être regroupées et traitées par l’application cliente en tant qu’unité d’informations.

## <a name="create-a-new-app"></a>Créer une application

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Créer une intention de déplacer des employés entre des villes

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Sélectionnez **Créer une intention**. 

1. Entrez `MoveEmployeeToCity` dans la boîte de dialogue contextuelle, puis sélectionnez **Terminé**. 

    ![Capture d’écran de la boîte de dialogue contextuelle Créer une nouvelle intention](./media/luis-quickstart-intent-and-hier-entity/create-new-intent-move-employee-to-city.png)

1. Ajoutez des exemples d’énoncés à l’intention.

    |Exemples d’énoncés|
    |--|
    |Déplacer John W. Smith de Seattle à Dallas|
    |Transférer Jill Jones de Seattle vers Le Caire|
    |Éloigner John Jackson de Tampa, destination Atlanta |
    |Déplacer Debra Doughtery de Tulsa vers Dallas|
    |Dép Jill Jones du Caire vers Tampa|
    |Muter Alice Anderson de Paris à Redmond|
    |Carl Chamerlin de San Francisco à Redmond|
    |Transférer Steve Standish de San Diego vers Bellevue |
    |Retirer Tanner Thompson de Kansas City et l’envoyer à Chicago|

    [ ![Capture d’écran de LUIS avec les nouveaux énoncés dans l’intention MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

## <a name="create-a-location-entity"></a>Créer une entité d’emplacement
LUIS a besoin de comprendre ce qu’est un emplacement en étiquetant l’origine et la destination dans les énoncés. Si vous devez voir l’énoncé dans l’affichage du jeton (brut), sélectionnez le bouton bascule dans la barre au-dessus des énoncés dénommé **Entities View** (affichage d’entités). Après avoir activé le bouton bascule , la commande s’appelle **Tokens View** (Vue des jetons).

Prenez l’énoncé suivant :

```json
move John W. Smith leaving Seattle headed to Dallas
```

Deux emplacements sont indiqués dans l’énoncé : `Seattle` et `Dallas`. Ils soient groupés en tant qu’enfants d’une même entité hiérarchique, `Location` car les deux ensembles de données doivent être extraits de l’énoncé pour que soit traitée la demande dans l’application cliente et sont liés l’un à l’autre. 
 
Si un seul enfant (origine ou destination) d’une entité hiérarchique est présent, il est tout de même extrait. Il n’est pas nécessaire de trouver tous les enfants pour en extraire un seul ou quelques-uns. 

1. Dans l’énoncé `move John W. Smith leaving Seattle headed to Dallas`, sélectionnez le mot `Seattle`. Un menu déroulant apparaît avec une zone de texte à son sommet. Entrez le nom de l’entité `Location` dans la zone de texte, puis sélectionnez **Créer une entité** dans le menu déroulant. 

    [![Capture d'écran illustrant la création d'une entité sur la page d'intention](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png "Capture d'écran illustrant la création d'une entité sur la page d'intention")](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png#lightbox)

1. Dans la fenêtre contextuelle, sélectionnez le type d’entité **hiérarchique** avec `Origin` et `Destination` comme entités enfants. Sélectionnez **Terminé**.

    ![Capture d'écran de la boîte de dialogue contextuelle de création d'entité pour la nouvelle entité Emplacement](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Capture d'écran de la boîte de dialogue contextuelle de création d'entité pour la nouvelle entité Emplacement")

1. L’étiquette de `Seattle` est marquée comme `Location` car LUIS ne sait pas si le terme désigne l’origine ou la destination ou aucun des deux. Sélectionnez `Seattle`, puis **Emplacement**, suivez le menu à droite et cliquez sur `Origin`.

    [![Capture d'écran de la boîte de dialogue contextuelle d'étiquetage des entités permettant de changer l'enfant de l'entité Emplacements](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png "Capture d'écran de la boîte de dialogue contextuelle d'étiquetage des entités permettant de changer l'enfant de l'entité Emplacements")](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png#lightbox)

1. Étiquetez les autres emplacements dans tous les autres énoncés. Quand tous les emplacements sont marqués, les énoncés commencent à ressembler à un modèle. 

    [![Capture d'écran de l'entité Emplacements étiquetée dans les énoncés](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png "Capture d'écran de l'entité Emplacements étiquetée dans les énoncés")](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png#lightbox)

    Le trait de soulignement rouge indique que LUIS n’est pas sûr de l’entité. Un entraînement résout cette situation. 

## <a name="add-example-utterances-to-the-none-intent"></a>Ajouter des exemples d’énoncés à l’intention « None » 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Entraîner l’application pour que les modifications apportées à l’intention puissent être testées 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publier l’application pour que le modèle entraîné soit interrogeable à partir du point de terminaison

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obtenir l’intention et la prédiction d’entité à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Allez à la fin de l’URL dans la barre d’adresses, puis entrez `Please move Carl Chamerlin from Tampa to Portland`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `MoveEmployee` avec l’entité hiérarchique extraite.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.979823351
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.979823351
        },
        {
          "intent": "None",
          "score": 0.0156363435
        }
      ],
      "entities": [
        {
          "entity": "portland",
          "type": "Location::Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "tampa",
          "type": "Location::Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    L’intention correcte est prédite et le tableau d’entités a les valeurs d’origine et de destination dans la propriété **entities** correspondante.
    
## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informations connexes

* Informations conceptuelles sur les [entités hiérarchiques](luis-concept-entity-types.md)
* [Guide pratique pour entraîner](luis-how-to-train.md)
* [Comment publier](luis-how-to-publish-app.md)
* [Guide pratique pour tester dans le portail LUIS](luis-interactive-test.md)
* [Rôles et entités hiérarchiques](luis-concept-roles.md#roles-versus-hierarchical-entities)
* [Améliorer les prédictions avec des modèles](luis-concept-patterns.md)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé une intention et ajouté des exemples d’énoncés pour les données des emplacements d’origine et de destination issues d’un apprentissage contextuel. Une fois l’application entraînée et publiée, une application cliente peut utiliser ces informations pour créer un ticket de déménagement avec les informations appropriées.

> [!div class="nextstepaction"] 
> [Découvrez comment ajouter une entité composite](luis-tutorial-composite-entity.md) 
