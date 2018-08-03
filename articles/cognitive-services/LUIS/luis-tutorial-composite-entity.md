---
title: Tutoriel sur la création d’une entité composite pour extraire des données complexes - Azure | Microsoft Docs
description: Découvrez comment créer une entité composite dans votre application LUIS pour extraire différents types de données d’entités.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 07/30/2018
ms.author: diberry
ms.openlocfilehash: 9c84afc231ff4b086e76f50702870e30da7add6e
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364900"
---
# <a name="tutorial-6-add-composite-entity"></a>Tutoriel : 6. Ajouter une entité composite 
Dans ce tutoriel, ajoutez une entité composite pour regrouper les données extraites dans une entité les contenant.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendre des entités composites 
> * Ajouter une entité composite pour extraire des données
> * Entraîner et publier l’application
> * Interroger un point de terminaison de l’application pour voir la réponse JSON de LUIS

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Avant de commencer
Si vous ne disposez pas de l’application Ressources humaines du tutoriel [entité hiérarchique](luis-quickstart-intent-and-hier-entity.md), [importez](luis-how-to-start-new-app.md#import-new-app) le JSON dans une application du site Web [LUIS](luis-reference-regions.md#luis-website). L’application à importer se trouve dans le référentiel Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json).

Si vous souhaitez conserver l’application Ressources humaines d’origine, clonez la version sur la page [Paramètres](luis-how-to-manage-versions.md#clone-a-version), et nommez-la `composite`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine.  

## <a name="composite-entity-is-a-logical-grouping"></a>L’entité composite est un regroupement logique 
L’objectif de l’entité composite est de regrouper des entités connexes dans une entité de catégorie parente. Les informations existent en tant qu’entités distinctes avant la création d’un composite. Elle est similaire à une entité hiérarchique, mais peut contenir plusieurs types d’entités. 

 Créer une entité composite lorsque des entités distinctes peuvent être regroupées logiquement et que ce regroupement logique est utile l’application cliente. 

Dans cette application, le nom de l’employé est défini dans la liste d’entités **Employé** et inclut des synonymes du nom, l’adresse de messagerie, le numéro de poste téléphonique professionnel, le numéro de téléphone mobile et États-Unis. ID taxe fédérale. 

L’intention **MoveEmployee** a des exemples d’énoncés pour demander le déménagement d’un employé d’un bâtiment et d’un bureau à un autre. Les noms des bâtiments sont des lettres : « A », « B », etc., tandis que ceux des bureaux sont des nombres : « 1234 », « 13245 ». 

Les exemples d’énoncés dans l’intention **MoveEmployee** incluent :

|Exemples d’énoncés|
|--|
|Faire déménager John W. Smith vers a-2345|
|déplacer x12345 vers h-1234 demain|
 
La demande de déplacement doit inclure au moins l’employé (n’importe quel synonyme) et l’emplacement du bâtiment et du bureau de destination. La demande peut également inclure le bureau d’origine ainsi qu’une date où le déménagement doit avoir lieu. 

Les données extraites du point de terminaison doivent contenir ces informations et les retourner dans une entité composite `RequestEmployeeMove`. 

## <a name="create-composite-entity"></a>Créer une entité composite
1. Assurez-vous que votre application Ressources humaines figure dans la section **Générer** de LUIS. Vous pouvez modifier cette section en sélectionnant **Générer** dans la barre de menu en haut à droite. 

2. Dans la page **Intentions**, sélectionnez l’intention **MoveEmployee**. 

3. Sélectionnez l’icône de loupe sur la barre d’outils pour filtrer la liste des énoncés. 

    [![](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "Capture d’écran de LUIS sur l’intention « MoveEmployee » avec le bouton de loupe mis en surbrillance")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. Entrez `tomorrow` dans la zone de texte de filtre pour rechercher l’énoncé `shift x12345 to h-1234 tomorrow`.

    [![](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "Capture d’écran de LUIS sur l’intention « MoveEmployee » avec le filtre de « demain » mis en surbrillance")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    Une autre méthode consiste à filtrer l’entité par datetimeV2, en sélectionnant **Filtres de l’entité** puis à sélectionner **datetimeV2** dans la liste. 

5. Sélectionnez la première entité, `Employee`, puis sélectionnez **Inclure dans un wrapper d’entité composite** dans la liste du menu contextuel. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-1.png "Capture d’écran de LUIS sur intention « MoveEmployee » en sélectionnant la première entité composite mise en surbrillance")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. Puis sélectionnez immédiatement la dernière entité, `datetimeV2`, dans l’énoncé. Une barre verte sous les termes sélectionnés indique une entité composite. Dans le menu contextuel, entrez le nom composite `RequestEmployeeMove` puis sélectionnez **Créer un composite** sur dans le menu contextuel. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-2.png "Capture d’écran de LUIS sur intention « MoveEmployee » en sélectionnant la dernière entité dans le composite et en créant une entité mise en surbrillance")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. Dans **Quel type d’entité souhaitez-vous créer ?**, presque tous les champs obligatoires sont dans la liste. Seul l’emplacement d’origine est manquant. Sélectionnez **Ajouter une entité enfant**, puis **Locations::Origin** dans la liste des entités existantes et, pour terminer **fait**. 

    ![Capture d’écran de LUIS sur l’intention « MoveEmployee » ajoutant une autre entité dans la fenêtre contextuelle](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. Sélectionnez la loupe dans la barre d’outils pour supprimer le filtre. 

## <a name="label-example-utterances-with-composite-entity"></a>Énoncés d’exemples d’étiquettes avec une entité composite
1. Dans chaque exemple d’énoncé, sélectionnez l’entité la plus à gauche, qui devrait se trouver dans le composite. Ensuite, sélectionnez **Inclure dans un wrapper d’entité composite**.

    [![](media/luis-tutorial-composite-entity/hr-label-entity-1.png "Capture d’écran de LUIS sur intention « MoveEmployee » en sélectionnant la première entité composite mise en surbrillance")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. Sélectionnez le dernier mot dans l’entité composite, puis **RequestEmployeeMove** dans le menu contextuel. 

    [![](media/luis-tutorial-composite-entity/hr-label-entity-2.png "Capture d’écran de LUIS sur intention « MoveEmployee » en sélectionnant la dernière entité composite mise en surbrillance")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. Vérifiez que tous les énoncés de l’intention sont étiquetés avec l’entité composite. 

    [![](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "Capture d’écran de LUIS sur « MoveEmployee » avec tous les énoncés étiquetés")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS
LUIS ne connaît pas la nouvelle entité composite jusqu'à ce que l’application soit formée. 

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publier l’application pour obtenir l’URL de point de terminaison

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint"></a>Demander le point de terminaison 

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Move Jill Jones from a-1234 to z-2345 on March 3 2 p.m.`. Le dernier paramètre de la chaîne de requête est `q`, la requête de l’énoncé. 

    Étant donné que ce test consiste à vérifier que le composite est extrait correctement, un test peut inclure soit un exemple d’énoncé existant, soit un nouvel énoncé. Un bon test consiste à inclure toutes les entités enfants dans l’entité composite.

    ```JSON
    {
      "query": "Move Jill Jones from a-1234 to z-2345 on March 3  2 p.m",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9959525
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9959525
        },
        {
          "intent": "GetJobInformation",
          "score": 0.009858314
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00728598563
        },
        {
          "intent": "FindForm",
          "score": 0.0058053555
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.005371796
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00266987388
        },
        {
          "intent": "None",
          "score": 0.00123299169
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00116407464
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00102653319
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0006628214
        }
      ],
      "entities": [
        {
          "entity": "march 3 2 p.m",
          "type": "builtin.datetimeV2.datetime",
          "startIndex": 41,
          "endIndex": 54,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2018-03-03 14:00:00"
              },
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2019-03-03 14:00:00"
              }
            ]
          }
        },
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 5,
          "endIndex": 14,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "z - 2345",
          "type": "Locations::Destination",
          "startIndex": 31,
          "endIndex": 36,
          "score": 0.9690751
        },
        {
          "entity": "a - 1234",
          "type": "Locations::Origin",
          "startIndex": 21,
          "endIndex": 26,
          "score": 0.9713137
        },
        {
          "entity": "-1234",
          "type": "builtin.number",
          "startIndex": 22,
          "endIndex": 26,
          "resolution": {
            "value": "-1234"
          }
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 32,
          "endIndex": 36,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 47,
          "endIndex": 47,
          "resolution": {
            "value": "3"
          }
        },
        {
          "entity": "2",
          "type": "builtin.number",
          "startIndex": 50,
          "endIndex": 50,
          "resolution": {
            "value": "2"
          }
        },
        {
          "entity": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "type": "requestemployeemove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "requestemployeemove",
          "value": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "children": [
            {
              "type": "builtin.datetimeV2.datetime",
              "value": "march 3 2 p.m"
            },
            {
              "type": "Locations::Destination",
              "value": "z - 2345"
            },
            {
              "type": "Employee",
              "value": "jill jones"
            },
            {
              "type": "Locations::Origin",
              "value": "a - 1234"
            }
          ]
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
    }
    ```

  Cet énoncé retourne un tableau d’entités composites. Chaque entité se voit attribuer un type et une valeur. Pour une plus grande précision de chaque entité enfant, utilisez la combinaison de type et de valeur de l’élément de tableau composite pour trouver l’élément correspondant dans le tableau d’entités.  

## <a name="what-has-this-luis-app-accomplished"></a>Quel est l’accomplissement de cette application LUIS ?
Cette application a identifié une intention de type requête en langage naturel et retourné les données extraites en tant que groupe nommé. 

Votre chatbot a maintenant suffisamment d’informations pour déterminer l’action principale et les détails connexes dans l’énoncé. 

## <a name="where-is-this-luis-data-used"></a>Où ces données LUIS sont-elles utilisées ? 
LUIS en a fini avec cette requête. L’application d’appel, par exemple un chatbot, peut prendre le résultat topScoringIntent et les données de l’entité pour passer à l’étape suivante. LUIS n’effectue pas ce travail de programmation pour le robot ou l’application d’appel. LUIS détermine uniquement l’intention de l’utilisateur. 

## <a name="clean-up-resources"></a>Supprimer les ressources

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Découvrez comment ajouter une entité simple avec une liste d’expressions](luis-quickstart-primary-and-secondary-data.md)  