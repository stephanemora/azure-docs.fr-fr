---
title: 'Tutoriel : Utiliser des modèles de rôles pour améliorer les prédictions de LUIS – Azure | Microsoft Docs'
titleSuffix: Cognitive Services
description: Dans ce tutoriel, utilisez des modèles de rôles pour les entités associées selon le contexte afin d’améliorer les prévisions de LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 08/03/2018
ms.author: diberry
ms.openlocfilehash: 633bf16790437ba83e9a2a99f33a23fef9255364
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43090524"
---
# <a name="tutorial-improve-app-with-pattern-roles"></a>Tutoriel : Améliorer l’application avec des modèles de rôles

Dans ce tutoriel, utilisez une entité simple avec des rôles combinés à des modèles pour augmenter la prédiction de l’intention et de l’entité.  Lorsque vous utilisez des modèles, l’intention nécessite moins d’exemples d’énoncés.

> [!div class="checklist"]
* Comprendre les modèles de rôles
* Utiliser une entité simple avec des rôles 
* Créer un modèle pour des énoncés à l’aide d’une entité simple avec des rôles
* Vérifier l’amélioration des prédictions de modèle.

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Avant de commencer
Si vous ne disposez pas de l’application Ressources humaines du tutoriel [modèle](luis-tutorial-pattern.md), [importez](luis-how-to-start-new-app.md#import-new-app) le JSON dans une nouvelle application du site Web [LUIS](luis-reference-regions.md#luis-website). L’application à importer se trouve dans le référentiel GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-patterns-HumanResources-v2.json).

Si vous souhaitez conserver l’application Ressources humaines d’origine, clonez la version sur la page [Paramètres](luis-how-to-manage-versions.md#clone-a-version), et nommez-la `roles`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. 

## <a name="the-purpose-of-roles"></a>Objectif des rôles
L’objectif des rôles est extraire les entités liées au contexte d’un énoncé. Dans l’énoncé, les valeurs de ville d’origine (`Move new employee Robert Williams from Sacramento and San Francisco`) et de ville de destination sont liées et utilisent un langage commun pour indiquer chaque emplacement. 

Lorsque vous utilisez des modèles, toutes les entités du modèle doivent être détectées _avant_ que le modèle corresponde à l’énoncé. 

Lorsque vous créez un modèle, la première étape consiste à sélectionner l’intention de ce modèle. En sélectionnant l’intention, si le modèle correspond, l’intention appropriée est toujours renvoyée avec un score élevé (généralement 99-100 %). 

### <a name="compare-hierarchical-entity-to-simple-entity-with-roles"></a>Comparer une entité hiérarchique et une entité simple avec des rôles

Dans le [didacticiel hiérarchique](luis-quickstart-intent-and-hier-entity.md), l’intention **MoveEmployee** vous indique quand déplacer un employé existant d’un bâtiment/bureau à un autre. Les exemples d’énoncés contenaient les emplacements d’origine et de destination, mais n’utilisaient pas les rôles. Au lieu de cela, l’origine et la destination comportaient des enfants de l’entité hiérarchique. 

Dans ce tutoriel, l’application Ressources humaines détecte les énoncés sur le déplacement des nouveaux employés d’une ville à une autre. Ces deux types d’énoncés sont similaires mais ils sont résolus avec des capacités LUIS différentes.

|Didacticiel|Exemple d’énoncé|Emplacements d’origine et de destination|
|--|--|--|
|[Hiérarchique (aucun rôle)](luis-quickstart-intent-and-hier-entity.md)|mv Jill Jones de **a-2349** à **b-1298**|a-2349, b-1298|
|Ce tutoriel (avec des rôles)|Transférer Billy Patterson de **Yuma** vers **Denver**.|Yuma, Denver|

Vous ne pouvez pas utiliser l’entité hiérarchique dans le modèle, car seuls les parents hiérarchiques sont utilisés dans les modèles. Pour retourner les emplacements nommés de l’origine et de la destination, vous devez utiliser un modèle.

### <a name="simple-entity-for-new-employee-name"></a>Entité simple pour le nom d’un nouvel employé
Le nom du nouvel employé, Billy Patterson, ne fait pas encore partie de l’entité dans la liste des **employés**. Le nom du nouvel employé est d’abord extrait pour être envoyé vers un système externe afin de créer les informations d’identification de l’entreprise. Une fois les informations d’identification de l’entreprise créées, les informations d’identification des employés sont ajoutées à l’entité dans la liste des **employés**.

La liste des **employés** a été créée dans le [tutoriel sur les listes](luis-quickstart-intent-and-list-entity.md).

L’entité **NewEmployee** est une entité simple sans aucun rôle. 

### <a name="simple-entity-with-roles-for-relocation-cities"></a>Entité simple avec des rôles pour les villes de réaffectation
Le nouvel employé et sa famille doivent être transférés de la ville actuelle vers la ville où se trouve la société fictive. Étant donné qu’un nouvel employé peut venir de n’importe quelle ville, les emplacements doivent être détectés. Une liste définie comme une entité de liste ne fonctionnerait pas car seules les villes de la liste seront extraites.

Les noms de rôles associés aux villes d’origine et de destination doivent être uniques sur toutes les entités. Un moyen simple de s’assurer que les rôles sont uniques consiste à les associer à l’entité apparentée à l’aide d’une stratégie d’affectation de noms. L’entité **NewEmployeeRelocation** est une entité simple à deux rôles : **NewEmployeeReloOrigin** et **NewEmployeeReloDestination**.

### <a name="simple-entities-need-enough-examples-to-be-detected"></a>Les entités simples nécessitent un nombre suffisant d’exemples pour être détectées
Comme l’exemple d’énoncé `Move new employee Robert Williams from Sacramento and San Francisco` contient uniquement des entités issues de l’apprentissage automatique, il est important de fournir suffisamment d’exemples d’énoncés à l’intention afin de détecter les entités.  

**Même si les modèles vous permettent de fournir moins d’exemples d’énoncés, si les entités ne sont pas détectées, le modèle ne correspond pas.**

Si vous avez des difficultés avec la détection d’une entité simple car elle représente un nom comme une ville, vous pouvez utiliser une liste d’expressions de valeurs similaires. Cela permet de détecter le nom de la ville en fournissant à LUIS des détails supplémentaires sur ce type de mot ou d’expression. Les listes d’expressions sont utiles uniquement avec le modèle car elles facilitent la détection de l’entité, ce qui est nécessaire pour que le modèle corresponde. 

## <a name="create-new-entities"></a>Créer des entités
1. Sélectionnez **Build** dans le menu supérieur.

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

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publier l’application pour obtenir l’URL de point de terminaison

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-without-pattern"></a>Interroger le point de terminaison sans modèle

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Move Wayne Berry from Miami to Mount Vernon`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. 

    ```JSON
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

Les modèles amélioreront le score de prédiction, mais les entités doivent être correctement prédites pour que le modèle corresponde à l’énoncé. 

## <a name="add-a-pattern-that-uses-roles"></a>Ajouter un modèle qui utilise des rôles
1. Sélectionnez **Test** dans le volet de navigation supérieur.

2. Sélectionnez **Modèles** dans le volet de navigation gauche.

3. Sélectionnez **NewEmployeeRelocationProcess** dans la liste déroulante **Sélectionner une intention**. 

4. Entrez le modèle suivant : `move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    Si vous formez, publiez et interrogez le point de terminaison, vous pouvez être déçu de constater que les entités sont introuvables, ce qui signifie que le modèle ne correspond pas et, par conséquent, que la prédiction ne s’est pas améliorée. C’est une conséquence du manque d’exemples d’énoncés avec les entités étiquetées. Au lieu d’ajouter d’autres d’exemples, ajoutez une liste d’expressions pour résoudre ce problème.

## <a name="create-a-phrase-list-for-cities"></a>Créer une liste d’expressions pour les villes
Tout comme les noms de personnes, les villes posent parfois problème car elles peuvent contenir une combinaison de mots et de signes de ponctuation. Mais comme les villes de la région et le monde sont connus, LUIS a besoin d’une liste d’expressions de villes pour commencer l’apprentissage. 

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

    N’ajoutez pas chaque ville dans le monde ou même chaque ville dans la région. LUIS doit être en mesure d’identifier une ville dans la liste. 

    Veillez à sélectionner l’option **Ces valeurs sont interchangeables**. Ce paramètre signifie que les termes de la liste sont traités comme des synonymes. C’est exactement la manière dont ils doivent être traités dans le modèle.

    N’oubliez pas que [la dernière fois](luis-quickstart-primary-and-secondary-data.md) la série de didacticiels a créé une liste d’expressions pour améliorer également la détection d’une entité simple.  

3. Former et publier l’application

## <a name="query-endpoint-for-pattern"></a>Rechercher un modèle pour un point de terminaison
1. Dans la page **Publier**, sélectionnez le lien **Point de terminaison** en bas de la page. Cette action ouvre une autre fenêtre de navigateur avec l’URL de point de terminaison affichée dans la barre d’adresses. 

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Move wayne berry from miami to mount vernon`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. 

    ```JSON
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

## <a name="clean-up-resources"></a>Supprimer les ressources

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez les meilleures pratiques pour les applications LUIS](luis-concept-best-practices.md)
