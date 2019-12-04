---
title: Ajouter des entités - LUIS
titleSuffix: Azure Cognitive Services
description: Créez des entités pour extraire les données clés des énoncés de l'utilisateur dans les applications LUIS (Language Understanding). Les données d’entité extraites sont utilisées par l’application cliente pour satisfaire aux demandes des clients.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1f2b293acdc77e25e6b932c47d466cc28a04a2b6
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383683"
---
# <a name="add-entities-to-extract-data"></a>Ajouter des entités pour extraire des données 

Créez des entités pour extraire les données clés des énoncés de l'utilisateur dans les applications LUIS (Language Understanding). Les données d’entité extraites sont utilisées par votre application cliente pour satisfaire aux demandes des clients.

L’entité représente un mot ou une phrase dans l’énoncé que vous souhaitez extraire. Les entités décrivent des informations relatives à l’intention, et sont parfois essentiels pour que votre application effectue sa tâche. Vous pouvez créer des entités lorsque vous ajoutez un exemple d’énoncé à une intention (et avant ou après).

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>Planifier des entités, puis créer et étiqueter

Il est possible de créer des entités issues du Machine Learning à partir des exemples d’énoncés ou de la page **Entités**. 

En règle générale, consacrer du temps à la planification des entités avant de créer une entité issue du Machine Learning dans le portail se révèle une bonne pratique. Créez ensuite l’entité issue du Machine Learning à partir de l’exemple d’énoncé avec tous les détails dans les sous-composants, les descripteurs et les contraintes que vous connaissez à ce moment-là. Le [tutoriel sur les entités décomposables](tutorial-machine-learned-entity.md) montre comment utiliser cette méthode. 

Dans le cadre de la planification des entités, vous savez peut-être déjà que vous avez besoin d’entités de correspondance à du texte (comme des entités prédéfinies, des entités d’expression régulière ou des entités de liste). Vous pouvez les créer à partir de la page **Entités** avant de les étiqueter dans les exemples d’énoncés. 

Lors de l’étiquetage, vous pouvez étiqueter des entités individuelles, puis créer une entité issue du Machine Learning parente. Vous pouvez aussi commencer avec une entité issue du Machine Learning parente et la décomposer en entités enfants. 

> [!TIP] 
>Étiquetez tous les mots pouvant indiquer une entité, même si ces mots ne sont pas utilisés lors de l’extraction dans l’application cliente. 

## <a name="creating-an-entity-before-or-with-labeling"></a>Création d’une entité avant ou pendant l’étiquetage

Utilisez le tableau suivant pour comprendre quelles entités créer, puis les créer ou les ajouter à l’application. 

|Type d’entité|Où créer l’entité dans le portail LUIS|
|--|--|
|Entité apprise par la machine|Détail de l’entité ou de l’intention|
|Entité de liste|Détail de l’entité ou de l’intention|
|Entité d’expression régulière|Entités|
|Entité Pattern.any|Entités|
|Entité prédéfinie|Entités|
|Détail du domaine prédéfini|Entités|

Vous pouvez créer toutes les entités à partir de la page **Entités**, ou créer deux entités dans le cadre de l’étiquetage de l’entité dans l’exemple d’énoncé de la page de **détail de l’intention**. Vous pouvez uniquement _étiqueter_ une entité dans un exemple d’énoncé à partir de la page de **détail de l’intention**. 

## <a name="create-a-machine-learned-entity"></a>Créez une entité issue du Machine Learning

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Créer une entité de correspondance à du texte

L’utilisation d’entités de correspondance à du texte permet d’extraire des données de plusieurs façons :

|Entités de correspondance à du texte|Objectif|
|--|--|
|[Entité de liste](#add-list-entities-for-exact-matches)|Liste de noms canoniques complétée de synonymes en tant que formes alternatives|
|Entité d’expression régulière|Correspondance à du texte à l’aide d’une entité d’expression régulière|
|[Entité prédéfinie](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|Correspondance à des types de données courants comme un nombre, un e-mail, une date|
|Détail du domaine prédéfini|Correspondance à l’aide de domaines sélectionnés|
|[Pattern.any](#add-a-patternany-entity)| Correspondance à des entités pouvant être facilement confondues avec le texte environnant|  

Les entités prédéfinies fonctionnent sans fournir de données d’entraînement personnalisées. Les autres entités nécessitent que vous fournissiez des données d’entraînement de client (comme les éléments de l’entité de liste) ou une expression (comme une expression régulière ou pattern.any).

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Comment créer une entité personnalisée

1. Dans le portail LUIS, accédez à la section **Gérer**, puis à la page **Entités**. 
1. Sélectionnez **+ Créer**, puis le type d’entité. 
1. Poursuivez la configuration de l’entité, puis sélectionnez **Créer** une fois que vous avez terminé. 

### <a name="add-list-entities-for-exact-matches"></a>Ajouter des entités de liste pour des correspondances exactes

Les entités de liste représentent un ensemble fixe et fermé de mots liés les uns aux autres. Alors que vous pouvez, en tant qu’auteur, modifier la liste, LUIS ne peut l’augmenter ni la réduire. Vous pouvez également effectuer une importation vers une entité de liste existante à l’aide d’un [list entity .json format(reference-entity-list.md#example-json-to-import-into-list-entity). 

La liste suivante montre le nom canonique et les synonymes. 

|Couleur : nom d’élément de liste|Couleur : synonymes|
|--|--|
|Rouge|carmin, sang, cerise, camion de pompier|
|Bleu|ciel, azur, cobalt|
|Vert|émeraude, olive|

Utilisez la procédure pour créer une entité de liste. Une fois l’entité de liste créée, vous n’avez pas besoin d’étiqueter les exemples d’énoncés dans une intention. Les éléments de liste et les synonymes sont mis en correspondance à l’aide du texte exact. 

1. Dans la section **Build**, sélectionnez **Entités** dans le panneau gauche, puis **+ Créer**.

1. Dans la boîte de dialogue **Créer un type d’entité**, entrez le nom de l’entité, comme `Colors`, puis sélectionnez **Liste**.
1. Dans la boîte de dialogue **Créer une entité de liste**, dans **Ajouter une nouvelle sous-liste**, entrez le nom de l’élément de liste, comme `Green`, puis ajoutez des synonymes.

    > [!div class="mx-imgBorder"]
    > ![Créez une liste de couleurs en tant qu’entité de liste dans la page de détail de l’entité.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. Quand vous avez terminé d’ajouter des éléments de liste et des synonymes, sélectionnez **Créer**.

    Quand vous avez terminé d’apporter un groupe de modifications à l’application, n’oubliez pas d’**entraîner** l’application. N’entraînez pas l’application après une seule modification. 

    > [!NOTE]
    > Cette procédure montre comment créer et étiqueter une entité de liste à partir d’un exemple d’énoncé dans la page de **détail de l’intention**. Vous pouvez également créer la même entité à partir de la page **Entités**.

## <a name="add-a-role-for-an-entity"></a>Ajouter un rôle pour une entité

Un rôle est un sous-type nommé d’une entité, en fonction du contexte. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Ajouter un rôle pour distinguer les différents contextes

Dans l’énoncé suivant, il existe deux emplacements, chacun étant spécifié sémantiquement par les mots qui l’entourent, comme `to` et `from` : 

`Pick up the package from Seattle and deliver to New York City.`

Dans cette procédure, ajoutez des rôles `origin` et `destination` à une entité geographyV2 prédéfinie.

1. Dans la section **Build**, sélectionnez **Entities** dans le volet gauche.

1. Sélectionnez **+ Ajouter une entité prédéfinie**. Sélectionnez **geographyV2**, puis **Terminé**. Une entité prédéfinie est alors ajoutée à l’application.
    
    Si vous constatez que votre modèle, quand il comporte une entité Pattern.any, extrait mal les entités, utilisez une [liste explicite](reference-pattern-syntax.md#explicit-lists) pour corriger ce problème. 

1. Sélectionnez l’entité geographyV2 prédéfinie qui vient d’être ajoutée dans la liste des entités de la page **Entités**. 
1. Pour ajouter un nouveau rôle, sélectionnez **+** en regard de **Aucun rôle ajouté**.
1. Dans la zone de texte **Rôle de type**, entrez le nom du rôle `Origin`, puis appuyez sur Entrée. Ajoutez un deuxième nom de rôle `Destination`, puis appuyez sur Entrée. 

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran d’ajout du rôle Origin à l’entité Location](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    Le rôle est ajouté à l’entité prédéfinie, mais il n’est pas ajouté aux énoncés qui utilise cette entité. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Étiqueter le texte avec un rôle dans un exemple d’énoncé

1. Accédez à la page de détail de l’intention, qui contient des exemples d’énoncés qui utilisent le rôle. 
1. Pour étiqueter avec le rôle, sélectionnez l’étiquette d’entité (ligne continue sous le texte) dans l’exemple d’énoncé, puis sélectionnez **Afficher dans la palette d’entités** dans la liste déroulante. 

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la sélection de l’option Afficher dans la palette d’entités](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    La palette d’entités s’ouvre à droite. 

1. Sélectionnez l’entité, puis accédez au bas de la palette et sélectionnez le rôle. 

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la sélection de l’option Afficher dans la palette d’entités](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Ajouter une entité pattern.any

Les entités [pattern.any](luis-concept-entity-types.md) sont uniquement valides dans les [modèles](luis-how-to-model-intent-pattern.md), et non dans les exemples d’énoncés des intentions. Ce type d’entité aide LUIS à trouver la fin des entités présentant une longueur variable et différents choix de mots. Cette entité étant utilisée dans un modèle, LUIS sait où se trouve la fin de l’entité dans le modèle d’énoncé.

### <a name="steps-to-create-a-patternany-entity"></a>Étapes de création d’une entité pattern.any

1. Dans la section **Build**, sélectionnez **Entités** dans le panneau gauche, puis **+ Créer**.

1. Dans la boîte de dialogue **Choisir un type d’entité**, entrez le nom de l’entité dans la zone **Nom**, puis sélectionnez **pattern.any** comme **Type** et sélectionnez **Créer**.

    Une fois que vous [avez créé un énoncé de modèle](luis-how-to-model-intent-pattern.md) à l’aide de cette entité, l’entité est extraite avec un algorithme issu du Machine Learning et de correspondance à du texte combiné. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Créer un énoncé de modèle pour utiliser l’entité pattern.any

Pour utiliser l’entité Pattern.any, ajoutez un modèle dans la page **Patterns**, dans la section **Improve app performance** (Améliorer les performances de l’application) avec la syntaxe entre accolades correcte, par exemple `Where is **{HumanResourcesFormTitle}** on the server?`.

Si vous constatez que votre modèle, quand il comporte une entité Pattern.any, extrait mal les entités, utilisez une [liste explicite](reference-pattern-syntax.md#explicit-lists) pour corriger ce problème. 

## <a name="do-not-change-entity-type"></a>Ne modifiez pas le type d’entité

LUIS n’autorise pas la modification du type de l’entité, car il ne sait pas quoi ajouter ou supprimer pour construire cette entité. Pour modifier le type, il est préférable de créer une nouvelle entité du bon type avec un nom légèrement différent. Ensuite, supprimez l’ancien nom d’entité étiqueté et ajoutez le nouveau nom d’entité dans chaque énoncé. Une fois tous les énoncés réétiquetés, supprimez l’ancienne entité. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Utiliser des modèles prédéfinis](howto-add-prebuilt-models.md) 

Pour en savoir plus :
* Guide pratique pour [effectuer l’apprentissage](luis-how-to-train.md)
* Guide pratique pour [tester](luis-interactive-test.md)
* Guide pratique pour [publier](luis-how-to-publish-app.md)
* Modèles :
    * [Concepts](luis-concept-patterns.md)
    * [Syntaxe](reference-pattern-syntax.md)
* [Référentiel GitHub des entités prédéfinies](https://github.com/Microsoft/Recognizers-Text)
* [Concepts de l’extraction des données](luis-concept-data-extraction.md)


 
