---
title: Ajouter des exemples d’énoncés
titleSuffix: Language Understanding - Azure Cognitive Services
description: Les exemples d’énoncés sont des exemples de texte de questions ou de commandes des utilisateurs. Pour l’apprentissage de Language Understanding (LUIS), vous devez ajouter des exemples d’énoncé à une intention.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 0d3123b1e0238a1907b5ad3d487b92a7919ff181
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60197961"
---
# <a name="add-an-entity-to-example-utterances"></a>Ajouter une entité à des exemples d’énoncés 

Les exemples d’énoncés sont des exemples de texte de questions ou de commandes des utilisateurs. Pour permettre l’apprentissage de Language Understanding (LUIS), vous devez ajouter des [exemples d’énoncé](luis-concept-utterance.md) à une [intention](luis-concept-intent.md).

En général, commencez par ajouter un exemple d’énoncé à une intention, puis créez des entités et étiquetez les énoncés dans la page d’intention. Si vous préférez commencer par créer des entités, voir [Ajouter des entités](luis-how-to-add-entities.md).

## <a name="marking-entities-in-example-utterances"></a>Marquage d’entités dans des exemples d’énoncés

Quand vous sélectionnez du texte à marquer pour une entité dans l’exemple d’énoncé, un menu contextuel s’affiche. Utilisez ce menu pour créer ou sélectionner une entité. 

Certains types d’entités, tels que les entités prédéfinies et les entités d’expression régulière, ne peuvent pas être marqués dans l’exemple d’énoncé parce qu’ils sont marqués automatiquement. 

## <a name="add-a-simple-entity"></a>Ajouter une entité simple

Dans la procédure suivante, vous allez créer et marquer une entité personnalisée dans l’énoncé suivant dans la page d’intention :

```text
Are there any SQL server jobs?
```

1. Sélectionnez `SQL server` dans l’énoncé pour l’étiqueter comme entité simple. Dans la zone de liste déroulante d’entité qui s’affiche, vous pouvez sélectionner une entité existante ou ajouter une entité. Pour ajouter une nouvelle entité, tapez son nom `Job` dans la zone de texte, puis sélectionnez **créer entité**.

    ![Capture d’écran de saisie du nom de l’entité](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > Lors de la sélection de mots à marquer en tant qu’entités :
    > * Pour un mot unique, sélectionnez-le simplement. 
    > * Pour un ensemble de deux ou plusieurs mots, sélectionnez le début, puis la fin de l’ensemble.

1. Dans la boîte de dialogue contextuelle **What type of entity do you want to create?** (Quel type d’entité voulez-vous créer ?), vérifiez le nom de l’entité, sélectionnez le type d’entité **Simple**, puis sélectionnez **Done** (Terminé).

    Une [liste d’expressions](luis-concept-feature.md) est couramment utilisée pour améliorer le signal d’une entité simple.

## <a name="add-a-list-entity"></a>Ajouter une entité de liste

Liste les entités représentent un ensemble de correspondances de texte exact des mots associés dans votre système. 

Pour la liste des services d’une entreprise, vous pouvez avoir des valeurs normalisées : `Accounting` et `Human Resources`. Chaque nom normalisé a des synonymes. Pour un service, ces synonymes peuvent inclure des acronymes ou numéros de service. Lorsque vous créez l’entité, vous n’êtes pas obligé de connaître toutes les valeurs. Vous pouvez en ajouter après avoir examiné les énoncés d’utilisateurs réels avec des synonymes.

1. Dans un énoncé exemple sur le **intentions** , sélectionnez l’ou les mots que vous souhaitez dans la nouvelle liste. Lorsque la liste déroulante entité s’affiche, entrez le nom de la nouvelle entité de liste dans la zone de texte supérieur, puis sélectionnez **créer entité**.   

1. Dans la zone contextuelle **What type of entity do you want to create?**, nommez l’entité et sélectionnez **List** comme type. Ajoutez des synonymes de cet élément de liste, puis sélectionnez **Done**. 

    ![Capture d’écran de saisie des synonymes d’entité de liste](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Vous pouvez ajouter d’autres éléments de liste ou synonymes d’éléments en étiquetant d’autres énoncés, ou en modifiant l’entité à partir d’**Entités** dans le volet de navigation gauche. La [modification](luis-how-to-add-entities.md#add-list-entities) des entités vous offre la possibilité d’entrer des éléments supplémentaires avec des synonymes correspondants, ou d’importer une liste. 

## <a name="add-composite-entity"></a>Ajouter une entité composite

Les entités composites sont créées à partir d’**entités** existantes dans une entité parente. 

En supposant que l’énoncé, `Does John Smith work in Seattle?`, un énoncé composite peut retourner des informations d’entité du nom employé `John Smith`et l’emplacement `Seattle` dans une entité composite. Les entités enfants doivent déjà exister dans l’application et être marquées dans l’énoncé exemple avant de créer l’entité composite.

1. Afin d’encapsuler les entités enfants dans une entité composite, sélectionnez le **premier** étiqueté entité (la plus à gauche) dans l’énoncé de l’entité composite. Une liste déroulante s’affiche pour indiquer les options associées à cette sélection.

1. Sélectionnez **encapsuler dans une entité composite** dans la liste déroulante. 

1. Sélectionnez le dernier mot de l’entité composite (le plus à droite). Notez qu’une ligne verte suit l’entité composite. Cela est l’indicateur visuel pour une entité composite et doit être sous tous les mots dans l’entité composite à partir de l’entité la plus à gauche enfant à l’entité enfant de la plus à droite.

1. Entrez le nom de l’entité composite dans la liste déroulante.

    Lorsque vous encapsulez les entités correctement, une ligne verte apparaît sous la phrase entière.

1. Validez les détails de l’entité composite dans la zone contextuelle **What type of entity do you want to create?**, puis sélectionnez **Done**.

    ![Capture d’écran de la zone contextuelle de détails d’entité](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. L’entité composite s’affiche, avec des mises en surbrillance bleues pour les différentes entités et un trait de soulignement vert pour l’entité composite entière. 

    ![Capture d’écran de la page de détails d’intentions, avec entité composite](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-hierarchical-entity"></a>Ajouter une entité hiérarchique

**Entités hiérarchiques risque d’être dépréciées. Utilisez [rôles de l’entité](luis-concept-roles.md) pour déterminer des sous-types d’entité, au lieu d’entités hiérarchiques.**

Une entité hiérarchique est une catégorie d’entités apprises de façon contextuelle et associées de façon conceptuelle. Dans l’exemple suivant, l’entité contient des lieux de départ et de destination. 

Dans l’énoncé `Move John Smith from Seattle to Cairo`, Seattle est le lieu de départ et Cairo (Le Caire) le lieu de destination. Chaque lieu est différent sur le plan du contexte, et appris à partir de l’ordre des mots et du choix des mots dans l’énoncé.

1. Dans la page Intention, dans l’énoncé, sélectionnez `Seattle`, entrez le nom d’entité `Location`, puis appuyez sur la touche Entrée du clavier.

1. Dans la boîte de dialogue contextuelle **What type of entity do you want to create?**, sélectionnez _hierarchical_ pour **Entity type**, ajoutez `Origin` et `Destination` comme enfants, puis sélectionnez **Done**.

    ![Capture d’écran de la page de détails Intents (Intentions) avec l’entité ToLocation en surbrillance](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

1. Le mot dans l’énoncé a été étiqueté avec l’entité hiérarchique parent. Vous devez affecter le mot à une entité enfant. Revenez à l’énoncé sur la page de détails intentionnelle. Sélectionnez le mot, puis, dans la liste déroulante, choisissez le nom d’entité que vous avez créé. Choisissez ensuite l’entité enfant correcte dans le menu à droite.

    >[!CAUTION]
    >Les noms d’entité enfant doivent être uniques parmi toutes les entités dans une même application. Deux entités hiérarchiques différentes ne peuvent pas contenir d’entités enfants portant le même nom. 

## <a name="add-entitys-role-to-utterance"></a>Ajouter le rôle de l’entité à énoncé

Un rôle est un sous-type nommé d’une entité, déterminé par le contexte de l’énoncé. Vous pouvez marquer une entité dans un énoncé en tant que l’entité, ou sélectionner un rôle au sein de cette entité. Toute entité peut avoir des rôles, y compris les entités personnalisées qui font la machine a appris (entités simples et entités composites), ne sont pas machine a appris (des entités prédéfinies, entités de l’expression régulière, les entités de liste). 

En savoir plus [comment marquer un énoncé avec des rôles de l’entité](tutorial-entity-roles.md) à partir d’un didacticiel. 

## <a name="entity-status-predictions"></a>Prédictions de l’état de l’entité

Lorsque vous entrez un nouvel énoncé dans le portail LUIS, l’énoncé peut comporter des erreurs de prédiction de l’entité. L’erreur de prédiction est la différence entre la façon dont une entité est étiquetée et la façon dont LUIS a prédit l’entité. 

Cette différence est représentée visuellement dans le portail LUIS par un trait de soulignement rouge dans l’énoncé. Le trait de soulignement rouge peut apparaître entre ou en dehors des crochets d’entité. 

![Capture d’écran montrant une incohérence de prédiction de l’état de l’entité](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Sélectionnez les mots soulignés en rouge dans l’énoncé. 

En cas incohérence de prédiction, la zone de l’entité affiche un point d’exclamation rouge en regard de l’**état de l’entité**. Pour voir les différences entre les entités étiquetées et les entités prévues, sélectionnez **État de l’entité** et sélectionnez l’élément à droite.

![Sélection d’état de capture d’écran de l’entité](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

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

> [!Note]
> Quand une zone rouge est autour de l’intention étiquetée dans la ligne de l’énoncé de l’exemple, un [erreur de prédiction intent](luis-how-to-add-intents.md#intent-prediction-discrepancy-errors) s’est produite. Vous avez besoin résoudre ce problème. 

## <a name="other-actions"></a>Autres actions

Vous pouvez effectuer des actions sur des exemples d’énoncés en tant que groupe sélectionné ou élément individuel. Les groupes d’exemples d’énoncés sélectionnés changent le menu contextuel affiché au-dessus de la liste. Les éléments uniques peuvent utiliser à la fois le menu contextuel au-dessus de la liste et les points de suspension affichés à la fin de chaque ligne d’énoncé. 

### <a name="remove-entity-labels-from-utterances"></a>Supprimer des étiquettes d’entité d’énoncés

Vous pouvez supprimer d’un énoncé des étiquettes apprises automatiquement sur la page Intents (Intentions). Si l’entité n’est pas apprise automatiquement, il n’est pas possible de la supprimer de l’énoncé. Si vous avez besoin de supprimer de l’énoncé une entité non apprise automatiquement, vous devez supprimer l’entité de l’application entière. 

Pour supprimer une étiquette d’entité apprise automatiquement d’un énoncé, sélectionnez l’entité dans celui-ci. Sélectionnez ensuite **Remove Label** (Supprimer l’étiquette) dans la zone de liste déroulante de l’entité qui s’affiche.

### <a name="add-prebuilt-entity-label"></a>Ajouter une étiquette d’entité prédéfinie

Quand vous ajoutez les entités prédéfinies à votre application LUIS, vous n’avez pas besoin de marquer les énoncés avec ces entités. Pour plus d’informations sur les entités prédéfinies et la manière de les ajouter, voir [Ajouter des entités](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

### <a name="add-regular-expression-entity-label"></a>Ajouter une étiquette d’entité regular expression

Si vous ajoutez les entités d’expression régulière à votre application LUIS, vous n’avez pas besoin de marquer les énoncés avec ces entités. Pour plus d’informations sur les entités d’expression régulière et la manière de les ajouter, voir [Ajouter des entités](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts).


### <a name="create-a-pattern-from-an-utterance"></a>Créer un modèle à partir d’un énoncé

Voir [Ajouter un modèle à partir d’un énoncé existant sur une page d’intention ou d’entité](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-patternany-entity"></a>Ajouter une entité pattern.any

Si vous ajoutez les entités pattern.any à votre application LUIS, vous ne pouvez pas étiqueter les énoncés avec ces entités. Elles sont valides uniquement dans des modèles. Pour plus d’informations sur les entités pattern.any et la manière de les ajouter, voir [Ajouter des entités](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Former votre application après modification du modèle avec des énoncés

Après avoir ajouté, modifié ou supprimé des énoncés, [formez](luis-how-to-train.md) et [publiez](luis-how-to-publish-app.md) votre application pour que vos modifications affectent les requêtes de point de terminaison. 

## <a name="next-steps"></a>Étapes suivantes

Après étiquetage des énoncés dans vos intentions, vous pouvez créer une [entité composite](luis-how-to-add-entities.md).
