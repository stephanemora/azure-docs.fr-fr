---
title: Didacticiel de création d’une application LUIS pour obtenir des données correspondant à une liste - Azure | Microsoft Docs
description: Dans ce didacticiel, vous allez découvrir comment créer une application LUIS simple utilisant des entités de type liste et des intentions pour extraire les données dans ce guide de démarrage rapide.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 33394dff1091f27c79c74d8648a90724ba8d6698
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264825"
---
# <a name="tutorial-create-app-using-a-list-entity"></a>Didacticiel : Créer une application à l’aide d’une entité de type liste
Dans ce didacticiel, créez une application qui montre comment obtenir des données correspondant à une liste prédéfinie. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendre les entités de type liste 
> * Créer une application LUIS pour le domaine des boissons avec l’intention OrderDrinks
> * Ajouter l’intention _None_ ainsi que des exemples d’énoncés
> * Ajouter une entité de type liste pour extraire les éléments relatifs aux boissons d’un énoncé
> * Effectuer l’apprentissage de l’application et la publier
> * Interroger un point de terminaison de l’application pour voir la réponse JSON de LUIS

Pour cet article, vous devez disposer d’un compte [LUIS][LUIS] gratuit afin de créer votre application LUIS.

## <a name="purpose-of-the-list-entity"></a>Objet de l’entité de type liste
Cette application prend les commandes de boissons, par exemple `1 coke and 1 milk please`, et retourne les données telles que le type de boisson. Une entité **list** de boissons recherche les correspondances de texte exactes et les retourne. 

Une entité de type liste est un choix approprié pour ce type de données lorsque les valeurs des données sont un ensemble connu. Les noms des boissons peuvent varier et inclure de l’argot et des abréviations, mais ils ne changent pas fréquemment. 

## <a name="app-intents"></a>Ajouter des intentions
Les intentions sont des catégories correspondant aux souhaits de l’utilisateur. Cette application compte deux intentions : OrderDrink et None. L’intention [None](luis-concept-intent.md#none-intent-is-fallback-for-app) est délibérée, pour indiquer tout élément extérieur à l’application.  

## <a name="list-entity-is-an-exact-text-match"></a>L’entité de type liste est une correspondance de texte exacte
L’objet de l’entité est de rechercher et de classer les parties du texte figurant dans l’énoncé. Une entité [list](luis-concept-entity-types.md) permet une correspondance exacte de mots ou d’expressions.  

Pour cette application relative aux boissons, LUIS extrait la commande de boisson de telle façon qu’une commande standard peut être créée et exécutée. LUIS autorise des énoncés comptant des variantes, des abréviations et de l’argot. 

Exemples simples d’énoncés d’utilisateurs :

```
2 glasses of milk
3 bottles of water
2 cokes
```

Versions abrégées ou argotiques d’énoncés :

```
5 milk
3 h2o
1 pop
```
 
L’entité de type liste fait correspondre `h2o` à eau et `pop` à boisson sans alcool.  

## <a name="what-luis-does"></a>Action de LUIS
LUIS en a terminé lorsque l’intention et les entités de l’énoncé sont identifiées, [extraites](luis-concept-data-extraction.md#list-entity-data)et retournées au format JSON à partir du [point de terminaison](https://aka.ms/luis-endpoint-apis). L’application d’appel ou le chatbot prend cette réponse JSON et répond à la demande d’une façon qui dépend de la conception de l’application ou du chatbot. 

## <a name="create-a-new-app"></a>Créer une application
1. Connectez-vous au site web [LUIS][LUIS]. Veillez à vous connecter à la [région][LUIS-regions] où vous avez besoin de points de terminaison LUIS publiés.

2. Sur le site web [LUIS][LUIS], sélectionnez **Créer une application**.  

    ![Créer une application](./media/luis-quickstart-intent-and-list-entity/app-list.png)

3. Dans la boîte de dialogue contextuelle, entrez le nom `MyDrinklist`. 

    ![Nommer l’application MyDrinkList](./media/luis-quickstart-intent-and-list-entity/create-app-dialog.png)

4. À l’issue de ce processus, l’application affiche la page **Intents** (Intentions) avec l’intention **None**. 

    [![](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png "Capture d’écran de la page Intents (Intentions)")](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>Créer une intention

1. Dans la page **Intents** (Intentions), sélectionnez **Create new intent** (Créer une intention). 

    [![](media/luis-quickstart-intent-and-list-entity/create-new-intent.png "Capture d’écran de la page Intents (Intentions) avec le bouton Create new intent (Créer une intention) mis en surbrillance")](media/luis-quickstart-intent-and-list-entity/create-new-intent.png#lightbox)

2. Entrez le nom de la nouvelle intention `OrderDrinks`. Cette intention doit être sélectionnée à chaque fois qu’un utilisateur souhaite commander une boisson.

    En créant une intention, vous créez la catégorie principale d’informations que vous souhaitez identifier. Attribuer un nom à la catégorie permet à toute autre application utilisant les résultats de la requête LUIS d’utiliser ce nom de catégorie pour trouver une réponse appropriée ou agir de façon adéquate. LUIS ne répond pas à ces questions, et identifie uniquement le type d’informations demandé dans un langage naturel. 

    [![](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png "Capture d’écran de création d’une intention OrderDrings")](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png#lightbox)

3. Ajoutez plusieurs énoncés à l’intention `OrderDrinks` qu’un utilisateur est susceptible de demander selon vous :

    | Exemples d’énoncés|
    |--|
    |Merci de m’apporter 2 cocas et une bouteille d’eau dans ma chambre|
    |2 eaux gazeuses avec un zeste de citron vert|
    |H2O|

    [![](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png "Capture d’écran de saisie d’énoncé sur la page de l’intention OrderDrinks")](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Ajouter des énoncés à l’intention None

Pour le moment, l’application LUIS ne dispose d’aucun énoncé pour l’intention **None**. Comme elle a besoin d’énoncés auxquels vous ne souhaitez pas que l’application réponde, elle doit disposer d’énoncés dans l’intention **None**. Renseignez cette zone. 

1. Dans le panneau gauche, sélectionnez **Intents** (Intentions). 

    [![](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png "Capture d’écran de sélection du lien Intents (Intentions) dans le panneau gauche")](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png#lightbox)

2. Sélectionnez l’intention **None**. Ajoutez trois énoncés que votre utilisateur est susceptible d’entrer, mais qui ne sont pas pertinents pour votre application :

    | Exemples d’énoncés|
    |--|
    |Annuler|
    |Au revoir|
    |Que se passe-t-il ?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>Lorsque l’énoncé est prédit pour l’intention None
Dans votre application d’appel de LUIS, comme un chatbot, si LUIS retourne l’intention **None** pour un énoncé, votre robot peut demander si l’utilisateur souhaite mettre fin à la conversation. Le robot peut également donner d’autres directions pour poursuivre la conversation si l’utilisateur ne souhaite pas y mettre un terme. 

Les entités fonctionnent dans l’intention **None**. Si l’intention à notation supérieure est **None**, mais qu’une entité significative pour votre chatbot est extraite, votre chatbot peut y faire suite avec une question axée sur l’intention du client. 

## <a name="create-a-menu-entity-from-the-intent-page"></a>Créer une entité de type menu à partir de la page Intents (Intentions)
Maintenant que les deux intentions disposent d’énoncés, LUIS doit comprendre ce qu’est une boisson. Revenez à l’intention `OrderDrinks` et étiquetez (marquez) les boissons dans un énoncé en procédant comme suit :

1. Revenez à l’intention `OrderDrinks` en sélectionnant **Intents** (Intentions) dans le panneau gauche.

2. Dans la liste des intentions, sélectionnez `OrderDrinks`.

3. Dans l’énoncé `Please send 2 cokes and a bottle of water to my room`, sélectionnez le mot `water`. Un menu déroulant s’affiche avec une zone de texte dans sa partie supérieure permettant de créer une entité. Entrez le nom de l’entité `Drink` dans la zone de texte, puis sélectionnez **Créer une entité** dans le menu déroulant. 

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png "Capture d’écran de création d’une entité en sélectionnant un mot dans un énoncé")](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png#lightbox)

4. Dans la fenêtre contextuelle, sélectionnez le type d’entité **List**. Ajoutez un synonyme `h20`. Appuyez sur la touche Entrée après chaque synonyme. N’ajoutez pas `perrier` à la liste de synonymes. Cet élément est ajouté à l’étape suivante en tant qu’exemple. Sélectionnez **Terminé**.

    [![](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png "Capture d’écran de configuration de la nouvelle entité")](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png#lightbox)

5. Maintenant que l’entité est créée, étiquetez les autres synonymes de l’eau en sélectionnant le synonyme de l’eau, puis sélectionnez `Drink` dans la liste déroulante. Suivez le menu à droite, puis sélectionnez `Set as synonym`, puis `water`.

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png "Capture d’écran d’étiquetage d’un énoncé avec une entité existante")](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png#lightbox)

## <a name="modify-the-list-entity-from-the-entity-page"></a>Modifier l’entité de type liste dans la page Entité
L’entité de type liste de boissons est créée, mais elle ne compte pas de nombreux éléments ni synonymes. Si vous connaissez certains termes, abréviations et expressions argotiques, il est plus rapide de remplir la liste de la page **Entité**. 

1. Dans le panneau gauche, sélectionnez **Entités**.

    [![](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png "Capture d’écran de sélection du lien Entités dans le panneau gauche")](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png#lightbox)

2. Dans la liste des entités, sélectionnez `Drink`.

    [![](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png "Capture d’écran de sélection d’une entité Drink dans la liste des entités")](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png#lightbox)

3. Dans la zone de texte, entrez `Soda pop`, puis appuyez sur la touche Entrée. Il s’agit d’un terme généralement appliqué aux boissons gazeuses. Chaque culture possède un surnom ou un terme argotique pour désigner ce type de boisson.

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png "Capture d’écran de saisie d’un nom canonique")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png#lightbox)

4. Sur la même ligne que `Soda pop`, entrez les synonymes suivants : 

    ```
    coke
    cokes
    coca-cola
    coca-colas
    ```

    Les synonymes peuvent inclure des expressions, des signes de ponctuation, des possessifs et des pluriels. Étant donné que l’entité de type liste est une correspondance exacte de texte (à l’exception de la casse), les synonymes doivent comporter chaque variante. Vous pouvez développer la liste lorsque vous découvrez d’autres variantes dans les journaux des requêtes ou en vérifiant les accès au point de terminaison. 

    Cet article ne compte que quelques synonymes afin que l’exemple reste court. Une application LUIS au niveau de la production comporterait de nombreux synonymes et serait vérifiée et développée régulièrement. 

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png "Capture d’écran d’ajout de synonymes")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png#lightbox)

## <a name="train-the-luis-app"></a>Effectuer l’apprentissage de l’application LUIS
LUIS ne connaît pas les modifications apportées aux intentions et aux entités (modèle) tant que son apprentissage n’a pas été effectué. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Effectuer l’apprentissage**.

    ![Effectuer l’apprentissage de l’application](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. L’apprentissage est terminé lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

    ![Apprentissage réussi](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publier l’application pour obtenir l’URL de point de terminaison
Pour obtenir une prédiction LUIS dans un chatbot ou une autre application, vous devez publier l’application. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Publier**. 

    [![](media/luis-quickstart-intent-and-list-entity/publish.png "Capture d’écran de sélection du bouton Publier")](media/luis-quickstart-intent-and-list-entity/publish.png#lightbox)

2. Sélectionnez l’emplacement Production et le bouton **Publier**. 

    [![](media/luis-quickstart-intent-and-list-entity/publish-to-production.png "Capture d’écran de sélection du bouton Publier vers l’emplacement Production")](media/luis-quickstart-intent-and-list-entity/publish-to-production.png#lightbox)

3. La publication est terminée lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Interroger le point de terminaison avec un autre énoncé
1. Dans la page **Publier**, sélectionnez le lien **Point de terminaison** en bas de la page. Cette action ouvre une autre fenêtre de navigateur avec l’URL de point de terminaison affichée dans la barre d’adresses. 

    [![](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png "Capture d’écran de l’URL de point de terminaison sur la page Publier")](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png#lightbox)

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `2 cokes and 3 waters`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **q**uery. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `OrderDrinks` avec les deux types de boissons `cokes` et `waters`.

```
{
  "query": "2 cokes and 3 waters",
  "topScoringIntent": {
    "intent": "OrderDrinks",
    "score": 0.999998569
  },
  "intents": [
    {
      "intent": "OrderDrinks",
      "score": 0.999998569
    },
    {
      "intent": "None",
      "score": 0.23884207
    }
  ],
  "entities": [
    {
      "entity": "cokes",
      "type": "Drink",
      "startIndex": 2,
      "endIndex": 6,
      "resolution": {
        "values": [
          "Soda pop"
        ]
      }
    },
    {
      "entity": "waters",
      "type": "Drink",
      "startIndex": 14,
      "endIndex": 19,
      "resolution": {
        "values": [
          "h20"
        ]
      }
    }
  ]
}
```

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>Où en est le traitement en langage naturel de l’entité List ? 
Étant donné que l’entité de type liste est une correspondance de texte exacte, elle ne repose pas sur le traitement en langage naturel (ou l’apprentissage automatique). LUIS utilise le traitement en langage naturel (ou l’apprentissage automatique) pour sélectionner l’intention à notation supérieure appropriée. En outre, un énoncé peut être une combinaison de plusieurs entités, voire de plusieurs types d’entités. Chaque énoncé est traité pour toutes les entités de l’application, y compris le traitement en langage naturel (ou par apprentissage automatique) des entités telles que l’entité **Simple**.

## <a name="what-has-this-luis-app-accomplished"></a>Quel est l’accomplissement de cette application LUIS ?
Cette application, comptant seulement deux intentions et une entité de type liste, a identifié une intention de type requête en langage naturel et retourné les données extraites. 

Votre chatbot possède maintenant suffisamment d’informations pour déterminer l’action principale `OrderDrinks` ainsi que les types de boissons commandés à partir de l’entité de type liste de boissons. 

## <a name="where-is-this-luis-data-used"></a>Où ces données LUIS sont-elles utilisées ? 
LUIS en a fini avec cette demande. L’application d’appel, par exemple un chatbot, peut prendre le résultat topScoringIntent et les données de l’entité pour passer à l’étape suivante. LUIS n’effectue pas ce travail de programmation pour le robot ou l’application d’appel. LUIS détermine uniquement l’intention de l’utilisateur. 

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, supprimez l’application LUIS. Pour ce faire, sélectionnez le menu représentant trois points (...) à droite du nom de l’application dans la liste des applications, puis **Supprimer**. Dans la boîte de dialogue contextuelle **Supprimer l’application ?**, sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrir comment ajouter une entité de type expression régulière](luis-quickstart-intents-regex-entity.md)

Ajoutez **l’entité prédéfinie** de type [nombre](luis-how-to-add-entities.md#add-prebuilt-entity) pour extraire le nombre. 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
