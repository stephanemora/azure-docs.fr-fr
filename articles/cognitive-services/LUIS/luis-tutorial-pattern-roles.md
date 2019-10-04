---
title: 'Didacticiel : Données de rôles de modèle - LUIS'
titleSuffix: Azure Cognitive Services
description: Les modèles permettent d’extraire des données à partir d’énoncés de modèle au format approprié. L’énoncé de modèle utilise une entité simple et des rôles pour extraire des données associées telles que l’emplacement d’origine et l’emplacement de destination.
ms.custom: seodec18
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 7b95dcf6a93c9abdeab9520f0a0fd80eb17dccff
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70387637"
---
# <a name="tutorial-extract-contextually-related-patterns-using-roles"></a>Didacticiel : Extraire des modèles relatifs au contexte à l’aide de rôles

Dans ce tutoriel, vous allez utiliser un modèle pour extraire des données à partir d’un énoncé de modèle au format approprié. L’énoncé de modèle utilise une [entité simple](luis-concept-entity-types.md#simple-entity) et des [rôles](luis-concept-roles.md) pour extraire des données associées telles que la localisation d’origine et la localisation de destination.  Lorsque vous utilisez des modèles, l’intention nécessite moins d’exemples d’énoncés.


**Dans ce tutoriel, vous allez découvrir comment :**

> [!div class="checklist"]
> * Importer l’exemple d’application
> * Créer des entités
> * Créer une intention
> * Former
> * Publish
> * Obtenir les intentions et les entités à partir du point de terminaison
> * Créer un modèle avec des rôles
> * Créer une liste d’expressions de villes
> * Obtenir les intentions et les entités à partir du point de terminaison

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="using-roles-in-patterns"></a>Utilisation de rôles dans les modèles

L’objectif des rôles est d’extraire les entités liées au contexte d’un énoncé. Dans l’énoncé, les valeurs de ville d’origine (`Move new employee Robert Williams from Sacramento and San Francisco`) et de ville de destination sont liées et utilisent un langage commun pour indiquer chaque emplacement. 


Le nom du nouvel employé, Billy Patterson, ne fait pas encore partie de l’entité dans la liste des **employés**. Le nom du nouvel employé est d’abord extrait pour être envoyé vers un système externe afin de créer les informations d’identification de l’entreprise. Une fois les informations d’identification de l’entreprise créées, les informations d’identification des employés sont ajoutées à l’entité dans la liste des **employés**.

Le nouvel employé et sa famille doivent être transférés de la ville actuelle vers la ville où se trouve la société fictive. Étant donné qu’un nouvel employé peut venir de n’importe quelle ville, les emplacements doivent être détectés. Une liste définie comme une entité de liste ne fonctionnerait pas car seules les villes de la liste seront extraites.

Les noms de rôles associés aux villes d’origine et de destination doivent être uniques sur toutes les entités. Un moyen simple de s’assurer que les rôles sont uniques consiste à les associer à l’entité apparentée à l’aide d’une stratégie d’affectation de noms. L’entité **NewEmployeeRelocation** est une entité simple avec deux rôles : **NewEmployeeReloOrigin** et **NewEmployeeReloDestination**. Relo est l’abréviation de Relocation.

Comme l’exemple d’énoncé `Move new employee Robert Williams from Sacramento and San Francisco` contient uniquement des entités issues de l’apprentissage automatique, il est important de fournir suffisamment d’exemples d’énoncés à l’intention afin de détecter les entités.  

**Même si les modèles vous permettent de fournir moins d’exemples d’énoncés, si les entités ne sont pas détectées, le modèle ne correspond pas.**

Si vous avez des difficultés avec la détection d’une entité simple car elle représente un nom comme une ville, vous pouvez utiliser une liste d’expressions de valeurs similaires. Cela permet de détecter le nom de la ville en fournissant à LUIS des détails supplémentaires sur ce type de mot ou d’expression. Les listes d’expressions sont utiles uniquement avec le modèle car elles facilitent la détection de l’entité, ce qui est nécessaire pour que le modèle corresponde. 

## <a name="import-example-app"></a>Importer l’exemple d’application
Continuez avec l’application créée dans le dernier tutoriel, nommée **HumanResources**. 

Procédez comme suit :

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-patterns-HumanResources-v2.json).

2. Importez le code JSON dans une nouvelle application.

3. À partir de la section **Manage (Gérer)** , sous l’onglet **Versions**, clonez la version et nommez-la `roles`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL.

## <a name="create-new-entities"></a>Créer des entités

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Dans le menu de navigation de gauche, sélectionnez **Entités**. 

3. Sélectionnez **Créer une entité**.

4. Dans la fenêtre contextuelle, entrez `NewEmployee` comme entité **Simple**.

5. Sélectionnez **Créer une entité**.

6. Dans la fenêtre contextuelle, entrez `NewEmployeeRelocation` comme entité **Simple**.

7. Sélectionnez **NewEmployeeRelocation** dans la liste des entités. 

8. Entrez le premier rôle en tant que `NewEmployeeReloOrigin` et appuyez sur Entrée.

9. Entrez le second rôle en tant que `NewEmployeeReloDestination` et appuyez sur Entrée.

## <a name="create-new-intent"></a>Créer une intention
Dans ces étapes, l’étiquetage des entités peut être simplifié si l’entité keyPhrase prédéfinie est supprimée avant de commencer, puis rajoutée lorsque vous avez terminé les étapes décrites dans cette section. 

1. Sélectionnez **Intentions** dans le volet de navigation gauche.

2. Sélectionnez **Créer une intention**. 

3. Entrez `NewEmployeeRelocationProcess` en tant que nom d’intention dans la boîte de dialogue contextuelle.

4. Entrez les exemples d’énoncés suivants pour étiqueter les nouvelles entités. Les valeurs de l’entité et du rôle apparaissent en gras. N’oubliez pas de basculer vers la **vue des jetons** si vous estimez que cela facilite l’étiquetage du texte. 

    Vous ne spécifiez pas le rôle de l’entité lors de l’étiquetage dans l’intention. Vous le ferez ultérieurement lors de la création du modèle. 

    |Énoncé|NewEmployee|NewEmployeeRelocation|
    |--|--|--|
    |Transférer **Bob Jones** de **Seattle** à **Los Colinas**|Bob Jones|Seattle, Los Colinas|
    |Transférer **Dave C. Cooper** de **Redmond** à **New York**|Dave C. Cooper|Redmond, New York|
    |Transférer **Jim Paul Smith** de **Toronto** à **Vancouver ouest**|Jim Paul Smith|Toronto, West Vancouver|
    |Transférer **J. Benson** de **Boston** à **Staines-upon-Thames**|J. Benson|Boston, Staines-upon-Thames|
    |Transférer **Travis « Trav » Hinton** de **Castelo Branco** à **Orlando**|Travis « Trav » Hinton|Castelo Branco, Orlando|
    |Transférer **Trevor Nottington III** d’**Aranda de Duero** à **Boise**|Trevor Nottington III|Aranda de Duero, Boise|
    |Déplacer **Dr. Greg Williams** de **Orlando** à **Ellicott City**|Dr. Greg Williams|Orlando, Ellicott City|
    |Transférer **Robert « Bobby » Gregson** de **Kansas City** à **San Juan Capistrano**|Robert « Bobby » Gregson|Kansas City, San Juan Capistrano|
    |Transférer **Patti Owens** de **Bellevue** à **Rockford Lhotka**|Patti Owens|Bellevue, Rockford|
    |Transférer **Janet Bartlet** de **Tuscan** à **Santa Fe**|Janet Bartlet|Tuscan, Santa Fe|

    Le nom de l’employé se compose de différents préfixes, nombres de mots, syntaxes et suffixes. Ceci est important pour permettre à LUIS de comprendre les variantes du nom d’un nouvel employé. Les noms de villes se composent également de différents nombres de mots et syntaxes. Cette variété est importante pour indiquer à LUIS comment ces entités peuvent apparaître dans les énoncés d’un utilisateur. 
    
    Si une entité a le même nombre de mots et aucune autre variante, vous apprenez à LUIS que cette entité dispose uniquement de ce nombre de mots et d’aucune autre variante. LUIS est incapable de prédire correctement un ensemble plus large de variantes car aucune variante ne lui a été présentée. 

    Si vous avez supprimé l’entité keyPhrase, replacez-la maintenant dans l’application.

## <a name="train"></a>Former

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publish

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obtenir l’intention et les entités à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Move Wayne Berry from Miami to Mount Vernon`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. 

    ```json
    {
      "query": "Move Wayne Berry from Newark to Columbus",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.514479756
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.514479756
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.017118983
        },
        {
          "intent": "MoveEmployee",
          "score": 0.009982505
        },
        {
          "intent": "GetJobInformation",
          "score": 0.008637771
        },
        {
          "intent": "ApplyForJob",
          "score": 0.007115978
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.006120186
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00452428637
        },
        {
          "intent": "None",
          "score": 0.00400899537
        },
        {
          "intent": "OrgChart-Reports",
          "score": 0.00240071164
        },
        {
          "intent": "Utilities.Help",
          "score": 0.001770991
        },
        {
          "intent": "EmployeeFeedback",
          "score": 0.001697356
        },
        {
          "intent": "OrgChart-Manager",
          "score": 0.00168116146
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00163952739
        },
        {
          "intent": "FindForm",
          "score": 0.00112958835
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.629158735
        },
        {
          "entity": "newark",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 27,
          "score": 0.638941
        }
      ]
    }  
    ```

Le score de prédiction de l’intention n’est que de 50 % environ. Si votre application cliente nécessite une valeur supérieure, vous devez prendre les mesures requises. Par ailleurs, les entités n’ont pas été prédites.

L’un des emplacements a été extrait, mais pas l’autre. 

Les modèles amélioreront le score de prédiction, mais les entités doivent être correctement prédites pour que le modèle corresponde à l’énoncé. 

## <a name="pattern-with-roles"></a>Modèle avec des rôles

1. Sélectionnez **Test** dans le volet de navigation supérieur.

2. Sélectionnez **Modèles** dans le volet de navigation gauche.

3. Sélectionnez **NewEmployeeRelocationProcess** dans la liste déroulante **Sélectionner une intention**. 

4. Entrez le modèle suivant : `move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    Si vous formez, publiez et interrogez le point de terminaison, vous pouvez être déçu de constater que les entités sont introuvables, ce qui signifie que le modèle ne correspond pas et, par conséquent, que la prédiction ne s’est pas améliorée. C’est une conséquence du manque d’exemples d’énoncés avec les entités étiquetées. Au lieu d’ajouter d’autres d’exemples, ajoutez une liste d’expressions pour résoudre ce problème.

## <a name="cities-phrase-list"></a>Liste d’expressions de villes
Tout comme les noms de personnes, les villes posent parfois problème car elles peuvent contenir une combinaison de mots et de signes de ponctuation. Comme les villes de la région et le monde sont connus, LUIS a besoin d’une liste d’expressions de villes pour commencer l’apprentissage. 

1. Sélectionnez **Liste d’expressions** dans la section **Améliorer les performances de l’application** du menu de gauche. 

2. Nommez la liste `Cities` et ajoutez le code suivant `values` pour obtenir la liste :

    |Valeurs de la liste d’expressions|
    |--|
    |Seattle|
    |San Diego|
    |New York|
    |Los Angeles|
    |Portland|
    |Philadelphie|
    |Miami|
    |Dallas|

    N’ajoutez pas chaque ville dans le monde ou même chaque ville dans la région. LUIS doit être en mesure d’identifier une ville dans la liste. Veillez à sélectionner l’option **Ces valeurs sont interchangeables**. Ce paramètre signifie que les termes de la liste sont traités comme des synonymes. 

3. Former et publier l’application

## <a name="get-intent-and-entities-from-endpoint"></a>Obtenir l’intention et les entités à partir du point de terminaison

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Move wayne berry from miami to mount vernon`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. 

    ```json
    {
      "query": "Move Wayne Berry from Miami to Mount Vernon",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.9999999
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.9999999
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.49678385E-06
        },
        {
          "intent": "MoveEmployee",
          "score": 8.240291E-07
        },
        {
          "intent": "GetJobInformation",
          "score": 6.3131273E-07
        },
        {
          "intent": "None",
          "score": 4.25E-09
        },
        {
          "intent": "OrgChart-Manager",
          "score": 2.8E-09
        },
        {
          "intent": "OrgChart-Reports",
          "score": 2.8E-09
        },
        {
          "intent": "EmployeeFeedback",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.StartOver",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.Help",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Stop",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Cancel",
          "score": 1.35E-09
        },
        {
          "intent": "FindForm",
          "score": 1.23846156E-09
        },
        {
          "intent": "ApplyForJob",
          "score": 5.692308E-10
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "builtin.keyPhrase",
          "startIndex": 5,
          "endIndex": 15
        },
        {
          "entity": "miami",
          "type": "builtin.keyPhrase",
          "startIndex": 22,
          "endIndex": 26
        },
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.9410646,
          "role": ""
        },
        {
          "entity": "miami",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 26,
          "score": 0.9853915,
          "role": "NewEmployeeReloOrigin"
        },
        {
          "entity": "mount vernon",
          "type": "NewEmployeeRelocation",
          "startIndex": 31,
          "endIndex": 42,
          "score": 0.986044347,
          "role": "NewEmployeeReloDestination"
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
   }
    ```

Le score de l’intention est désormais beaucoup plus élevé, et les noms de rôles font partie de la réponse de l’entité.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel a ajouté une entité avec des rôles et une intention avec des exemples d’énoncés. La première prédiction de point de terminaison utilisant l’entité a correctement prédit l’intention, mais avec un score de confiance faible. Une seule des deux entités a été détectée. Ensuite, le tutoriel a ajouté un modèle qui a utilisé les rôles d’entité, et une liste d’expressions pour accroître la valeur des noms de ville dans les énoncés. La seconde prédiction de point de terminaison a retourné un score de confiance élevée et trouvé les deux rôles d’entité. 

> [!div class="nextstepaction"]
> [Découvrez les meilleures pratiques pour les applications LUIS](luis-concept-best-practices.md)
