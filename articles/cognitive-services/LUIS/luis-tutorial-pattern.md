---
title: 'Tutoriel : Modèles - LUIS'
titleSuffix: Azure Cognitive Services
description: Utilisez des modèles pour accroître la prédiction d’intentions et d’entités tout en fournissant moins d’exemples d’énoncés dans ce tutoriel. Le modèle est fourni sous forme d’exemple d’énoncé de modèle, qui comprend la syntaxe pour identifier les entités et le texte pouvant être ignoré.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 69894dfc6bcbe9eb56451524c78e82da2745aa52
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979762"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Tutoriel : Ajouter des formats d’énoncé de modèle courants pour améliorer les prédictions

Dans ce tutoriel, vous allez utiliser des modèles pour accroître la prédiction d’intentions et d’entités, ce qui vous permet de fournir moins d’exemples d’énoncés. Le modèle est un énoncé de modèle affecté à une intention, contenant la syntaxe pour identifier les entités et le texte pouvant être ignoré.

**Dans ce tutoriel, vous allez découvrir comment :**

> [!div class="checklist"]
> * Créer un modèle
> * Vérifier l’amélioration des prédictions de modèle
> * Marquer le texte comme pouvant être ignoré et imbriquer dans le modèle
> * Utiliser le panneau de test pour vérifier la réussite du modèle

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>Énoncés dans l’intention et le modèle

Les énoncés stockés dans l’application LUIS peuvent être de deux types :

* Exemples d’énoncés dans l’intention
* Énoncés de modèle dans le modèle

Le fait d’ajouter des énoncés de modèle en tant que modèle vous permet globalement de fournir moins d’exemples d’énoncés à une intention.

Un modèle appliqué est une correspondance d’expression combinée au machine learning.  L’énoncé de modèle, conjugué aux exemples d’énoncés, permet à LUIS de mieux identifier les énoncés qui correspondent à l’intention.

## <a name="import-example-app-and-clone-to-new-version"></a>Importer un exemple d’application et un clone dans une nouvelle version

Utiliser les étapes suivantes :

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json?raw=true).

1. Importez le fichier JSON dans une nouvelle application sur la [préversion du portail LUIS](https://preview.luis.ai).

1. À partir de la section **Manage (Gérer)** , sous l’onglet **Versions**, clonez la version et nommez-la `patterns`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL.

## <a name="create-new-intents-and-their-utterances"></a>Créer de nouvelles intentions et leurs énoncés

1. Dans la barre de navigation, sélectionnez **Build** (Générer).

1. Dans la page **Intents** (Intentions), sélectionnez **+ Create** (Créer) pour créer une intention.

1. Entrez `OrgChart-Manager` dans la boîte de dialogue contextuelle, puis sélectionnez **Terminé**.

    ![Créer une fenêtre contextuelle de message](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. Ajoutez des exemples d’énoncés à l’intention. Ces énoncés ne sont pas _exactement_ identiques, mais ils ont un modèle qui peut être extrait.

    |Exemples d’énoncés|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

    Ne vous inquiétez pas si l’entité keyPhrase est étiquetée dans les énoncés de l’intention au lieu de l’entité Employee. Les deux sont correctement prédites dans le volet de test et au point de terminaison.

1. Sélectionnez **Intentions** dans le volet de navigation gauche.

1. Sélectionner **+ Create** (Créer) pour créer une intention. Entrez `OrgChart-Reports` dans la boîte de dialogue contextuelle, puis sélectionnez **Terminé**.

1. Ajoutez des exemples d’énoncés à l’intention.

    |Exemples d’énoncés|
    |--|
    |`Who are John W. Smith's subordinates?`|
    |`Who reports to John W. Smith?`|
    |`Who does John W. Smith manage?`|
    |`Who are Jill Jones direct reports?`|
    |`Who does Jill Jones supervise?`|

### <a name="caution-about-example-utterance-quantity"></a>Attention à la quantité des exemples d’énoncés

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

### <a name="train-the-app-before-testing-or-publishing"></a>Entraîner l’application avant un test ou la publication

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

### <a name="publish-the-app-to-query-from-the-endpoint"></a>Publier l’application pour faire des requêtes à partir du point de terminaison

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

### <a name="get-intent-and-entities-from-endpoint"></a>Obtenir l’intention et les entités à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Who is the boss of Jill Jones?`. Le dernier paramètre de chaîne de requête est l’énoncé `query`.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.313054234
                },
                "OrgChart-Reports": {
                    "score": 0.2462688
                },
                "EmployeeFeedback": {
                    "score": 0.0488328524
                },
                "GetJobInformation": {
                    "score": 0.0156933
                },
                "MoveEmployee": {
                    "score": 0.011265873
                },
                "Utilities.StartOver": {
                    "score": 0.003065792
                },
                "Utilities.Stop": {
                    "score": 0.00300148362
                },
                "Utilities.Cancel": {
                    "score": 0.00271081156
                },
                "None": {
                    "score": 0.00212835032
                },
                "ApplyForJob": {
                    "score": 0.0020669254
                },
                "Utilities.Confirm": {
                    "score": 0.00200891262
                },
                "FindForm": {
                    "score": 0.00194145238
                },
                "Utilities.Help": {
                    "score": 0.00182301877
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "Employee": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "Employee": [
                        {
                            "type": "Employee",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

Cette requête n’a réussi ? Pour ce cycle de formation, elle a réussi. Le score des deux premières intentions sont proches, mais l’intention la plus haute n’est pas très élevée (plus de 60 %) et est trop proche du score de l’intention suivante.

Étant donné que la formation de LUIS n’est pas exactement la même chaque fois, il y a une petite variation. Ces deux scores pourraient s’inverser lors du prochain cycle de formation. Il en résulte que l’intention erronée pourrait être retournée.

Utiliser des modèles pour augmenter significativement le score de l’intention en pourcentage et l’éloigner du score le plus élevé suivant.

Laissez cette deuxième fenêtre de navigation s’ouvrir. Vous utiliserez cette valeur plus loin dans le didacticiel.

## <a name="template-utterances"></a>Modèles d’énoncés
En raison de la nature du domaine des ressources humaines, il existe plusieurs moyens courants de poser des questions sur les relations des employés dans les organisations. Par exemple :

|Énoncés|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Ces énoncés sont trop proches pour permettre de déterminer l’unicité contextuelle de chacun sans fournir trop d’exemples d’énoncé. Si l’on ajoute un modèle pour une intention, LUIS apprend les modèles d’énoncés courants de cette intention avec peu d’exemples d’énoncés.

Quelques exemples de modèles d’énoncés pour cette intention :

|Exemples de modèles d’énoncés|signification de la syntaxe|
|--|--|
|`Who does {Employee} report to[?]`|interchangeable `{Employee}`<br>ignorer `[?]`|
|`Who reports to {Employee}[?]`|interchangeable `{Employee}`<br>ignorer `[?]`|

La syntaxe `{Employee}` marque le type et l’emplacement de l’entité dans l’énoncé de modèle. La syntaxe facultative, `[?]`, marque les mots ou la ponctuation facultative. LUIS établit une correspondance avec l’énoncé et ignore le texte facultatif à l’intérieur des crochets.

Bien que la syntaxe ressemble à une expression régulière, ce n’en est pas une. Seule la syntaxe d’accolade, `{}`, et de crochet, `[]`, est prise en charge. Ils peuvent être imbriqués jusqu’à deux niveaux.

Pour qu’un modèle corresponde à un énoncé, les entités au sein de l’énoncé doivent d’abord correspondre aux entités du modèle d’énoncé. Cela signifie que les entités doivent avoir suffisamment d’exemples dans les exemples d’énoncés avec un fort degré de prédiction avant que les modèles avec entités réussissent. Cependant, le modèle contribue uniquement à la prédicition d’intentions, mais pas d’entités.

**Même si les modèles vous permettent de fournir moins d’exemples d’énoncés, si les entités ne sont pas détectées, le modèle ne correspond pas.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Ajouter les modèles pour l’intention OrgChart-Manager

1. Sélectionnez **Build** dans le menu supérieur.

1. Dans le volet de navigation gauche, sous **Améliorer les performances de l’application**, sélectionnez **Modèles**.

1. Sélectionnez l’intention **OrgChart-Manager**, puis entrez les modèles d’énoncés suivants :

    |Modèles d’énoncés|
    |:--|
    |`Who is {Employee} the subordinate of[?]`|
    |`Who does {Employee} report to[?]`|
    |`Who is {Employee}['s] manager[?]`|
    |`Who does {Employee} directly report to[?]`|
    |`Who is {Employee}['s] supervisor[?]`|
    |`Who is the boss of {Employee}[?]`|

1. Toujours sur la page Modèles, sélectionnez l’intention **OrgChart-Reports**, puis entrez les modèles d’énoncés suivants :

    |Modèles d’énoncés|
    |:--|
    |`Who are {Employee}['s] subordinates[?]`|
    |`Who reports to {Employee}[?]`|
    |`Who does {Employee} manage[?]`|
    |`Who are {Employee} direct reports[?]`|
    |`Who does {Employee} supervise[?]`|
    |`Who does {Employee} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Point de terminaison de requête lorsque des modèles sont utilisés

Maintenant que les modèles sont ajoutés à l’application, formez, publiez et interrogez l’application sur le point de terminaison du runtime de prédiction.

1. Sélectionnez **Train** (Entraîner). Une fois l’entraînement terminé, sélectionnez **Publish** (Publier), sélectionnez l’emplacement **Production**, puis **Done** (Terminé).

1. Après avoir effectué la publication, rebasculez vers l’onglet du navigateur où figure l’URL du point de terminaison.

1. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Who is the boss of Jill Jones?` en tant qu’énoncé. Le dernier paramètre de chaîne de requête est `query`.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999997854
                },
                "OrgChart-Reports": {
                    "score": 6.13748343E-05
                },
                "EmployeeFeedback": {
                    "score": 8.052567E-06
                },
                "GetJobInformation": {
                    "score": 1.18197136E-06
                },
                "MoveEmployee": {
                    "score": 7.65549657E-07
                },
                "None": {
                    "score": 3.975E-09
                },
                "Utilities.StartOver": {
                    "score": 1.53E-09
                },
                "Utilities.Confirm": {
                    "score": 1.38181822E-09
                },
                "Utilities.Help": {
                    "score": 1.38181822E-09
                },
                "Utilities.Stop": {
                    "score": 1.38181822E-09
                },
                "Utilities.Cancel": {
                    "score": 1.25833333E-09
                },
                "FindForm": {
                    "score": 1.15384613E-09
                },
                "ApplyForJob": {
                    "score": 5.26923061E-10
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "Employee": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "Employee": [
                        {
                            "type": "Employee",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

La prédiction d’intention est désormais beaucoup plus fiable et le score de la prochaine intention la plus haute est bien inférieur. Ces deux intentions ne seront pas inversées pendant l’entraînement.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Utilisation d’un texte facultatif et d’entités prédéfinies

Les énoncés de modèle précédents dans ce didacticiel possédaient quelques exemples de texte facultatifs tels que l’utilisation possessive de la lettre s, `'s`, et l’utilisation du point d’interrogation, `?`. Supposons que vous devez tenir compte des dates actuelles et futures dans l’énoncé.

Les exemples d’énoncés sont :

|Intentionnel|Exemples d’énoncés avec un texte facultatif et des entités prédéfinies|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Chacun de ces exemples utilise une conjugaison du verbe, `was`, `is`, `will be`, ainsi qu’une date, `March 3`, `now`, et `in a month`, que requiert LUIS pour effectuer correctement ses prédictions. Notez que les deux derniers exemples du tableau utilisent presque le même texte à l’exception de `in` et `on`.

Exemples d’énoncés de modèle pour ces informations facultatives :

|Intentionnel|Exemples d’énoncés avec un texte facultatif et des entités prédéfinies|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


L’utilisation de la syntaxe facultative des crochets, `[]`, facilite l’ajout d’un énoncé de modèle à ce texte facultatif, ce dernier peut se voir imbriqué à un deuxième niveau, `[[]]`, et peut inclure des entités ou du texte.


**Question : Pourquoi toutes les lettres `w`, la première lettre de chaque énoncé de modèle, sont en minuscules ? Le fait qu’elles soient en majuscules ou en minuscules n’est-il pas facultatif ?** L’énoncé soumis au point de terminaison de requête par l’application cliente, est converti en minuscules. L’énoncé de modèle peut être en majuscules ou minuscules, tout comme l’énoncé du point de terminaison. La comparaison est toujours effectuée après la conversion en minuscules.

**Question : Pourquoi le nombre prédéfini ne fait pas partie de l’énoncé de modèle alors que le 3 mars est prédit à la fois comme un nombre `3` et une date `March 3` ?** L’énoncé de modèle utilise une date en fonction du contexte, soit littéralement comme dans `March 3`, ou de façon abstraite comme dans `in a month`. Une date peut contenir un nombre mais un nombre ne peut pas nécessairement être considéré comme une date. Utilisez toujours l’entité qui représente au mieux le type à retourner dans les résultats JSON de prédiction.

**Question : Qu’en est-il des énoncés incompréhensibles comme `Who will {Employee}['s] manager be on March 3?`.** Les conjugaisons grammaticalement différentes, comme dans le cas où le `will` et le `be` sont séparés, doivent constituer un nouvel énoncé de modèle. L’énoncé de modèle existant ne correspond pas. Bien que l’intention de l’énoncé n’ait pas changé, le placement du mot dans l’énoncé a changé. Cette modification affecte la prédiction dans LUIS. Vous pouvez [regrouper et/ou](#use-the-or-operator-and-groups) les temps des verbes pour combiner ces énoncés.

**N’oubliez pas : les entités sont les premières trouvées, le modèle étant ensuite mis en correspondance.**

### <a name="edit-the-existing-pattern-template-utterance"></a>Modifier l’énoncé de modèle existant

1. Sur la préversion du portail LUIS, sélectionnez **Build** (Générer) dans le menu supérieur, puis **Patterns** (Modèles) dans le menu de gauche.

1. Recherchez l’énoncé de modèle existant, `Who is {Employee}['s] manager[?]`, puis sélectionnez les points de suspension (***...***) à droite, puis sélectionnez **Modifier** dans le menu contextuel.

1. Modifiez l’énoncé de modèle en : `who is {Employee}['s] manager [[on]{datetimeV2}?]`

### <a name="add-new-pattern-template-utterances"></a>Ajouter de nouveaux énoncés de modèle

1. Alors que vous êtes toujours dans la section **Modèles** de**Générer**, ajoutez plusieurs nouveaux énoncés de modèle. Sélectionnez **OrgChart-Manager** dans le menu déroulant d’intentions et entrez chacun des énoncés de modèle suivants :

    |Intentionnel|Exemples d’énoncés avec un texte facultatif et des entités prédéfinies|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Sélectionnez **Train** (Entraîner) dans la barre de navigation pour entraîner l’application.

3. À la fin de l’entraînement, sélectionnez **Test** (Tester) en haut du panneau pour ouvrir le panneau de test.

4. Entrez plusieurs énoncés de test pour vérifier que le modèle est mis en correspondance et que le score de l’intention est très élevé.

    Après avoir entré le premier énoncé, sélectionnez **Inspecter** sous le résultat, afin que vous puissiez voir tous les résultats de prédiction. Chaque énoncé doit avoir l’intention **OrgChart-Manager** et doit extraire les valeurs pour les entités d’Employee et datetimeV2.

    |Énoncé|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

Tous ces énoncés ont trouvé les entités à l’intérieur, par conséquent, ils correspondent au même modèle et ont un score de prédiction élevé. Vous avez ajouté plusieurs modèles qui correspondront à de nombreuses variantes d’énoncés. Vous n’avez pas eu besoin d’ajouter des exemples d’énoncés dans l’intention pour faire fonctionner l’énoncé de modèle dans le modèle.

Cette utilisation de modèles a fourni :
* des scores de prédiction plus élevés
* avec les mêmes exemples d’énoncés dans l’intention
* avec seulement quelques énoncés de modèle bien construits dans le modèle

### <a name="use-the-or-operator-and-groups"></a>Utiliser l’opérateur OR et les groupes

Plusieurs des énoncés de modèle précédents sont très proches. Utilisez la syntaxe **group** `()` et **OR** `|` pour réduire les énoncés de modèle.

Les 2 modèles suivants peuvent être combinés dans un modèle unique à l’aide de la syntaxe group `()` et OR `|`.

|Intentionnel|Exemples d’énoncés avec un texte facultatif et des entités prédéfinies|
|--|--|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Le nouvel énoncé de modèle sera :

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`.

Cet exemple utilise un **groupe** autour de la conjugaison du verbe requis `in` et `on` facultatifs avec un canal **ou** entre les deux.

1. Sur la page **Modèles**, sélectionnez le filtre **OrgChart-Manager**. Limitez la liste en recherchant `manager`.

1. Conservez une version de l’énoncé de modèle (à modifier dans l’étape suivante) et supprimez les autres variations.

1. Modifiez l’énoncé de modèle en :

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`

2. Sélectionnez **Train** (Entraîner) dans la barre de navigation pour entraîner l’application.

3. À la fin de l’entraînement, sélectionnez **Test** (Tester) en haut du panneau pour ouvrir le panneau de test.

    Utilisez le volet de test pour tester les versions de l’énoncé :

    |Énoncés à entrer dans le volet de test|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

En utilisant une syntaxe de modèle plus riche, vous pourriez réduire le nombre d’énoncés de modèle à maintenir dans votre application, tout en ayant toujours un score de prédiction élevé.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>Utilisez les ancres de début et de fin de l’énoncé

La syntaxe du modèle fournit la syntaxe d’ancre de début et de fin d’énoncé, à savoir le signe insertion, `^`. Les ancres de début et de fin d’énoncé peuvent être utilisées ensemble pour cibler des énoncé très spécifiques et éventuellement littéraux ou utilisées séparément pour cibler des intentions.

## <a name="using-patternany-entity"></a>Utilisation de l’entité pattern.any

L’entité pattern.any vous permet de rechercher des données en forme libre où le libellé de l’entité ne permet pas de distinguer la fin de l’entité du reste de l’énoncé.

Cette application Ressources humaines permet aux employés de trouver les formulaires de l’entreprise.

|Énoncé|
|--|
|Où se trouve **HRF-123456** ?|
|Qui a créé **HRF-123234** ?|
|**HRF-456098** est-il publié en français ?|

Toutefois, chaque formulaire comporte à la fois un nom mis en forme utilisé dans le tableau précédent, ainsi qu’un nom convivial, tel que `Request relocation from employee new to the company 2018 version 5`.

Les énoncés avec le nom du formulaire convivial ressemblent à ceci :

|Énoncé|
|--|
|Où se trouve le formulaire **Request relocation from employee new to the company 2018 version 5** (demande de réaffectation d'un nouvel employé) ?|
|Qui a écrit le formulaire **Request relocation from employee new to the company 2018 version 5** (demande de réaffectation d'un nouvel employé - 2018 version 5) ?|
|Le formulaire **Request relocation from employee new to the company 2018 version 5** (demande de réaffectation d'un nouvel employé - 2018 version 5) ? est-t disponible en français ?|

Certains mots peuvent être source de confusion pour LUIS, qui ne sait pas où se termine l’entité. Dans un modèle, l’entité Pattern.any permet de spécifier le début et la fin du nom du formulaire afin que LUIS puisse extraire correctement son nom.

|Exemple de modèle d’énoncé|
|--|
|Où se trouve {FormName}[?]|
|Qui a créé {FormName}[?]|
|{nom_formulaire} est-il publié en Français[?]|

### <a name="add-example-utterances-with-patternany"></a>Ajouter des exemples d’énoncés avec Pattern.any

1. Sélectionnez **Build** dans le volet de navigation supérieur, puis **Intentions** dans le volet de navigation gauche.

1. Sélectionnez **FindForm** dans la liste des intentions.

1. Ajoutez quelques exemples d’énoncés :

    |Exemple d’énoncé|Nom du formulaire|
    |--|--|
    |Où se trouve le formulaire **What to do when a fire breaks out in the Lab** (Que faire en cas d’incendie dans le labo) ? Et qui doit le signer une fois que je l’ai lu ?|What to do when a fire breaks out in the Lab
    |Où se trouve le formulaire **Request relocation from employee new to the company** (demande de réaffectation d'un nouvel employé) sur le serveur ?|Request relocation from employee new to the company|
    |Qui a écrit le formulaire **Health and wellness requests on the main campus** (demandes concernant la santé et le bien-être sur le campus principal) et quelle est la version la plus récente ?|Health and wellness requests on the main campus|
    |Je recherche le formulaire intitulé **Office move request including physical assets** (demande de déménagement de bureau, y compris les biens matériels). |Office move request including physical assets|

    Sans entité Pattern.any, il serait difficile pour LUIS de comprendre où se termine le titre du formulaire en raison des nombreuses variations des noms de formulaire.

### <a name="create-a-patternany-entity"></a>Créer une entité Pattern.any
L’entité Pattern.any extrait des entités de longueur variable. Elle fonctionne uniquement dans un modèle, car le modèle marque le début et la fin de l’entité avec la syntaxe.

1. Sélectionnez **Entités** dans le volet de navigation gauche.

1. Sélectionnez **+ Create** (Créer), entrez le nom `FormName`, puis sélectionnez le type **Pattern.any**. Sélectionnez **Create** (Créer).

### <a name="add-a-pattern-that-uses-the-patternany"></a>Ajouter un modèle qui utilise l’entité Pattern.any

1. Sélectionnez **Modèles** dans le volet de navigation gauche.

1. Sélectionnez l’intention **FindForm**.

1. Entrez les modèles d’énoncés suivants, qui utilisent la nouvelle entité :

    |Modèles d’énoncés|
    |--|
    |`Where is the form ["]{FormName}["] and who needs to sign it after I read it[?]`|
    |`Where is ["]{FormName}["] on the server[?]`|
    |`Who authored ["]{FormName}["] and what is the most current version[?]`|
    |`I'm looking for the form named ["]{FormName}["][.]`|

1. Effectuez l’apprentissage de l’application.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Tester l’extraction de données à structure libre dans le nouveau modèle
1. Sélectionnez **Tester** dans la barre supérieure pour ouvrir le panneau de test.

1. Entrez l’énoncé suivant :

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. Sélectionnez **Inspecter** sous le résultat afin de voir les résultats des tests pour l’entité et l’intention.

    L’entité `FormName` est trouvée en premier, suivie du modèle, indiquant l’intention. Si, dans votre résultat de test, les entités ne sont pas détectées et, par conséquent, le modèle est introuvable, vous devrez ajouter des exemples d’énoncés sur l’intention (et non le modèle).

1. Fermez le panneau de test en sélectionnant le bouton **Tester** dans le volet de navigation supérieur.

### <a name="using-an-explicit-list"></a>Avec une liste explicite

Si vous constatez que votre modèle, quand il comporte une entité Pattern.any, extrait mal les entités, utilisez une [liste explicite](reference-pattern-syntax.md#explicit-lists) pour corriger ce problème.

## <a name="what-did-this-tutorial-accomplish"></a>Récapitulatif de ce tutoriel

Dans ce didacticiel, vous avez ajouté des modèles pour permettre à LUIS de prédire l’intention avec un score beaucoup plus élevé sans avoir à ajouter davantage d’exemples d’énoncés. Le marquage des entités et du texte pouvant être ignoré a permis à LUIS d’appliquer le modèle à une plus grande variété d’énoncés.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes


> [!div class="nextstepaction"]
> [En savoir plus sur l’utilisation de rôles avec un modèle](luis-tutorial-pattern.md)
