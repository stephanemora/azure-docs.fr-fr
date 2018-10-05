---
title: Ajouter des entités dans des applications LUIS
titleSuffix: Azure Cognitive Services
description: Ajoutez des entités (données clés dans le domaine de l’application) dans les applications Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: e82955da24e127e5536c2e40ad2cccf07c5fa173
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032000"
---
# <a name="manage-entities"></a>Gérer des entités
Après avoir identifié les [intentions](luis-concept-intent.md) de votre application, l’objectif est [d’étiqueter les exemples d’énoncés](luis-concept-utterance.md) avec des [entités](luis-concept-entity-types.md). Les entités sont les éléments importants d’une commande ou d’une question, qui peuvent être essentiels pour que l’application cliente effectue sa tâche. 

La **Liste des entités** de la page **Entités** permet d’ajouter, de modifier et de supprimer des entités dans une application. LUIS propose deux grands types d’entités : les [entités prédéfinies](luis-reference-prebuilt-entities.md) et les entités personnalisées.

Les sections suivantes ne sont disponibles qu’au sein d’une application LUIS, dans la section **Build**. Le lien **Build** se trouve dans la barre de navigation supérieure. Dans le menu de navigation gauche de la section **Build**, sélectionnez **Entités**. Il est possible [d’étiqueter](luis-how-to-add-example-utterances.md) au sein de l’énoncé une entité ajoutée à l’application si elle a été apprise automatiquement. Une fois l’application entraînée et publiée, vous pouvez recevoir des données d’entité [extraites](luis-concept-data-extraction.md) de la prédiction. 

## <a name="add-prebuilt-entity"></a>Ajouter une entité prédéfinie
Les entités prédéfinies sont définies dans le projet [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). Celles qui sont le plus souvent ajoutées aux applications sont *number* et *datetimeV2*. 

1. Dans la section **Build** de votre application, cliquez sur **Entités** dans le volet gauche.
 
2. Sur la page **Entités**, cliquez sur **Gérer les entités prédéfinies**.

3. Dans la boîte de dialogue **Ajouter ou supprimer des entités prédéfinies**, sélectionnez les entités prédéfinies **number** et **datetimeV2**. Ensuite, sélectionnez **Terminé**.

    ![Capture d’écran de la boîte de dialogue Ajouter des entités prédéfinies](./media/add-entities/list-of-prebuilt-entities.png)

    Pour plus d’informations sur l’extraction de l’entité prédéfinie à partir de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#prebuilt-entity-data).

## <a name="add-simple-entities"></a>Ajouter des entités simples
Une entité simple est une entité générique décrivant un concept unique. 

1. Dans la section **Build** de votre application, cliquez sur **Entités** dans le volet gauche, puis sélectionnez **Créer une entité**.

2. Dans la boîte de dialogue contextuelle, tapez `Airline` dans la zone **Nom de l’entité**, sélectionnez **Simple** dans la liste **Type d’entité**, puis sélectionnez **Terminé**.

    ![Capture d’écran de la boîte de dialogue de création d’une entité simple Airline](./media/add-entities/create-simple-airline-entity.png)

    Pour en savoir plus sur l’extraction de l’entité simple à partir de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#simple-entity-data). Pour en savoir plus sur l’utilisation d’une entité simple, essayez le [démarrage rapide](luis-quickstart-primary-and-secondary-data.md) d’entité simple.

## <a name="add-regular-expression-entities"></a>Ajouter des entités d’expression régulière
Une entité d’expression régulière sert à extraire des données de l’énoncé suivant l’expression régulière fournie. 

1. Dans votre application, sélectionnez **Entités** dans le volet de navigation gauche, puis **Créer une entité**.

2. Dans la boîte de dialogue contextuelle, entrez `AirFrance Flight` dans la zone **Nom de l’entité**, sélectionnez **Expression régulière** dans la liste **Type d’entité**, entrez l’expression régulière `AFR[0-9]{3,4}`, puis sélectionnez **Terminé**. 

    Cette expression régulière Airfrance Flight attend trois caractères, littéralement `AFR`, puis trois ou quatre chiffres compris entre 0 et 9. Elle correspond aux numéros de vol Air France, par exemple : « AFR101 », « ARF1302 » ou « AFR5006 ». Pour plus d’informations sur l’extraction de l’entité à partir de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md).

    ![Image de la boîte de dialogue de création d’une entité d’expression régulière](./media/add-entities/regex-entity-create-dialog.png)

    Pour plus d’informations sur l’extraction de l’entité d’expression régulière à partir de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#regular-expression-entity-data). Pour plus d’informations sur l’utilisation d’une entité d’expression régulière, essayez le [guide de démarrage rapide](luis-quickstart-intents-regex-entity.md) des entités d’expression régulière.

## <a name="add-hierarchical-entities"></a>Ajouter des entités hiérarchiques
Une entité hiérarchique est une catégorie d’entités apprises de façon contextuelle et associées de façon conceptuelle. Dans l’exemple suivant, l’entité contient des lieux de départ et de destination. 

Dans l’énoncé `Book 2 tickets from Seattle to Cairo`, Seattle est le lieu de départ et Cairo (Le Caire) le lieu de destination. Chaque lieu est différent sur le plan du contexte, et appris à partir de l’ordre des mots et du choix des mots dans l’énoncé.

Pour ajouter des entités hiérarchiques, suivez les étapes ci-dessous : 

1. Dans votre application, sélectionnez **Entités** dans le volet de navigation gauche, puis **Créer une entité**.

2. Dans la boîte de dialogue contextuelle, tapez `Location` dans la zone **Nom de l’entité**, puis sélectionnez **Hiérarchique** dans la liste **Type d’entité**.

    ![Ajouter une entité hiérarchique](./media/add-entities/hier-location-entity-creation.png)

3. Sélectionnez **Ajouter un enfant**, puis tapez « Origin » dans la zone **Enfant 1**. 

4. Sélectionnez **Ajouter un enfant**, puis tapez « Destination » dans la zone **Enfant 2**. Sélectionnez **Terminé**.
5. 
    >[!NOTE]
    >Pour supprimer un enfant, sélectionnez l’icône corbeille qui l’accompagne.

    >[!CAUTION]
    >Les noms d’entité enfant doivent être uniques parmi toutes les entités dans une même application. Deux entités hiérarchiques différentes ne peuvent pas contenir d’entités enfants portant le même nom. 

    Pour en savoir plus sur l’extraction de l’entité hiérarchique à partir de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#hierarchical-entity-data). Pour en savoir plus sur l’utilisation d’une entité hiérarchique, essayez le [démarrage rapide](luis-quickstart-intent-and-hier-entity.md) d’entité hiérarchique.

## <a name="add-composite-entities"></a>Ajouter des entités composites
Les entités composites permettent de définir des relations entre plusieurs entités existantes. Dans l’exemple suivant, l’entité contient un nombre de billets, un point de départ et une destination. 

Dans l’énoncé `Book 2 tickets from Seattle to Cairo`, le nombre 2 est associé à une entité prédéfinie, Seattle est le lieu de départ et Cairo (Le Caire) le lieu de destination. Une fois l’entité composite créée, chaque entité fait partie d’une entité parent.

1. Dans votre application, ajoutez l’entité prédéfinie **number**. Pour connaître les instructions à suivre, voir [Ajouter des entités prédéfinies](#add-prebuilt-entity). 

2. Ajoutez l’entité hiérarchique `Location`, y compris les sous-types : `origin` et `destination`. Pour connaître les instructions à suivre, voir [Ajouter des entités hiérarchiques](#add-hierarchical-entities). 

3. Sélectionnez **Entités** dans le volet de navigation gauche, puis **Créer une entité**.

4. Dans la boîte de dialogue contextuelle, tapez `TicketsOrder` dans la zone **Nom de l’entité**, puis sélectionnez **Composite** dans la liste **Type d’entité**.

5. Sélectionnez **Ajouter un enfant** pour ajouter un enfant.

6. Dans **Enfant 1**, sélectionnez l’entité **number** dans la liste.

7. Dans **Enfant 2**, sélectionnez l’entité **Location::Origin** dans la liste. 

8. Dans **Enfant 3**, sélectionnez l’entité **Location::Destination** dans la liste. 

9. Sélectionnez **Terminé**.

    ![Image de la boîte de dialogue de création d’une entité composite](./media/add-entities/ticketsorder-composite-entity.png)

    >[!NOTE]
    >Pour supprimer un enfant, sélectionnez l’icône corbeille qui l’accompagne.

    Pour en savoir plus sur l’extraction de l’entité composite à partir de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#composite-entity-data). Pour en savoir plus sur l’utilisation d’une entité composite, essayez le [didacticiel](luis-tutorial-composite-entity.md) sur les entités composites.


## <a name="add-patternany-entities"></a>Ajouter des entités Pattern.any
Les entités [Pattern.Any](luis-concept-entity-types.md) ne sont valides que dans les [modèles](luis-how-to-model-intent-pattern.md). Elles aident LUIS à trouver la fin des entités présentant une longueur variable et différents choix de mots dans les énoncés, dans la mesure où elles sont utilisées dans des modèles.

Si l’application a une intention `FindBookInfo`, le titre du livre risque d’interférer avec la prédiction de l’intention. Utilisez une entité Pattern.any au sein d’un modèle pour préciser quels mots font partie du titre du livre. La prédiction LUIS commence par l’énoncé, qui fait l’objet d’une vérification et d’une mise en correspondance avec les entités. Une fois les entités identifiées, le modèle est examiné et mis en correspondance. 

Dans l’énoncé `Who wrote the book Ask and when was it published?`, le titre du livre, Ask, est difficile, car le contexte ne permet pas de déterminer à coup sûr où se termine le titre et où commence le reste de l’énoncé. Les mots peuvent être dans n’importe quel ordre dans les titres de livres : un seul mot, des expressions complexes avec des signes de ponctuation ou des ordres de mots dénués de sens. Un modèle permet de créer une entité permettant d’extraire avec exactitude la totalité de l’entité. Une fois le titre du livre trouvé, l’intention `FindBookInfo` est prédite, car elle correspond au modèle.

1. Dans la section **Build** de votre application, cliquez sur **Entités** dans le volet gauche, puis sélectionnez **Créer une entité**.

2. Dans la boîte de dialogue **Ajouter une entité**, tapez `BookTitle` dans la zone **Nom de l’entité** et sélectionnez **Pattern.any** comme **Type d’entité**.
 
    ![Capture d’écran de création d’une entité Pattern.any](./media/add-entities/create-pattern-any-entity.png)

    Pour utiliser l’entité Pattern.any, ajoutez un modèle sur la page **Modèles** (sous la section **Améliorer les performances de l’application**) avec la syntaxe entre accolades, par exemple, « For **{BookTitle}** who is the author? ».

    Pour plus d’informations sur l’extraction de l’entité Pattern.any à partir de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#patternany-entity-data). Pour savoir comment utiliser une entité Pattern.any, essayez le tutoriel [Modèle](luis-tutorial-pattern.md).

Si vous constatez que votre modèle, quand il comporte une entité Pattern.any, extrait mal les entités, utilisez une [liste explicite](luis-concept-patterns.md#explicit-lists) pour corriger ce problème. 

## <a name="add-role-to-pattern-based-entity"></a>Ajouter un rôle à une entité reposant sur un modèle
Un rôle est un sous-type nommé d’une entité en fonction du contexte. Il s’apparente à une entité [hiérarchique](#add-hierarchical-entities), à ceci près qu’il ne peut être utilisé que dans un [modèle](luis-how-to-model-intent-pattern.md). 

Par exemple, un billet d’avion a une *ville d’origine* et une *ville de destination*, mais les deux sont des villes. LUIS conclut que ce sont des villes et peut déterminer la ville d’origine et la ville de destination en fonction du contexte (ordre et choix des mots). 

La syntaxe d’un rôle est **{Nom de l’entité:Nom du rôle}**, le nom de l’entité étant suivi de deux-points, puis du nom du rôle. Exemple : « Book a ticket from {Location:Origin} to {Location:Destination} ».

1. Dans la section **Build** de votre application, sélectionnez **Entités** dans le volet gauche.

2. Sélectionnez **Créer une entité**. Entrez le nom de `Location`. Sélectionnez le type **Simple**, puis **Terminé**.

3. Sélectionnez **Entités** dans le volet gauche, puis la nouvelle entité **Location** créée à l’étape 2.

4. Dans la zone de texte **Nom du rôle**, entrez le nom `Origin` du rôle et entrez. Ajoutez le deuxième nom de rôle `Destination`. Par exemple, un trajet en avion peut avoir une ville d’origine et une ville de destination. Les deux rôles sont « Origin » et « Destination ».

    ![Capture d’écran d’ajout du rôle Origin à l’entité Location](./media/add-entities/roles-enter-role-name-text.png)

    Pour plus d’informations sur l’extraction de rôles à partir de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md). Pour savoir comment utiliser une entité Pattern.any, essayez le tutoriel Modèle.

## <a name="add-list-entities"></a>Ajouter des entités de liste
Les entités de liste représentent un ensemble fixe et fermé de mots liés les uns aux autres dans le système. 

Pour une entité list drinks (boissons), vous pouvez avoir deux valeurs normalisées : water (eau) et soda pop (boisson gazeuse). Chaque nom normalisé a des synonymes. Pour water (eau), les synonymes sont H2O, gas, flat (gazeuse, plate). Pour soda pop (boisson gazeuse), les synonymes sont fruit, cola et ginger. Lorsque vous créez l’entité, vous n’êtes pas obligé de connaître toutes les valeurs. Vous pouvez en ajouter après avoir examiné les énoncés d’utilisateurs réels avec des synonymes.

|Nom normalisé|Synonymes|
|--|--|
|Water (Eau)|H2O, gas, flat (gazeuse, plate)|
|Soda pop (boisson gazeuse)|Fruit, cola, ginger|

1. Dans la section **Build** de votre application, cliquez sur **Entités** dans le volet gauche, puis sélectionnez **Créer une entité**.

2. Dans la boîte de dialogue **Ajouter une entité**, tapez `Drinks` dans la zone **Nom de l’entité** et sélectionnez **Liste** comme **Type d’entité**. Sélectionnez **Terminé**.
 
    ![Image de la boîte de dialogue de création d’une entité de liste Drinks](./media/add-entities/menu-list-dialog.png)
  
3.  La page d’entité de liste permet d’ajouter des noms normalisés. Dans la zone de texte **Valeurs**, entrez un élément de la liste, par exemple, `Water` pour la liste Drinks, puis appuyez sur la touche ENTRÉE. 

    ![Capture d’écran de la liste Drinks avec la valeur normalisée Water dans la zone de texte](./media/add-entities/entity-list-normalized-name.png)

4. À droite de la valeur normalisée **Water**, entrez les synonymes `h20`, `flat` et `gas`, en appuyant sur la touche Entrée après chaque élément.

    ![Capture d’écran de l’entité de liste Drinks avec les éléments synonymes de Water en surbrillance](./media/add-entities/menu-list-synonyms.png)

5. Si vous voulez d’autres éléments normalisés pour la liste, sélectionnez **Recommander** pour voir les options du [dictionnaire sémantique](luis-glossary.md#semantic-dictionary).

    ![Capture d’écran de l’entité de liste Drinks avec les éléments recommandés en surbrillance](./media/add-entities/entity-list-recommended-list.png)

6. Sélectionnez un élément de la liste recommandée pour l’ajouter aux valeurs normalisées, ou sélectionnez **Tout ajouter** pour ajouter tous les éléments. 

    ![Capture d’écran de l’entité de liste Drinks avec l’élément recommandé Beer et le bouton Tout ajouter en surbrillance](./media/add-entities/list-entity-add-suggestions.png)

    Pour en savoir plus sur l’extraction d’entités de liste de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#list-entity-data). Pour en savoir plus sur l’utilisation d’une entité de liste, essayez le [démarrage rapide](luis-quickstart-intent-and-list-entity.md).

## <a name="import-list-entity-values"></a>Importer des valeurs d’entité de liste
Il est possible d’importer des valeurs dans une entité de liste existante.

 1. Sur la page d’entité de liste, sélectionnez **Importer des listes**.

 2. Dans la boîte de dialogue **Importer de nouvelles entrées**, sélectionnez **Choisir un fichier** et sélectionnez le fichier JSON qui comporte la liste.

    ![Capture d’écran de la boîte de dialogue contextuelle Importer des valeurs d’entité de liste](./media/add-entities/menu-list-import-json-dialog-with-file.png)

    >[!NOTE]
    >LUIS importe uniquement les fichiers portant l’extension « .json ».

 3. Pour en savoir plus sur la syntaxe de liste prise en charge dans JSON, sélectionnez **En savoir plus sur la syntaxe de liste prise en charge** pour développer la boîte de dialogue et afficher un exemple de syntaxe autorisée. Pour réduire la boîte de dialogue et masquer la syntaxe, sélectionnez à nouveau le titre du lien.

 4. Sélectionnez **Terminé**.

    Voici un exemple de code JSON valide pour une entité de liste **Colors** :

    ```
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

## <a name="edit-entity-name"></a>Modifier le nom de l’entité
1. Sur la page de la liste **Entités**, sélectionnez l’entité dans la liste. La page **Entité** s’ouvre.

2. Sur la page **Entité**, modifiez le nom de l’entité en sélectionnant l’icône de modification qui se trouve à côté. Le type d’entité n’est pas modifiable. 

## <a name="delete-entity"></a>Supprimer une entité

Sur la page **Entité**, sélectionnez le bouton **Supprimer l’entité**. Ensuite, cliquez sur **OK** dans le message de confirmation pour confirmer la suppression.
 
![Capture d’écran de la page de l’entité Location avec le bouton Supprimer l’entité en surbrillance](./media/add-entities/entity-delete.png)

>[!NOTE]
>* La suppression d’une entité hiérarchique a pour effet de supprimer toutes ses entités enfants.
>* La suppression d’une entité composite a pour effet de supprimer l’entité composite et de rompre la relation composite, mais non de supprimer les entités qui la composent.

## <a name="changing-entity-type"></a>Modifier le type d’entité
LUIS n’autorise pas la modification du type de l’entité, car il ne sait pas quoi ajouter ou supprimer pour construire cette entité. Pour modifier le type, il est préférable de créer une nouvelle entité du bon type avec un nom légèrement différent. Ensuite, supprimez l’ancien nom d’entité étiqueté et ajoutez le nouveau nom d’entité dans chaque énoncé. Une fois tous les énoncés réétiquetés, supprimez l’ancienne entité. 

## <a name="create-a-pattern-from-an-utterance"></a>Créer un modèle à partir d’un énoncé
Voir [Ajouter un modèle à partir d’un énoncé existant sur une page d’intention ou d’entité](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="search-utterances"></a>Rechercher parmi des énoncés
L’icône loupe de la barre d’outils permet de faire des recherches et des filtres sur les énoncés. 

## <a name="train-your-app-after-changing-model-with-entities"></a>Effectuer l’apprentissage de l’application après avoir changé de modèle avec des entités
Après avoir ajouté, modifié ou supprimé des entités, [entraînez](luis-how-to-train.md) et [publiez](luis-how-to-publish-app.md) votre application pour que vos modifications affectent les requêtes de point de terminaison. 

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez ajouté des intentions, des énoncés et des entités, vous avez une application LUIS de base. Découvrez comment [entraîner](luis-how-to-train.md), [tester](luis-interactive-test.md), et [publier](luis-how-to-publish-app.md) votre application.
 
