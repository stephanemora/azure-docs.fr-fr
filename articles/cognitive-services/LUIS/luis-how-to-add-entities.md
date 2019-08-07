---
title: Ajouter des entités - LUIS
titleSuffix: Azure Cognitive Services
description: Créez des entités pour extraire les données clés des énoncés de l'utilisateur dans les applications LUIS (Language Understanding).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 1b42ab9155f5b9719ef6477934722021e3fbac99
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638240"
---
# <a name="create-entities-without-utterances"></a>Créer des entités sans énoncés

L’entité représente un mot ou une phrase dans l’énoncé que vous souhaitez extraire. Une entité représente une classe, notamment une collection d’objets similaires (lieux, choses, personnes, événements ou concepts). Les entités décrivent des informations relatives à l’intention, et sont parfois essentiels pour que votre application effectue sa tâche. Vous pouvez créer des entités lorsque vous ajoutez un énoncé à une intention (avant ou après).

La **liste des entités** de la page **Entities** permet d’ajouter, de modifier et de supprimer des entités dans une application LUIS. LUIS propose deux grands types d’entités : les [entités prédéfinies](luis-reference-prebuilt-entities.md) et les [entités personnalisées](luis-concept-entity-types.md#types-of-entities).

Lorsqu'une entité issue de l’apprentissage automatique est créée, vous devez marquer cette entité dans tous les exemples d’énoncé de toutes les intentions dans lesquelles elle est exprimée.

<a name="add-prebuilt-entity"></a>

## <a name="add-a-prebuilt-entity-to-your-app"></a>Ajouter une entité prédéfinie à votre application

Celles qui sont le plus souvent ajoutées aux applications sont *number* et *datetimeV2*. 

1. Dans votre application, dans la section **Build**, sélectionnez **Entities** dans le volet gauche.
 
1. Dans la page **Entities**, sélectionnez **Add prebuilt entity** (Ajouter une entité prédéfinie).

1. Dans la boîte de dialogue **Add prebuilt entities** (Ajouter des entités prédéfinies), sélectionnez les entités prédéfinies **number** et **datetimeV2**. Ensuite, sélectionnez **Terminé**.

    ![Capture d’écran de la boîte de dialogue Ajouter des entités prédéfinies](./media/add-entities/list-of-prebuilt-entities.png)

<a name="add-simple-entities"></a>

## <a name="add-simple-entities-for-single-concepts"></a>Ajouter des entités simples pour des concepts uniques

Une entité simple décrit un concept unique. Appliquez la procédure suivante pour créer une entité qui extrait des noms de services d’entreprise tels que *Human resources* ou *Operations*.   

1. Dans votre application, sélectionnez la section **Build**, sélectionnez **Entities** dans le volet gauche, puis **Create new entity** (Créer une entité).

1. Dans la boîte de dialogue contextuelle, tapez `Location` dans la zone **Entity name** (Nom de l’entité), sélectionnez **Simple** dans la liste **Entity type** (Type d’entité), puis **Done**.

    Une fois cette entité créée, accédez à toutes les intentions qui ont des exemples d’énoncés contenant l’entité. Sélectionnez le texte dans l’exemple d’énoncé et marquez-le en tant qu’entité. 

    Une [liste d’expressions](luis-concept-feature.md) est couramment utilisée pour améliorer le signal d’une entité simple.

<a name="add-regular-expression-entities"></a>

## <a name="add-regular-expression-entities-for-highly-structured-concepts"></a>Ajouter des entités d’expression régulière pour des concepts hautement structurés

Une entité d’expression régulière sert à extraire des données de l’énoncé suivant l’expression régulière fournie. 

1. Dans votre application, sélectionnez **Entités** dans le volet de navigation gauche, puis **Créer une entité**.

1. Dans la boîte de dialogue contextuelle, entrez `Human resources form name` dans la zone **Nom de l’entité**, sélectionnez **Expression régulière** dans la liste **Type d’entité**, entrez l’expression régulière `hrf-[0-9]{6}`, puis sélectionnez **Terminé**. 

    Cette expression régulière correspond aux caractères littéraux `hrf-`, suivis de 6 chiffres pour représenter un numéro de formulaire pour un formulaire de ressources humaines.

<a name="add-composite-entities"></a>

## <a name="add-composite-entities-to-group-into-a-parent-child-relationship"></a>Ajouter des entités composites à regrouper dans une relation parent-enfant

Vous pouvez définir des relations entre des entités de différents types en créant une entité composite. Dans l’exemple suivant, l’entité contient une expression régulière et une entité prédéfinie correspondant au nom.  

Dans l’énoncé `Send hrf-123456 to John Smith`, le texte `hrf-123456` est mis en correspondance avec une [expression régulière](#add-regular-expression-entities) de ressources humaines et `John Smith` est extrait avec l’entité prédéfinie personName. Chaque entité fait partie d’une entité parente plus grande. 

1. Dans votre application, sélectionnez **Entities** dans la navigation gauche de la section **Build**, puis sélectionnez **Add prebuilt entity** (Ajouter une entité prédéfinie).

1. Ajoutez l’entité prédéfinie **PersonName**. Pour connaître les instructions à suivre, voir [Ajouter des entités prédéfinies](#add-prebuilt-entity). 

1. Sélectionnez **Entités** dans le volet de navigation gauche, puis **Créer une entité**.

1. Dans la boîte de dialogue contextuelle, entrez `SendHrForm` dans la zone **Entity name**, puis sélectionnez **Composite** dans la liste **Entity type**.

1. Sélectionnez **Ajouter un enfant** pour ajouter un enfant.

1. Dans **Enfant 1**, sélectionnez l’entité **number** dans la liste.

1. Dans **Child #2**, sélectionnez l’entité **Human resources form name** (Nom du formulaire de ressources humaines) dans la liste. 

1. Sélectionnez **Terminé**.

<a name="add-pattern-any-entities"></a>

## <a name="add-patternany-entities-to-capture-free-form-entities"></a>Ajouter des entités Pattern.any pour capturer des entités à structure libre

Les entités [Pattern.Any](luis-concept-entity-types.md) sont valides uniquement dans les [modèles](luis-how-to-model-intent-pattern.md), et non dans les intentions. Ce type d’entité aide LUIS à trouver la fin des entités présentant une longueur variable et différents choix de mots. Cette entité étant utilisée dans un modèle, LUIS sait où se trouve la fin de l’entité dans le modèle d’énoncé.

Si une application a une intention `FindHumanResourcesForm`, le titre du formulaire extrait risque d’interférer avec la prédiction de l’intention. Utilisez une entité Pattern.any au sein d’un modèle pour préciser quels mots font partie du titre du formulaire. La prédiction LUIS commence par l’énoncé, qui fait l’objet d’une vérification et d’une mise en correspondance avec les entités. Une fois les entités identifiées, le modèle est examiné et mis en correspondance. 

Dans l’énoncé `Where is Request relocation from employee new to the company on the server?`, le titre du formulaire est délicat car le contexte ne permet pas de déterminer à coup sûr où se termine le titre et où commence le reste de l’énoncé. Les mots peuvent être dans n’importe quel ordre dans les titres : un seul mot, des expressions complexes avec des signes de ponctuation ou des ordres de mots dénués de sens. Un modèle permet de créer une entité permettant d’extraire avec exactitude la totalité de l’entité. Une fois le titre trouvé, l’intention `FindHumanResourcesForm` est prédite car elle correspond au modèle.

1. Dans la section **Build**, sélectionnez **Entities** dans le volet gauche, puis **Create new entity**.

1. Dans la boîte de dialogue **Add Entity**, tapez `HumanResourcesFormTitle` dans la zone **Entity name** et sélectionnez **Pattern.any** comme **Entity type**.

    Pour utiliser l’entité Pattern.any, ajoutez un modèle dans la page **Patterns**, dans la section **Improve app performance** (Améliorer les performances de l’application) avec la syntaxe entre accolades correcte, par exemple `Where is **{HumanResourcesFormTitle}** on the server?`.

    Si vous constatez que votre modèle, quand il comporte une entité Pattern.any, extrait mal les entités, utilisez une [liste explicite](luis-concept-patterns.md#explicit-lists) pour corriger ce problème. 

<a name="add-a-role-to-pattern-based-entity"></a>

## <a name="add-a-role-to-distinguish-different-contexts"></a>Ajouter un rôle pour distinguer les différents contextes

Un rôle est un sous-type nommé en fonction du contexte. Il est disponible dans toutes les entités, y compris les entités prédéfinies et non issues de l’apprentissage automatique. 

La syntaxe d’un rôle est **`{Entityname:Rolename}`** , le nom de l’entité étant suivi de deux-points, puis du nom du rôle. Par exemple : `Move {personName} from {Location:Origin} to {Location:Destination}`.

1. Dans la section **Build**, sélectionnez **Entities** dans le volet gauche.

1. Sélectionnez **Créer une entité**. Entrez le nom de `Location`. Sélectionnez le type **Simple**, puis **Done**. 

1. Sélectionnez **Entités** dans le volet gauche, puis la nouvelle entité **Emplacement** créée à l’étape précédente.

1. Dans la zone de texte **Nom du rôle**, entrez le nom `Origin` du rôle et entrez. Ajoutez le deuxième nom de rôle `Destination`. 

    ![Capture d’écran d’ajout du rôle Origin à l’entité Location](./media/add-entities/roles-enter-role-name-text.png)

<a name="add-list-entities"></a>

## <a name="add-list-entities-for-exact-matches"></a>Ajouter des entités de liste pour des correspondances exactes

Les entités de liste représentent un ensemble fixe et fermé de mots liés les uns aux autres. 

Pour une application de ressources humaines, vous pouvez avoir une liste de tous les services, avec tous les synonymes pour ces services. Lorsque vous créez l’entité, vous n’êtes pas obligé de connaître toutes les valeurs. Vous pouvez en ajouter après avoir examiné les énoncés d’utilisateurs réels avec des synonymes.

1. Dans la section **Build**, sélectionnez **Entities** dans le volet gauche, puis **Create new entity**.

1. Dans la boîte de dialogue **Ajouter une entité**, tapez `Department` dans la zone **Nom de l’entité** et sélectionnez **Liste** comme **Type d’entité**. Sélectionnez **Terminé**.
  
1. La page d’entité de liste permet d’ajouter des noms normalisés. Dans la zone de texte **Values**, entrez un élément de service pour la liste, par exemple `HumanResources`, puis appuyez sur la touche Entrée. 

1. À droite de la valeur normalisée, entrez les synonymes, en appuyant sur la touche Entrée après chaque élément.

1. Si vous voulez d’autres éléments normalisés pour la liste, sélectionnez **Recommander** pour voir les options du [dictionnaire sémantique](luis-glossary.md#semantic-dictionary).

    ![Capture d’écran de sélection de la fonctionnalité Recommander pour afficher les options](./media/add-entities/hr-list-2.png)


1. Sélectionnez un élément de la liste recommandée pour l’ajouter aux valeurs normalisées, ou sélectionnez **Tout ajouter** pour ajouter tous les éléments. 
    Vous pouvez importer des valeurs dans une entité de liste existante en utilisant le format JSON suivant :

    ```JSON
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

<a name="change-entity-type"></a>

## <a name="do-not-change-entity-type"></a>Ne modifiez pas le type d’entité

LUIS n’autorise pas la modification du type de l’entité, car il ne sait pas quoi ajouter ou supprimer pour construire cette entité. Pour modifier le type, il est préférable de créer une nouvelle entité du bon type avec un nom légèrement différent. Ensuite, supprimez l’ancien nom d’entité étiqueté et ajoutez le nouveau nom d’entité dans chaque énoncé. Une fois tous les énoncés réétiquetés, supprimez l’ancienne entité. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-pattern-from-an-example-utterance"></a>Créer un modèle à partir d’un exemple d’énoncé

Voir [Ajouter un modèle à partir d’un énoncé existant sur une page d’intention ou d’entité](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="train-your-app-after-changing-model-with-entities"></a>Effectuer l’apprentissage de l’application après avoir changé de modèle avec des entités

Après avoir ajouté, modifié ou supprimé des entités, [entraînez](luis-how-to-train.md) et [publiez](luis-how-to-publish-app.md) votre application pour que vos modifications affectent les requêtes de point de terminaison. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les entités prédéfinies, consultez le projet [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). 

Pour plus d’informations sur la façon dont l’entité apparaît dans la réponse à la requête de point de terminaison JSON, consultez [Extraction de données](luis-concept-data-extraction.md).

Maintenant que vous avez ajouté des intentions, des énoncés et des entités, vous avez une application LUIS de base. Découvrez comment [entraîner](luis-how-to-train.md), [tester](luis-interactive-test.md), et [publier](luis-how-to-publish-app.md) votre application.
 
