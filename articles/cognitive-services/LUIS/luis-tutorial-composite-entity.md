---
title: 'Tutoriel : Tutoriel sur l’entité composite - LUIS'
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez ajouter une entité composite pour regrouper les données extraites dans une seule entité contenante. Grâce au regroupement des données, l’application cliente peut extraire facilement les données associées dans différents types de données.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: f2b2c3f52610cd9fae0845b15aebf032a088000b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447944"
---
# <a name="tutorial-group-and-extract-related-data"></a>Tutoriel : Regrouper et extraire les données associées
Dans ce tutoriel, vous allez ajouter une entité composite pour regrouper les données extraites dans une seule entité contenante. Grâce au regroupement des données, l’application cliente peut extraire facilement les données associées dans différents types de données.

L’objectif de l’entité composite est de regrouper des entités connexes dans une entité de catégorie parente. Les informations existent en tant qu’entités distinctes avant la création d’un composite.

Elle est adaptée à ce type de données, car les données :

* Sont associées.
* Utilisent divers types d’entités.
* Doivent être regroupées et traitées par l’application cliente en tant qu’unité d’informations.

**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importer l’exemple d’application
> * Créer une intention
> * Ajouter une entité composite
> * Former
> * Publish
> * Obtenir les intentions et les entités à partir du point de terminaison

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importer l’exemple d’application

1.  Téléchargez le [fichier JSON de l’application](
https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json?raw=true) à partir du tutoriel d’entité de liste et enregistrez-le.

2. Importez le fichier JSON dans une nouvelle application à partir du [portail LUIS](https://www.luis.ai).

3. À partir de la section **Manage (Gérer)** , sous l’onglet **Versions**, clonez la version et nommez-la `composite`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL.

## <a name="composite-entity"></a>Entité composite

Dans cette application, le nom du service est défini dans l’entité de liste **Department** et inclut des synonymes.

L’intention **TransferEmployeeToDepartment** comprend des exemples d’énoncés pour demander l’affectation d’un employé à un nouveau service.

Exemples d’énoncés pour cette intention :

|Exemples d’énoncés|
|--|
|Affecter John W. Smith au service de comptabilité|
|Transférer Jill Jones à partir du service R&D|

La demande d’affectation doit inclure le nom du service et le nom de l’employé.

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Ajouter l’entité prédéfini PersonName pour faciliter l’extraction de types de données courants

LUIS fournit plusieurs entités prédéfinies pour l’extraction de données courantes.

1. Sélectionnez **Build** (Générer) dans le volet de navigation supérieur, puis **Entities** (Entités) dans le menu de navigation gauche.

1. Sélectionnez le bouton **Gérer l’entité prédéfinie**.

1. Sélectionnez **[PersonName](luis-reference-prebuilt-person.md)** dans la liste des entités prédéfinies, puis **Done** (Terminé).

    ![Capture d’écran de la sélection du nombre dans la boîte de dialogue des entités prédéfinies](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Cette entité vous permet d’ajouter la reconnaissance de nom à votre application cliente.

## <a name="create-composite-entity-from-example-utterances"></a>Créer une entité composite à partir d’exemples d’énoncés

1. Sélectionnez **Intentions** dans le volet de navigation gauche.

1. Sélectionnez **TransferEmployeeToDepartment** dans la liste des intentions.

1. Dans l’énoncé `place John Jackson in engineering`, sélectionnez l’entité personName, `John Jackson`, puis **Inclure dans un wrapper d’entité composite** dans la liste de menu contextuel pour l’énoncé suivant.

    ![Capture d'écran de la sélection de l’encapsulage d’entité composite dans la boîte de dialogue déroulante](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Puis sélectionnez immédiatement la dernière entité, `engineering`, dans l’énoncé. Une barre verte sous les termes sélectionnés indique une entité composite. Dans le menu contextuel, entrez le nom composite `TransferEmployeeInfo`, puis appuyez sur Entrée.

    ![Capture d'écran de la sélection du nom de l’entité composite dans la boîte de dialogue déroulante](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. Dans **What type of entity do you want to create?** (Quel type d’entité souhaitez-vous créer ?), tous les champs obligatoires sont dans la liste : `personName` et `Department`. Sélectionnez **Terminé**. Notez que l’entité prédéfinie, personName, a été ajoutée à l’entité composite. Si vous pouvez avoir une entité prédéfinie apparaissant entre les jetons de début et de fin d’une entité composite, celle-ci doit contenir ces entités prédéfinies. Si les entités prédéfinies ne sont pas incluses, l’entité composite n’est pas correctement prédite mais chaque élément individuel l’est.

    ![Capture d'écran de la sélection du nom de l’entité composite dans la boîte de dialogue déroulante](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Énoncés d’exemples d’étiquettes avec une entité composite

1. Dans chaque exemple d’énoncé, sélectionnez l’entité la plus à gauche, qui devrait se trouver dans le composite. Ensuite, sélectionnez **Inclure dans un wrapper d’entité composite**.

1. Sélectionnez le dernier mot dans l’entité composite, puis **TransferEmployeeInfo** dans le menu contextuel.

1. Vérifiez que tous les énoncés de l’intention sont étiquetés avec l’entité composite.

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Entraîner l’application pour que les changements apportés à l’intention puissent être testés

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publier l’application pour que le modèle entraîné soit interrogeable à partir du point de terminaison

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obtenir l’intention et la prédiction d’entité à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Move Jill Jones to DevOps`. Le dernier paramètre de la chaîne de requête est `q`, la requête de l’énoncé.

    Étant donné que ce test consiste à vérifier que le composite est extrait correctement, un test peut inclure soit un exemple d’énoncé existant, soit un nouvel énoncé. Un bon test consiste à inclure toutes les entités enfants dans l’entité composite.

    ```json
    {
      "query": "Move Jill Jones to DevOps",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9882747
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9882747
        },
        {
          "intent": "None",
          "score": 0.00925369747
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 5,
          "endIndex": 14
        },
        {
          "entity": "devops",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 24,
          "resolution": {
            "values": [
              "Development Operations"
            ]
          }
        },
        {
          "entity": "jill jones to devops",
          "type": "TransferEmployeeInfo",
          "startIndex": 5,
          "endIndex": 24,
          "score": 0.9607566
        }
      ],
      "compositeEntities": [
        {
          "parentType": "TransferEmployeeInfo",
          "value": "jill jones to devops",
          "children": [
            {
              "type": "builtin.personName",
              "value": "jill jones"
            },
            {
              "type": "Department",
              "value": "devops"
            }
          ]
        }
      ]
    }
    ```

   Cet énoncé retourne un tableau d’entités composites. Chaque entité se voit attribuer un type et une valeur. Pour une plus grande précision de chaque entité enfant, utilisez la combinaison de type et de valeur de l’élément de tableau composite pour trouver l’élément correspondant dans le tableau d’entités.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informations connexes

* [Tutoriel sur les entités de liste](luis-quickstart-intents-only.md)
* Informations conceptuelles sur les [entités composites](luis-concept-entity-types.md)
* [Guide pratique pour entraîner](luis-how-to-train.md)
* [Comment publier](luis-how-to-publish-app.md)
* [Guide pratique pour tester dans le portail LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel a créé une entité composite pour encapsuler des entités existantes. Cela permet à l’application cliente de trouver un groupe de données associées dans différents types de données afin de poursuivre la conversation. Une application cliente pour cette application de ressources humaines peut demander le jour et l’heure de début et de fin du déplacement. Elle peut également demander des informations concernant d’autres aspects logistiques du déplacement, comme un téléphone physique.

> [!div class="nextstepaction"]
> [Corriger les prédictions incertaines en révisant les énoncés de point de terminaison](luis-tutorial-review-endpoint-utterances.md)
