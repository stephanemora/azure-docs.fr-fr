---
title: 'Tutoriel 6 : Extraire des données composites avec une entité composite LUIS'
titleSuffix: Azure Cognitive Services
description: Ajoutez une entité composite pour regrouper les données extraites de différents types dans une seule entité contenante. Grâce au regroupement des données, l’application cliente peut extraire facilement les données associées dans différents types de données.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 8f7edecf1abd1f01a2f40f1420a6a85224271239
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52423499"
---
# <a name="tutorial-6-group-and-extract-related-data"></a>Tutoriel 6 : Regrouper et extraire des données associées
Dans ce tutoriel, vous allez ajouter une entité composite pour regrouper les données extraites dans une seule entité contenante. Grâce au regroupement des données, l’application cliente peut extraire facilement les données associées dans différents types de données.

L’objectif de l’entité composite est de regrouper des entités connexes dans une entité de catégorie parente. Les informations existent en tant qu’entités distinctes avant la création d’un composite. L’entité composite est similaire à une entité hiérarchique, mais elle peut contenir différents types d’entités. 

Elle est adaptée à ce type de données, car les données :

* Sont associées. 
* Utilisent divers types d’entités.
* Doivent être regroupées et traitées par l’application cliente en tant qu’unité d’informations.

**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Utiliser l’application de tutoriel existante
> * Ajouter une entité composite 
> * Former
> * Publish
> * Obtenir les intentions et les entités à partir du point de terminaison

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Utiliser l’application existante
Continuez avec l’application créée dans le dernier tutoriel, nommée **HumanResources**. 

Si vous n’avez pas l’application HumanResources du tutoriel précédent, effectuez les étapes suivantes :

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-hier-HumanResources.json).

2. Importez le code JSON dans une nouvelle application.

3. À partir de la section **Manage (Gérer)**, sous l’onglet **Versions**, clonez la version et nommez-la `composite`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL.


## <a name="composite-entity"></a>Entité composite
Créer une entité composite lorsque des entités distinctes peuvent être regroupées logiquement et que ce regroupement logique est utile l’application cliente. 

Dans cette application, le nom de l’employé est défini dans la liste d’entités **Employé** et inclut des synonymes du nom, l’adresse de messagerie, le numéro de poste téléphonique professionnel, le numéro de téléphone mobile et États-Unis. ID taxe fédérale. 

L’intention **MoveEmployee** a des exemples d’énoncés pour demander le déménagement d’un employé d’un bâtiment et d’un bureau à un autre. Les noms des bâtiments sont des lettres : « A », « B », etc., tandis que ceux des bureaux sont des nombres : « 1234 », « 13245 ». 

Les exemples d’énoncés dans l’intention **MoveEmployee** incluent :

|Exemples d’énoncés|
|--|
|Faire déménager John W. Smith vers a-2345|
|déplacer x12345 vers h-1234 demain|
 
La demande de déplacement doit inclure l’employé (n’importe quel synonyme) et l’emplacement du bâtiment et du bureau de destination. La demande peut également inclure le bureau d’origine ainsi qu’une date où le déménagement doit avoir lieu. 

Les données extraites du point de terminaison doivent contenir ces informations et les retourner dans l’entité composite `RequestEmployeeMove` :

```JSON
"compositeEntities": [
  {
    "parentType": "RequestEmployeeMove",
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
]
```

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Dans la page **Intentions**, sélectionnez l’intention **MoveEmployee**. 

3. Sélectionnez l’icône de loupe sur la barre d’outils pour filtrer la liste des énoncés. 

    [![](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "Capture d’écran de LUIS sur l’intention « MoveEmployee » avec le bouton de loupe mis en surbrillance")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. Entrez `tomorrow` dans la zone de texte de filtre pour rechercher l’énoncé `shift x12345 to h-1234 tomorrow`.

    [![](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "Capture d’écran de LUIS sur l’intention « MoveEmployee » avec le filtre de « demain » mis en surbrillance")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    Une autre méthode consiste à filtrer l’entité par datetimeV2, en sélectionnant **Filtres de l’entité** puis à sélectionner **datetimeV2** dans la liste. 

5. Sélectionnez la première entité, `Employee`, puis sélectionnez **Inclure dans un wrapper d’entité composite** dans la liste du menu contextuel. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-1.png "Capture d’écran de LUIS sur intention « MoveEmployee » en sélectionnant la première entité composite mise en surbrillance")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. Puis sélectionnez immédiatement la dernière entité, `datetimeV2`, dans l’énoncé. Une barre verte sous les termes sélectionnés indique une entité composite. Dans le menu contextuel, entrez le nom composite `RequestEmployeeMove`, puis appuyez sur Entrée. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-2.png "Capture d’écran de LUIS sur intention « MoveEmployee » en sélectionnant la dernière entité dans le composite et en créant une entité mise en surbrillance")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. Dans **Quel type d’entité souhaitez-vous créer ?**, presque tous les champs obligatoires sont dans la liste. Seul l’emplacement d’origine est manquant. Sélectionnez **Ajouter une entité enfant**, puis **Locations::Origin** dans la liste des entités existantes et, pour terminer **fait**. 

    Notez que l’entité prédéfinie, number, a été ajoutée à l’entité composite. Si vous pouvez avoir une entité prédéfinie apparaissant entre les jetons de début et de fin d’une entité composite, celle-ci doit contenir ces entités prédéfinies. Si les entités prédéfinies ne sont pas incluses, l’entité composite n’est pas correctement prédite mais chaque élément individuel l’est.

    ![Capture d’écran de LUIS sur l’intention « MoveEmployee » ajoutant une autre entité dans la fenêtre contextuelle](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. Sélectionnez la loupe dans la barre d’outils pour supprimer le filtre. 

9. Supprimez le mot `tomorrow` du filtre afin de voir à nouveau tous les exemples d’énoncés. 

## <a name="label-example-utterances-with-composite-entity"></a>Énoncés d’exemples d’étiquettes avec une entité composite


1. Dans chaque exemple d’énoncé, sélectionnez l’entité la plus à gauche, qui devrait se trouver dans le composite. Ensuite, sélectionnez **Inclure dans un wrapper d’entité composite**.

    [![](media/luis-tutorial-composite-entity/hr-label-entity-1.png "Capture d’écran de LUIS sur intention « MoveEmployee » en sélectionnant la première entité composite mise en surbrillance")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. Sélectionnez le dernier mot dans l’entité composite, puis **RequestEmployeeMove** dans le menu contextuel. 

    [![](media/luis-tutorial-composite-entity/hr-label-entity-2.png "Capture d’écran de LUIS sur intention « MoveEmployee » en sélectionnant la dernière entité composite mise en surbrillance")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. Vérifiez que tous les énoncés de l’intention sont étiquetés avec l’entité composite. 

    [![](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "Capture d’écran de LUIS sur « MoveEmployee » avec tous les énoncés étiquetés")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train"></a>Former

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publish

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obtenir l’intention et les entités à partir du point de terminaison 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

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
          "type": "RequestEmployeeMove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "RequestEmployeeMove",
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
      ]
    }
    ```

  Cet énoncé retourne un tableau d’entités composites. Chaque entité se voit attribuer un type et une valeur. Pour une plus grande précision de chaque entité enfant, utilisez la combinaison de type et de valeur de l’élément de tableau composite pour trouver l’élément correspondant dans le tableau d’entités.  

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel a créé une entité composite pour encapsuler des entités existantes. Cela permet à l’application cliente de trouver un groupe de données associées dans différents types de données afin de poursuivre la conversation. Une application cliente pour cette application de ressources humaines peut demander le jour et l’heure de début et de fin du déplacement. Elle peut également demander des informations concernant d’autres aspects logistiques du déplacement, comme un téléphone physique. 

> [!div class="nextstepaction"] 
> [Découvrez comment ajouter une entité simple avec une liste d’expressions](luis-quickstart-primary-and-secondary-data.md)  
