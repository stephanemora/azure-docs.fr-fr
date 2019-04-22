---
title: Correspondance de texte exacte
titleSuffix: Azure Cognitive Services
description: Obtenez des données qui correspondent à une liste d’éléments prédéfinie. Chaque élément de la liste peut avoir des synonymes qui correspondent également exactement
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 9083227dd81dca219666e07b70f487069413855d
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521269"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance"></a>Didacticiel : Obtenir des données exactes correspondant au texte d’un énoncé

Ce tutoriel explique comment obtenir des données d’entité correspondant à une liste de termes prédéfinie. 

**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Créer une application
> * Ajouter une intention
> * Ajouter une entité de liste 
> * Former 
> * Publish
> * Obtenir les intentions et les entités à partir du point de terminaison

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Qu’est-ce qu’une entité de liste ?

Une entité de liste est une correspondance de texte exacte par rapport aux mots de l’énoncé. 

Chaque élément de la liste peut inclure une liste de synonymes. Pour l’application de ressources humaines, un service de l’entreprise peut être identifié par plusieurs informations clés, telles qu’un nom officiel, un acronyme courant et un codes de facturation interne. 

L’application de ressources humaines doit déterminer le service vers lequel un employé est transféré. 

Une entité de type liste est un choix approprié pour ce type de données lorsque :

* Les valeurs des données sont un ensemble connu.
* L’ensemble ne dépasse pas les [limites](luis-boundaries.md) maximum de LUIS pour ce type d’entité.
* Le texte de l’énoncé est une correspondance exacte avec un synonyme ou le nom canonique. LUIS n’utilise pas la liste au-delà des correspondances de texte exactes. Une simple entité de liste ne suffit pas pour résoudre la recherche de radical, les pluriels et autres variantes. Pour gérer les variantes, envisagez d’utiliser un [modèle](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) avec la syntaxe de texte facultative. 

## <a name="create-a-new-app"></a>Créer une application

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-transfer-employees-to-a-different-department"></a>Créer une intention de transférer des employés vers un autre service

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Sélectionnez **Créer une intention**. 

3. Entrez `TransferEmployeeToDepartment` dans la boîte de dialogue contextuelle, puis sélectionnez **Terminé**. 

    ![Capture d’écran de la boîte de dialogue contextuelle Créer une nouvelle intention](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Ajoutez des exemples d’énoncés à l’intention.

    |Exemples d’énoncés|
    |--|
    |Affecter John W. Smith au service de comptabilité|
    |Transférer Jill Jones à partir de R&D|
    |Le service 1234 a un nouveau membre nommé Bill Bradstreet|
    |Placer John Jackson en ingénierie |
    |Déplacer Debra Doughtery vers les Ventes internes|
    |Dép Jill Jones vers le service informatique|
    |Muter Alice Anderson vers DevOps|
    |Carl Chamerlin au service financier|
    |Steve Standish vers 1234|
    |Tanner Thompson vers 3456|

    [![Capture d’écran de l’intention avec des exemples d’énoncés](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png "Capture d’écran de l’intention avec des exemples d’énoncés")](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="department-list-entity"></a>Entité de liste de service

L’intention **TransferEmployeeToDepartment** disposant désormais d’exemples d’énoncés, LUIS doit comprendre ce qu’est un service. 

Le nom _canonique_ principal pour chaque élément est le nom de service. Voici des exemples de synonymes de chaque nom canonique : 

|Nom canonique|Synonymes|
|--|--|
|Comptabilité|Cptb<br>Compta<br>3456|
|Opérations de développement|DevOps<br>4949|
|Ingénierie|ing<br>ingén<br>4567|
|Finances|fin<br>2020|
|Technologies de l’information|IT<br>2323|
|Ventes internes|ventesi<br>ventesin<br>1414|
|Recherche et développement|R&D<br>1234|

1. Dans le panneau gauche, sélectionnez **Entités**.

1. Sélectionnez **Créer une entité**.

1. Dans la fenêtre de dialogue contextuelle des entités, saisir `Department` dans le nom d’entité, et **List** pour le type d’entité. Sélectionnez **Terminé**.  

    [![Capture d’écran de la boîte de dialogue contextuelle de création d’une entité](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png "Capture d’écran de la boîte de dialogue contextuelle de création d’une entité")](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png#lightbox)

1. Dans la page Department entity, entrez la valeur `Accounting`.

    [![Capture d’écran de saisie de valeur](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "Capture d’écran de saisie de valeur")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

1. Pour Synonyms, ajoutez les synonymes à partir du tableau précédent.

    [![Capture d’écran de saisie de synonymes](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "Capture d’écran de saisie de synonymes")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

1. Faites de même pour tous les noms canoniques et leurs synonymes. 

## <a name="add-example-utterances-to-the-none-intent"></a>Ajouter des exemples d’énoncés à l’intention « None » 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Entraîner l’application pour que les modifications apportées à l’intention puissent être testées 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publier l’application pour que le modèle entraîné soit interrogeable à partir du point de terminaison

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obtenir l’intention et la prédiction d’entité à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `shift Joe Smith to IT`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **q**uery. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `TransferEmployeeToDepartment` avec `Department` extrait.

   ```json
    {
      "query": "shift Joe Smith to IT",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9775754
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9775754
        },
        {
          "intent": "None",
          "score": 0.0154493852
        }
      ],
      "entities": [
        {
          "entity": "it",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 20,
          "resolution": {
            "values": [
              "Information Technology"
            ]
          }
        }
      ]
    }
   ```

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informations connexes

* Informations conceptuelles sur les [entités de liste](luis-concept-entity-types.md#list-entity)
* [Guide pratique pour entraîner](luis-how-to-train.md)
* [Comment publier](luis-how-to-publish-app.md)
* [Guide pratique pour tester dans le portail LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez créé une intention, ajouté des exemples d’énoncés, puis créé une entité de liste pour extraire des correspondances de texte exactes à partir des énoncés. Une fois l’application entraînée et publiée, une requête au point de terminaison a identifié l’intention et retourné les données extraites.

Poursuivez avec cette application, [en ajoutant une entité composite](luis-tutorial-composite-entity.md).

> [!div class="nextstepaction"]
> [Ajouter une entité prédéfinie avec un rôle à l’application](tutorial-entity-roles.md)

