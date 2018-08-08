---
title: 'Tutoriel : Utiliser des modèles pour améliorer les prédictions de LUIS – Azure | Microsoft Docs'
titleSuffix: Cognitive Services
description: Ce tutoriel utilise les modèles d’intentions afin d’améliorer les prédictions de LUIS en matière d’intentions et d’entités.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/30/2018
ms.author: diberry
ms.openlocfilehash: 355c1edd4fa7433e68a9c0e903f4f782203326fe
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365876"
---
# <a name="tutorial-improve-app-with-patterns"></a>Tutoriel : Améliorer l’application avec des modèles

Ce tutoriel utilise les modèles pour améliorer les prédictions en matière d’intentions et d’entités.  

> [!div class="checklist"]
* Identifier quand un modèle serait utile à votre application.
* Créer un modèle.
* Vérifier l’amélioration des prédictions de modèle.

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Avant de commencer

Si vous ne disposez pas de l’application Ressources humaines du tutoriel [test par lots](luis-tutorial-batch-testing.md), [importez](luis-how-to-start-new-app.md#import-new-app) le JSON dans une nouvelle application du site Web [LUIS](luis-reference-regions.md#luis-website). L’application à importer se trouve dans le référentiel GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-batchtest-HumanResources.json).

Si vous souhaitez conserver l’application Ressources humaines d’origine, clonez la version sur la page [Paramètres](luis-how-to-manage-versions.md#clone-a-version), et nommez-la `patterns`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Apprendre à LUIS les énoncés courants avec moins d’exemples grâce aux modèles

En raison de la nature du domaine des ressources humaines, il existe plusieurs moyens courants de poser des questions sur les relations des employés dans les organisations. Par exemple : 

|Énoncés|
|--|
|À qui Jill Jones rend-il compte ?|
|Qui rend compte à Jill Jones ?|

Ces énoncés sont trop proches pour permettre de déterminer l’unicité contextuelle de chacun sans fournir trop d’exemples d’énoncé. Si l’on ajoute un modèle pour une intention, LUIS apprend les modèles d’énoncés courants de cette intention avec peu d’exemples d’énoncés. 

Quelques exemples d’énoncés de modèle pour cette intention :

|Exemples d’énoncés de modèle|
|--|
|À qui {Employee} rend-il compte ?|
|Qui rend compte à {Employee} ?|

Le modèle est fourni par le biais d’un exemple d’énoncé de modèle, qui inclut la syntaxe pour identifier les entités et le texte pouvant être ignoré. Un modèle est une combinaison de correspondance par expression régulière et de Machine Learning.  Les exemples de modèles d’énoncés, ainsi que les énoncés de l’intention, permettent à LUIS de mieux comprendre quels énoncés correspondent à l’intention.

Pour qu’un modèle corresponde à un énoncé, les entités au sein de l’énoncé doivent d’abord correspondre aux entités du modèle d’énoncé. Cependant, le modèle contribue uniquement à la prédicition d’intentions, mais pas d’entités. 

**Même si les modèles vous permettent de fournir moins d’exemples d’énoncés, si les entités ne sont pas détectées, le modèle ne correspond pas.**

Souvenez-vous : les employés ont été créés dans le [tutoriel d’entité de liste](luis-quickstart-intent-and-list-entity.md).

## <a name="create-new-intents-and-their-utterances"></a>Créer de nouvelles intentions et leurs énoncés

Ajouter deux nouvelles intentions de nouveau : `OrgChart-Manager` et `OrgChart-Reports`. Une fois que LUIS retourne une prédiction à l’application cliente, le nom de l’intention peut être utilisé comme nom de fonction dans l’application cliente, et l’entité Employee peut être utilisée comme paramètre de cette fonction.

```Javascript
OrgChart-Manager(employee){
    ///
}
```

1. Assurez-vous que votre application Ressources humaines figure dans la section **Générer** de LUIS. Vous pouvez modifier cette section en sélectionnant **Générer** dans la barre de menu en haut à droite. 

2. Dans la page **Intents** (Intentions), sélectionnez **Create new intent** (Créer une intention). 

3. Entrez `OrgChart-Manager` dans la boîte de dialogue contextuelle, puis sélectionnez **Terminé**.

    ![Créer une fenêtre contextuelle de message](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. Ajoutez des exemples d’énoncés à l’intention.

    |Exemples d’énoncés|
    |--|
    |De qui John W. Smith est-il le subordonné ?|
    |À qui John W. Smith rend-il compte ?|
    |Qui est le responsable de John W. Smith ?|
    |À qui Jill Jones rend-il compte directement ?|
    |Qui est le superviseur de Jill Jones ?|

    [![Capture d’écran de LUIS ajoutant de nouveaux énoncés à l’intention](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "Capture d’écran de LUIS ajoutant de nouveaux énoncés à l’intention")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    Ne vous inquiétez pas si l’entité keyPhrase est étiquetée dans les énoncés de l’intention au lieu de l’entité Employee. Les deux sont correctement prédites dans le volet de test et au point de terminaison. 

5. Sélectionnez **Intentions** dans le volet de navigation gauche.

6. Sélectionnez **Créer une intention**. 

7. Entrez `OrgChart-Reports` dans la boîte de dialogue contextuelle, puis sélectionnez **Terminé**.

8. Ajoutez des exemples d’énoncés à l’intention.

    |Exemples d’énoncés|
    |--|
    |Qui sont les subordonnés de John W. Smith ?|
    |Qui rend compte à John W. Smith ?|
    |De qui John W. Smith est-il responsable ?|
    |Qui sont les collaborateurs directs de Jill Jones ?|
    |De qui Jill Jones est-il le superviseur ?|

## <a name="caution-about-example-utterance-quantity"></a>Attention à la quantité des exemples d’énoncés

La quantité des exemples d’énoncés dans ces intentions ne suffit pas pour un apprentissage correct de LUIS. Dans une application réelle, chaque intention doit avoir un minimum de 15 énoncés, avec un large éventail de mots au choix et de longueurs d’énoncés. Ces quelques énoncés sont spécialement sélectionnés pour mettre en évidence des modèles. 

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publier l’application pour obtenir l’URL de point de terminaison

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Interroger le point de terminaison avec un autre énoncé

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Who is the boss of Jill Jones?`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.353984952
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.353984952
            },
            {
                "intent": "OrgChart-Reports",
                "score": 0.214128986
            },
            {
                "intent": "EmployeeFeedback",
                "score": 0.08434003
            },
            {
                "intent": "MoveEmployee",
                "score": 0.019131
            },
            {
                "intent": "GetJobInformation",
                "score": 0.004819009
            },
            {
                "intent": "Utilities.Confirm",
                "score": 0.0043958663
            },
            {
                "intent": "Utilities.StartOver",
                "score": 0.00312064588
            },
            {
                "intent": "Utilities.Cancel",
                "score": 0.002265454
            },
            {
                "intent": "Utilities.Help",
                "score": 0.00133465114
            },
            {
                "intent": "None",
                "score": 0.0011388344
            },
            {
                "intent": "Utilities.Stop",
                "score": 0.00111166481
            },
            {
                "intent": "FindForm",
                "score": 0.0008900076
            },
            {
                "intent": "ApplyForJob",
                "score": 0.0007836131
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                }
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Cette requête n’a réussi ? Pour ce cycle de formation, elle a réussi. Les scores des deux intentions principales sont proches. Étant donné que la formation de LUIS n’est pas exactement la même chaque fois, il y a une petite variation. Ces deux scores pourraient s’inverser lors du prochain cycle de formation. Il en résulte que l’intention erronée pourrait être retournée. 

Utiliser des modèles pour augmenter significativement le score de l’intention en pourcentage et l’éloigner du score le plus élevé suivant. 

Laissez cette deuxième fenêtre de navigation s’ouvrir. Vous utiliserez cette valeur plus loin dans le didacticiel. 

## <a name="add-the-template-utterances"></a>Ajouter les énoncés de modèle

1. Sélectionnez **Build** dans le menu supérieur.

2. Dans le volet de navigation gauche, sous **Améliorer les performances de l’application**, sélectionnez **Modèles**.

3. Sélectionnez l’intention **OrgChart-Manager**, puis entrez les énoncés de modèle suivants, un par un, en sélectionnant Entrée après chacun :

    |Modèles d’énoncés|
    |:--|
    |De qui {Employee} est-il le subordonné[?]|
    |À qui {Employee} rend-il compte[?]|
    |Qui est le responsable de {Employee}[?]|
    |À qui {Employee} rend-il directement compte[?]|
    |Qui est le superviseur de {Employee}[?]|
    |Qui est le patron de {Employee}[?]|

    La syntaxe `{Employee}` marque le type et l’emplacement de l’entité dans l’énoncé de modèle. 

    Les entités avec des rôles utilisent une syntaxe comprenant le nom de rôle, et sont traitées dans un [tutoriel séparé pour les rôles](luis-tutorial-pattern-roles.md). 

    La syntaxe facultative, `[]`, marque les mots ou la ponctuation facultatifs. LUIS établit une correspondance avec l’énoncé et ignore le texte facultatif à l’intérieur des crochets.

    Si vous tapez le modèle d’énoncé, LUIS vous aide à remplir l’entité lorsque vous entrez l’accolade gauche, `{`.

    [![Capture d’écran de la saisie d’énoncés de modèle pour une intention](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. Sélectionnez l’intention **OrgChart-Reports**, puis entrez les modèles d’énoncés suivants, un par un, en actionnant Entrée après chacun d’eux :

    |Modèles d’énoncés|
    |:--|
    |Qui sont les subordonnés de {Employee}[?]|
    |Qui rend compte à {Employee}[?]|
    |De qui {Employee} est-il responsable[?]|
    |Qui sont les collaborateurs directs de{Employee}[?]|
    |Qui supervise {Employee}[?]|
    |Qui est le chef de {Employee}[?]|

## <a name="query-endpoint-when-patterns-are-used"></a>Point de terminaison de requête lorsque des modèles sont utilisés

1. Former et publier à nouveau l’application.

2. Rebasculez les onglets de navigateur vers l’onglet URL de point de terminaison.

3. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Who is the boss of Jill Jones?` en tant qu’énoncé. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.9999989
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.9999989
            },
            {
                "intent": "OrgChart-Reports",
                "score": 7.616303E-05
            },
            {
                "intent": "EmployeeFeedback",
                "score": 7.84204349E-06
            },
            {
                "intent": "GetJobInformation",
                "score": 1.20674213E-06
            },
            {
                "intent": "MoveEmployee",
                "score": 7.91245157E-07
            },
            {
                "intent": "None",
                "score": 3.875E-09
            },
            {
                "intent": "Utilities.StartOver",
                "score": 1.49E-09
            },
            {
                "intent": "Utilities.Confirm",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Help",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Stop",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Cancel",
                "score": 1.225E-09
            },
            {
                "intent": "FindForm",
                "score": 1.123077E-09
            },
            {
                "intent": "ApplyForJob",
                "score": 5.625E-10
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                },
                "role": ""
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

La prédiction de l’intention la prédiction est maintenant beaucoup plus élevée.

## <a name="working-with-optional-text-and-prebuilt-entities"></a>Utilisation d’un texte facultatif et d’entités prédéfinies

Les énoncés de modèle précédents dans ce didacticiel possédaient quelques exemples de texte facultatifs tels que l’utilisation possessive de la lettre s, `'s`, et l’utilisation du point d’interrogation, `?`. Supposons que les points de terminaison des énoncés montrent que les gestionnaires et les représentants des ressources humaines recherchent des données historiques et planifient des mutations d’employés au sein de l’entreprise à une date ultérieure.

Les exemples d’énoncés sont :

|Intention|Exemples d’énoncés avec un texte facultatif et des entités prédéfinies|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Chacun de ces exemples utilise une conjugaison du verbe, `was`, `is`, `will be`, ainsi qu’une date, `March 3`, `now`, et `in a month`, que requiert LUIS pour effectuer correctement ses prédictions. Notez que les deux derniers exemples utilisent presque le même texte à l’exception de `in` et `on`.

Exemples des énoncés de modèle :
|Intention|Exemples d’énoncés avec un texte facultatif et des entités prédéfinies|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

L’utilisation de la syntaxe facultative des crochets, `[]`, facilite l’ajout d’un énoncé de modèle à ce texte facultatif, ce dernier peut se voir imbriqué à un deuxième niveau, `[[]]`, et peut inclure des entités ou du texte.

**Question : Pourquoi les deux derniers exemples d’énoncés n’ont-ils pas pu fusionner en un seul énoncé de modèle ?** Le modèle ne prend pas en charge la syntaxe OR. Pour entrer à la fois la version `in` et la version `on`, chaque énoncé doit être un énoncé de modèle séparé.

**Question : Pourquoi toutes les lettres `w`, la première lettre de chaque énoncé de modèle, sont en minuscules ? Le fait qu’elles soient en majuscules ou en minuscules n’est-il pas facultatif ?** L’énoncé soumis au point de terminaison de requête par l’application cliente, est converti en minuscules. L’énoncé de modèle peut être en majuscules ou minuscules, tout comme l’énoncé du point de terminaison. La comparaison est toujours effectuée après la conversion en minuscules.

**Question : Pourquoi le nombre prédéfini ne fait pas partie de l’énoncé de modèle alors que le 3 mars est prédit à la fois comme un nombre `3` et une date `March 3`?** L’énoncé de modèle utilise une date en fonction du contexte, soit littéralement comme dans `March 3`, ou de façon abstraite comme dans `in a month`. Une date peut contenir un nombre mais un nombre ne peut pas nécessairement être considéré comme une date. Utilisez toujours l’entité qui représente au mieux le type à retourner dans les résultats JSON de prédiction.  

**Question : Qu’en est-il des énoncés incompréhensibles, comme avec `Who will {Employee}['s] manager be on March 3?`.** Les conjugaisons grammaticalement différentes, comme dans le cas où le `will` et le `be` sont séparés, doivent constituer un nouvel énoncé de modèle. L’énoncé de modèle existant ne correspond pas. Bien que l’intention de l’énoncé n’ait pas changé, le placement du mot dans l’énoncé a changé. Cette modification affecte la prédiction dans LUIS.

**N’oubliez pas : les entités sont les premières trouvées, le modèle étant ensuite mis en correspondance.**

## <a name="edit-the-existing-pattern-template-utterance"></a>Modifier l’énoncé de modèle existant

1. Sur le site web de LUIS, sélectionnez **Générer** dans le menu supérieur, puis sélectionnez **Modèles** dans le menu de gauche. 

2. Recherchez l’énoncé de modèle existant, `Who is {Employee}['s] manager[?]`, puis sélectionnez les points de suspension (***...***) à droite. 

3. Sélectionnez **Modifier** dans le menu contextuel. 

4. Modifiez l’énoncé de modèle en : `who is {Employee}['s] manager [[on]{datetimeV2}?]]`

## <a name="add-new-pattern-template-utterances"></a>Ajouter de nouveaux énoncés de modèle

1. Alors que vous êtes toujours dans la section **Modèles** de**Générer**, ajoutez plusieurs nouveaux énoncés de modèle. Sélectionnez **OrgChart-Manager** dans le menu déroulant d’intentions et entrez chacun des énoncés de modèle suivants :

    |Intention|Exemples d’énoncés avec un texte facultatif et des entités prédéfinies|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Effectuez l’apprentissage de l’application.

3. Sélectionnez **Test** en haut du panneau pour ouvrir le panneau de test. 

4. Entrez plusieurs énoncés de test pour vérifier que le modèle est mis en correspondance et que le score de l’intention est très élevé. 

    Après avoir entré le premier énoncé, sélectionnez **Inspecter** sous le résultat, afin que vous puissiez voir tous les résultats de prédiction.

    |Énoncé|
    |--|
    |Qui sera le responsable de Jill Jones|
    |qui sera le responsable de Jill Jones|
    |Qui sera le responsable de Jill Jones ?|
    |qui sera le responsable de Jill Jones le 3 mars|
    |Qui sera le responsable de Jill Jones le mois prochain|
    |Qui sera le responsable de Jill Jones dans un mois ?|

Tous ces énoncés ont trouvé les entités à l’intérieur, par conséquent, ils correspondent au même modèle et ont un score de prédiction élevé.

## <a name="clean-up-resources"></a>Supprimer les ressources

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’utilisation de rôles avec un modèle](luis-tutorial-pattern-roles.md)