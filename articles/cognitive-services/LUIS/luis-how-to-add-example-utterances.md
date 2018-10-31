---
title: Ajouter des exemples d’énoncés dans des applications LUIS
titleSuffix: Azure Cognitive Services
description: Découvrez comment ajouter des énoncés dans des applications Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/10/2018
ms.author: diberry
ms.openlocfilehash: 6a3edfd426fcdce83bd60332ba2b1ff6224dae1a
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645557"
---
# <a name="add-example-utterances-and-label-with-entities"></a>Ajouter des exemples d’énoncés et les étiqueter avec des entités

Les exemples d’énoncés sont des exemples de texte de questions ou de commandes des utilisateurs. Pour permettre l’apprentissage de Language Understanding (LUIS), vous devez ajouter des [exemples d’énoncé](luis-concept-utterance.md) à une [intention](luis-concept-intent.md).

En général, commencez par ajouter un exemple d’énoncé à une intention, puis créez des entités et étiquetez les énoncés dans la page d’intention. Si vous préférez commencer par créer des entités, voir [Ajouter des entités](luis-how-to-add-entities.md).

## <a name="add-an-utterance"></a>Ajouter un énoncé
Dans une page d’intention, entrez un exemple d’énoncé pertinent que vous attendez de vos utilisateurs, tel que `book 2 adult business tickets to Paris tomorrow on Air France`, dans la zone de texte sous le nom d’intention, puis appuyez sur Entrée. 
 
>[!NOTE]
>LUIS convertit tous les énoncés en minuscules.

![Capture d’écran de la page de détails Intents (Intentions), avec l’énoncé en surbrillance](./media/luis-how-to-add-example-utterances/add-new-utterance-to-intent.png) 

Les énoncés sont ajoutés à la liste d’énoncés pour l’intention en cours. 

## <a name="ignoring-words-and-punctuation"></a>Ignorer les mots et les signes de ponctuation
Si vous souhaitez ignorer des mots ou des signes de ponctuation spécifiques dans l’exemple d’énoncé, utilisez un [modèle](luis-concept-patterns.md#pattern-syntax) avec la syntaxe _ignore_. 

## <a name="add-simple-entity-label"></a>Ajouter une étiquette d’entité simple
Dans la procédure suivante, vous allez créer et étiqueter des entités personnalisées dans l’énoncé suivant sur la page d’intention :

```
book me 2 adult business tickets to Paris tomorrow on Air France
```

1. Sélectionnez « Air France » dans l’énoncé pour l’étiqueter comme une entité simple.

    > [!NOTE]
    > Lorsque vous sélectionnez des mots pour les étiqueter en tant qu’entités :
    > * Pour un mot unique, sélectionnez-le simplement. 
    > * Pour un ensemble de deux ou plusieurs mots, sélectionnez le début, puis la fin de l’ensemble.

2. Dans la zone de liste déroulante d’entité qui s’affiche, vous pouvez sélectionner une entité existante ou ajouter une entité. Pour ajouter une nouvelle entité, tapez son nom dans la zone de texte, puis sélectionnez **Créer une entité**. 
 
    ![Capture d’écran de la page de détails Intents (Intentions), avec l’option d’étiquetage d’entité simple en surbrillance](./media/luis-how-to-add-example-utterances/create-airline-simple-entity.png)

3. Dans la boîte de dialogue contextuelle **What type of entity do you want to create?** (Quel type d’entité voulez-vous créer ?), vérifiez le nom de l’entité, sélectionnez le type d’entité simple, puis sélectionnez **Done** (Terminé).

    ![Image de boîte de dialogue de confirmation](./media/luis-how-to-add-example-utterances/create-simple-airline-entity.png)

    Pour en savoir plus sur l’extraction de l’entité simple à partir de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#simple-entity-data). Pour en savoir plus sur l’utilisation d’une entité simple, essayez le [démarrage rapide](luis-quickstart-primary-and-secondary-data.md) d’entité simple.


## <a name="add-list-entity-and-label"></a>Ajouter une entité list et l’étiqueter
Les entités de liste représentent un ensemble fixe, fermé (correspondances de texte exactes) de mots associés dans votre système. 

Pour une entité list drinks (boissons), vous pouvez avoir deux valeurs normalisées : water (eau) et soda pop (boisson gazeuse). Chaque nom normalisé a des synonymes. Pour water (eau), les synonymes sont H2O, gas, flat (gazeuse, plate). Pour soda pop (boisson gazeuse), les synonymes sont fruit, cola et ginger. Lorsque vous créez l’entité, vous n’êtes pas obligé de connaître toutes les valeurs. Vous pouvez en ajouter après avoir examiné les énoncés d’utilisateurs réels avec des synonymes.

|Nom normalisé|Synonymes|
|--|--|
|Water (Eau)|H2O, gas, flat (gazeuse, plate)|
|Soda pop (boisson gazeuse)|Fruit, cola, ginger|

Lorsque vous créez une entité list à partir de la page d’intention, vous effectuez deux opérations qui peuvent ne pas être évidentes. Premièrement, vous créez une liste en ajoutant le premier élément de liste. Deuxièmement, le premier élément de liste est nommé avec le mot ou la phrase que vous avez sélectionnés dans l’énoncé. Si vous pouvez les modifier ultérieurement à partir de la page d’entité, il peut être plus rapide de sélectionner un énoncé contenant le mot que vous souhaitez comme nom pour l’élément de liste.

Par exemple, si vous souhaitez créer une liste de types de boissons et sélectionnez le mot `h2o` dans l’énoncé pour créer l’entité, la liste ne comprend qu’un seul élément nommé H2O. Si vous souhaitez un nom plus générique, vous devez choisir un énoncé qui utilise le nom plus générique. 

1. Dans l’énoncé, sélectionnez le mot qui est le premier élément de la liste, entrez le nom de la liste dans la zone de texte, puis sélectionnez **Créer une entité**.   

    ![Capture d’écran de la page de détails Intents (Intentions) avec Create new entity (Créer une entité) en surbrillance](./media/luis-how-to-add-example-utterances/create-drink-list-entity.png)

2. Dans la boîte de dialogue **What type of entity do you want to create?** (Quel type d’entité voulez-vous créer ?), ajoutez des synonymes de cet élément de liste. Pour l’élément water (eau) dans une liste de boissons, ajoutez `h20`, `perrier`, et `waters`, puis sélectionnez **Done** (Terminé). Notez que « waters » (eaux) est ajouté parce que les synonymes de la liste sont mis en correspondance au niveau du jeton. Dans la culture anglaise, ce niveau est au niveau du mot. Ainsi le mot « waters » (eaux) n’est mis en correspondance avec « water » (eau) que s’il figure dans la liste. 

    ![Capture d’écran de la boîte de dialogue What type of entity do you want to create? (Quel type d’entité voulez-vous créer ?)](./media/luis-how-to-add-example-utterances/drink-list-ddl.png)

    Cette liste de boissons ne contient qu’un seul type de boisson, water (eau). Vous pouvez ajouter d’autres types de boissons en étiquetant d’autres énoncés, ou en modifiant l’entité à partir de **Entities** (Entités) dans le volet de navigation de gauche. La [modification](luis-how-to-add-entities.md#add-list-entities) des entités vous offre la possibilité d’entrer des éléments supplémentaires avec des synonymes correspondants, ou d’[importer](luis-how-to-add-entities.md#import-list-entity-values) une liste. 

    Pour en savoir plus sur l’extraction d’entités de liste de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#list-entity-data). Pour en savoir plus sur l’utilisation d’une entité list, essayez le [démarrage rapide](luis-quickstart-intent-and-list-entity.md).

## <a name="add-synonyms-to-the-list-entity"></a>Ajouter des synonymes à l’entité list 
Ajoutez un synonyme à l’entité list en sélectionnant le mot ou la phrase dans l’énoncé. Si vous avez une entité list Drink (Boisson) et souhaitez ajouter `agua` comme synonyme pour water (eau), procédez comme suit :

Dans l’énoncé, sélectionnez le mot synonyme, par exemple `aqua` pour water (eau). Sélectionnez le nom d’entité list dans la liste déroulante, par exemple **Drink** (Boisson). Sélectionnez **Set as synonym** (Définir en tant que synonyme). Enfin, sélectionnez l’élément de liste dont le mot est synonyme, tel que **water** (eau).

![Capture d’écran de la page de détails Intents (Intentions) avec Create a new normalized view (Créer une vue personnalisée) en surbrillance](./media/luis-how-to-add-example-utterances/set-agua-as-synonym.png)

## <a name="create-new-item-for-list-entity"></a>Créer un élément pour une entité list
Créez un élément pour une entité list existante en sélectionnant le mot ou la phrase dans l’énoncé. Si vous avez une liste d’employés et souhaitez ajouter `Bob Smith` comme nouvel élément, procédez comme suit :

Dans l’énoncé, sélectionnez le mot ou la phrase pour le nouvel élément de liste, par exemple `Bob Smith`, sélectionnez le nom d’entité list dans la liste déroulante, tel que **Employee** (Employé), puis sélectionnez **Create a new normalized view** (Créer une vue personnalisée). 

![Capture d’écran de l’ajout d’un nouvel élément de liste](./media/luis-how-to-add-example-utterances/list-entity-create-new-item.png)

Le mot est maintenant surligné en bleu. Si vous pointez sur le mot, un indicateur s’affiche, montrant le nom de l’élément de liste, par exemple tea (thé).

![Capture d’écran de l’indicateur de nouvel élément de liste](./media/luis-how-to-add-example-utterances/list-entity-item-name-tag.png)

## <a name="wrap-entities-in-composite-label"></a>Encapsuler des entités dans une étiquette composite
Les entités composites sont créées à partir de **Entities** (entités). Vous ne pouvez pas créer une entité composite à partir de la page Intents (Intentions). Une fois l’entité composite créée, vous pouvez encapsuler les entités dans un énoncé sur la page Intents (Intentions). 

Si l’énoncé est `book 2 tickets from Seattle to Cairo`, un énoncé composite peut renvoyer des informations d’entité sur le nombre de tickets (2), ainsi que les lieux de départ (Seattle) et de destination (Caire) dans une entité parent unique. 

Suivez les [étapes](luis-how-to-add-entities.md#add-prebuilt-entity) suivantes pour ajouter entité prédéfinie **number** (nombre). Une fois l’entité créée, le chiffre `2` dans l’énoncé apparaît en bleu, indiquant qu’il s’agit d’une entité étiquetée. Les entités prédéfinies sont étiquetées par LUIS. Vous ne pouvez pas ajouter ou supprimer l’étiquette d’entité prédéfinie à partir d’un énoncé unique. Vous pouvez ajouter ou supprimer toutes les étiquettes prédéfinies uniquement en ajoutant ou supprimant l’entité prédéfinie à partir de l’application.

Suivez les [étapes](#add-hierarchical-entity-and-label) suivantes pour créer une entité hiérarchique **Location** (Lieu). Étiquetez les lieux de départ et de destination dans l’exemple d’énoncé. 

Avant d’encapsuler les entités dans une entité composite, assurez-vous que toutes les entités enfants sont surlignées en bleu, ce qui indique qu’elles ont été étiquetées dans l’énoncé.

1. Pour encapsuler les entités individuelles dans une entité composite, sélectionnez la première entité étiquetée dans l’énoncé pour l’entité composite. Dans l’exemple d’énoncé, `book 2 tickets from Seattle to Cairo`, la première entité, est le chiffre 2. Une liste déroulante affiche les choix pour cette sélection.

    ![Capture d’écran du chiffre sélectionné et des options de liste déroulante en surbrillance](./media/luis-how-to-add-example-utterances/wrap-1.png)

2. Sélectionnez **Wrap composite entity** (Encapsuler l’entité composite) dans la liste déroulante. 

    ![Capture d’écran des options de liste déroulante pour l’encapsulage d’entité composite avec Wrap composite entity (Encapsuler l’entité composite) en surbrillance](./media/luis-how-to-add-example-utterances/wrap-2.png)

3. Sélectionnez le dernier mot de l’entité composite. Dans l’énoncé de cet exemple, sélectionnez « Location::Destination » (représentant Cairo -Le Caire-). La ligne verte apparaît maintenant sous tous les mots de l’énoncé, y compris des mots ne constituant pas des entités, qui constituent l’entité composite.

    ![Capture d’écran de la page Intents (Intentions) BookFlight avec number (numéro) en surbrillance](./media/luis-how-to-add-example-utterances/wrap-composite.png)

4. Sélectionnez le nom de l’entité composite dans la liste déroulante. Pour cet exemple, il s’agit de **TicketOrder**.

    ![Capture d’écran d’encapsulage de mots avec une entité composite, avec le nom de l’entité composite en surbrillance dans la liste déroulante](./media/luis-how-to-add-example-utterances/wrap-4.png)

    Lorsque vous encapsulez les entités correctement, une ligne verte apparaît sous la phrase entière.

    ![Capture d’écran d’énoncé avec entité composite en surbrillance](./media/luis-how-to-add-example-utterances/wrap-5.png)

    Pour en savoir plus sur l’extraction de l’entité composite à partir de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#composite-entity-data). Pour en savoir plus sur l’utilisation d’une entité composite, essayez le [didacticiel](luis-tutorial-composite-entity.md) sur les entités composites.

## <a name="add-hierarchical-entity-and-label"></a>Ajouter entité hiérarchique et une étiquette
Une entité hiérarchique est une catégorie d’entités apprises de façon contextuelle et associées de façon conceptuelle. Dans l’exemple suivant, l’entité contient des lieux de départ et de destination. 

Dans l’énoncé `Book 2 tickets from Seattle to Cairo`, Seattle est le lieu de départ et Cairo (Le Caire) le lieu de destination. Chaque lieu est différent sur le plan du contexte, et appris à partir de l’ordre des mots et du choix des mots dans l’énoncé.

1. Dans la page Intents (Intentions), dans l’énoncé, sélectionnez « Seattle », entrez le nom d’entité Location (Lieu), puis sélectionnez **Create new entity** (Créer une entité).

    ![Capture d’écran de la boîte de dialogue Create Hierarchical Entity Labeling (Créer un étiquetage d’entité hiérarchique)](./media/luis-how-to-add-example-utterances/create-hier-ent-1.png)

2. Dans la boîte de dialogue contextuelle, sélectionnez hierarchical (hiérarchique) pour **Entity type** (Type d’entité), ajoutez `Origin` et `Destination` comme enfants, puis sélectionnez **Done** (Terminé).

    ![Capture d’écran de la page de détails Intents (Intentions) avec l’entité ToLocation en surbrillance](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

3. Le mot dans l’énoncé a été étiqueté avec l’entité hiérarchique parent. Vous devez affecter le mot à une entité enfant. Revenez à l’énoncé sur la page des intentions. Sélectionnez le mot, puis, dans la liste déroulante, choisissez le nom d’entité que vous avez créé. Choisissez ensuite l’entité enfant correcte dans le menu à droite.

    ![Capture d’écran de la page de détails Intents (Intentions) avec l’entité ToLocation en surbrillance](./media/luis-how-to-add-example-utterances/label-tolocation.png)

    >[!CAUTION]
    >Les noms d’entité enfant doivent être uniques parmi toutes les entités dans une même application. Deux entités hiérarchiques différentes ne peuvent pas contenir d’entités enfants portant le même nom. 

    Pour en savoir plus sur l’extraction de l’entité hiérarchique à partir de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#hierarchical-entity-data). Pour en savoir plus sur l’utilisation d’une entité hiérarchique, essayez le [démarrage rapide](luis-quickstart-intent-and-hier-entity.md) d’entité hiérarchique.

## <a name="entity-status-predictions"></a>Prédictions de l’état de l’entité

Lorsque vous entrez un nouvel énoncé dans le portail LUIS, l’énoncé peut comporter des erreurs de prédiction de l’entité. L’erreur de prédiction est la différence entre la façon dont une entité est étiquetée et la façon dont LUIS a prédit l’entité. 

Cette différence est représentée visuellement dans le portail LUIS par un trait de soulignement rouge dans l’énoncé. Le trait de soulignement rouge peut apparaître entre ou en dehors des crochets d’entité. 

![Capture d’écran montrant une incohérence de prédiction de l’état de l’entité](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Sélectionnez les mots soulignés en rouge dans l’énoncé. 

En cas incohérence de prédiction, la zone de l’entité affiche un point d’exclamation rouge en regard de l’**état de l’entité**. Pour voir les différences entre les entités étiquetées et les entités prévues, sélectionnez **État de l’entité** et sélectionnez l’élément à droite.

![Capture d’écran montrant une incohérence de prédiction de l’état de l’entité](./media/luis-how-to-add-example-utterances/entity-status.png)

La ligne rouge peut apparaître dans les situations suivantes :

    * Entre la saisie de l’énoncé et l’étiquetage de l’entité
    * Lorsque l’étiquette de l’entité est appliquée
    * Lorsque l’étiquette de l’entité est supprimée
    * Lorsque plusieurs étiquettes d’entité sont prédites pour ce texte 

Les solutions suivantes permettent de résoudre les écarts de prédiction d’entité :

|Entité|Indicateur visuel|Prédiction|Solution|
|--|--|--|--|
|Énoncé saisi, entité pas encore étiquetée.|trait de soulignement rouge|La prédiction est correcte.|Étiquetez l’entité avec la valeur prédite.|
|Texte sans étiquette|trait de soulignement rouge|Prédiction incorrecte|Les énoncés actuels utilisant cette entité incorrecte doivent être intégralement passés en revue. Les énoncés actuels ont faussement indiqué à LUIS que ce texte est l’entité prévue.
|Texte correctement étiqueté|mise en surbrillance de l’entité en bleu, trait de soulignement rouge|Prédiction incorrecte|Fournit davantage d’énoncés avec la bonne étiquette d’entité dans une grande diversité d’emplacements et d’utilisations. Les énoncés actuels ne permettent pas à LUIS de savoir s’il s’agit de l’entité ou si des entités similaires apparaissent dans le même contexte. L’entité similaire doit être combinée en une seule entité pour ne pas induire LUIS en erreur. Une autre solution consiste à ajouter une liste d’expressions pour améliorer la précision des mots. |
|Texte mal étiqueté|mise en surbrillance de l’entité en bleu, trait de soulignement rouge|Prédiction correcte| Fournit davantage d’énoncés avec la bonne étiquette d’entité dans une grande diversité d’emplacements et d’utilisations. 


## <a name="remove-entity-labels-from-utterances"></a>Supprimer des étiquettes d’entité d’énoncés
Vous pouvez supprimer d’un énoncé des étiquettes apprises automatiquement sur la page Intents (Intentions). Si l’entité n’est pas apprise automatiquement, il n’est pas possible de la supprimer de l’énoncé. Si vous avez besoin de supprimer de l’énoncé une entité non apprise automatiquement, vous devez supprimer l’entité de l’application entière. 

Pour supprimer une étiquette d’entité apprise automatiquement d’un énoncé, sélectionnez l’entité dans celui-ci. Sélectionnez ensuite **Remove Label** (Supprimer l’étiquette) dans la zone de liste déroulante de l’entité qui s’affiche.

![Capture d’écran de la page de détails Intents (Intentions), avec Remove Label (Supprimer l’étiquette) en surbrillance](./media/luis-how-to-add-example-utterances/remove-label.png) 

## <a name="add-prebuilt-entity-label"></a>Ajouter une étiquette d’entité prédéfinie
Si vous ajoutez les entités prédéfinies à votre application LUIS, vous n’avez pas besoin d’étiqueter les énoncés avec ces entités. Pour plus d’informations sur les entités prédéfinies et la manière de les ajouter, voir [Ajouter des entités](luis-how-to-add-entities.md#add-prebuilt-entity).

## <a name="add-regular-expression-entity-label"></a>Ajouter une étiquette d’entité regular expression
Si vous ajoutez les entités d’expression régulière à votre application LUIS, vous n’avez pas besoin d’étiqueter les énoncés avec ces entités. Pour plus d’informations sur les entités d’expression régulière et la manière de les ajouter, voir [Ajouter des entités](luis-how-to-add-entities.md#add-regular-expression-entities).

## <a name="create-a-pattern-from-an-utterance"></a>Créer un modèle à partir d’un énoncé
Voir [Ajouter un modèle à partir d’un énoncé existant sur une page d’intention ou d’entité](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="add-patternany-entity-label"></a>Ajouter une étiquette d’entité pattern.any
Si vous ajoutez les entités pattern.any à votre application LUIS, vous ne pouvez pas étiqueter les énoncés avec ces entités. Elles sont valides uniquement dans des modèles. Pour plus d’informations sur les entités pattern.any et la manière de les ajouter, voir [Ajouter des entités](luis-how-to-add-entities.md#add-patternany-entities).

<!--
Fix this - moved to luis-how-to-add-intents.md - how ?

## Search in utterances
## Prediction discrepancy errors
## Filter by intent prediction discrepancy errors
## Filter by entity type
## Switch to token view
## Delete utterances
## Edit an utterance
## Reassign utterances

-->
## <a name="train-your-app-after-changing-model-with-utterances"></a>Former votre application après modification du modèle avec des énoncés
Après avoir ajouté, modifié ou supprimé des énoncés, [formez](luis-how-to-train.md) et [publiez](luis-how-to-publish-app.md) votre application pour que vos modifications affectent les requêtes de point de terminaison. 

## <a name="next-steps"></a>Étapes suivantes

Après étiquetage des énoncés dans vos intentions, vous pouvez créer une [entité composite](luis-how-to-add-entities.md).
