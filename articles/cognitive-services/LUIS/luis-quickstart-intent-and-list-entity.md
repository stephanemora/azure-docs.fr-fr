---
title: Didacticiel de création d’une application LUIS pour obtenir des données correspondant à une liste - Azure | Microsoft Docs
description: Dans ce didacticiel, vous allez découvrir comment créer une application LUIS simple utilisant des entités de type liste et des intentions pour extraire les données dans ce guide de démarrage rapide.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: afad3fe725fddd0748cc206517a7274815cf1653
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495262"
---
# <a name="tutorial-4-add-list-entity"></a>Didacticiel : 4. Ajouter une entité de liste
Dans ce didacticiel, créez une application qui montre comment obtenir des données correspondant à une liste prédéfinie. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendre les entités de type liste 
> * Créer une nouvelle application LUIS pour le domaine des ressources humaines (RH) avec l’intention MoveEmployee
> * Ajouter une entité de type liste pour extraire l’employé d’un énoncé
> * Effectuer l’apprentissage de l’application et la publier
> * Interroger un point de terminaison de l’application pour voir la réponse JSON de LUIS

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Avant de commencer
Si vous ne disposez pas de l’application Ressources humaines du didacticiel [entité regex](luis-quickstart-intents-regex-entity.md), [importez](luis-how-to-start-new-app.md#import-new-app) le JSON dans une application du site Web [LUIS](luis-reference-regions.md#luis-website). L’application à importer se trouve dans le référentiel Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-regex-HumanResources.json).

Si vous souhaitez conserver l’application Ressources humaines d’origine, clonez la version sur la page [Paramètres](luis-how-to-manage-versions.md#clone-a-version), et nommez-la `list`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. 

## <a name="purpose-of-the-list-entity"></a>Objet de l’entité de type liste
Cette application prévoit des énoncés sur le déplacement d’un employé d’un bâtiment à un autre bâtiment. Cette application utilise une entité de liste pour extraire un employé. Les employés peuvent être cités par leur nom, numéro de téléphone, courrier électronique ou numéro de sécurité sociale (USA). 

Une entité de liste peut contenir de nombreux éléments, avec des synonymes pour chaque élément. Pour une PME, l’entité de liste est utilisée pour extraire les informations de l’employé. 

Le nom canonique pour chaque élément est le numéro d’employé. Pour ce domaine, des exemples de synonymes sont : 

|Objectif du synonyme|Valeur du synonyme|
|--|--|
|Nom|John W. Smith|
|Adresse de messagerie|john.w.smith@mycompany.com|
|Extension de téléphone|x12345|
|Numéro de téléphone mobile personnel|425-555-1212|
|Numéro de sécurité sociale (USA)|123-45-6789|

Une entité de type liste est un choix approprié pour ce type de données lorsque :

* Les valeurs des données sont un ensemble connu.
* L’ensemble ne dépasse pas les [limites](luis-boundaries.md) maximum de LUIS pour ce type d’entité.
* Le texte de l’énoncé est une correspondance exacte avec un synonyme. 

LUIS extrait l’employé de telle sorte qu’une commande standard pour migrer l’employé peut être créée par l’application cliente.
<!--
## Example utterances
Simple example utterances for a `MoveEmployee` inent:

```
move John W. Smith from B-1234 to H-4452
mv john.w.smith@mycompany from office b-1234 to office h-4452

```
-->

## <a name="add-moveemployee-intent"></a>Ajouter l’intention MoveEmployee

1. Assurez-vous que votre application Ressources humaines figure dans la section **Générer** de LUIS. Vous pouvez modifier cette section en sélectionnant **Générer** dans la barre de menu en haut à droite. 

2. Sélectionnez **Créer une intention**. 

3. Entrez `MoveEmployee` dans la boîte de dialogue contextuelle, puis sélectionnez **Terminé**. 

    ![Capture d’écran de la boîte de dialogue contextuelle Créer une nouvelle intention](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Ajoutez des exemples d’énoncés à l’intention.

    |Exemples d’énoncés|
    |--|
    |déménager John W. Smith de B-1234 à H-4452|
    |mv john.w.smith@mycompany.com du bureau b-1234 au bureau h-4452|
    |déplacer x12345 vers h-1234 demain|
    |placer 425-555-1212 à HH-2345|
    |bouger 123-45-6789 de A-4321 à J-23456|
    |mv Jill Jones de D-2345 à J-23456|
    |basculer jill-jones@mycompany.com vers M-12345|
    |x23456 vers M-12345|
    |425-555-0000 vers h-4452|
    |234-56-7891 vers hh-2345|

    [ ![Capture d’écran de la page d’intentions avec de nouveaux énoncés mis en surbrillance](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png) ](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png#lightbox)

## <a name="create-an-employee-list-entity"></a>Créer une entité de liste d’employés
Maintenant que l’intention **MoveEmployee** dispose d’énoncés, LUIS doit comprendre ce qu’est un employé. 

1. Dans le panneau gauche, sélectionnez **Entités**.

2. Sélectionnez **Créer une entité**.

3. Dans la fenêtre de dialogue contextuelle des entités, saisir `Employee` dans le nom d’entité, et **List** pour le type d’entité. Sélectionnez **Terminé**.  

    [![](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png "Capture d’écran de la fenêtre contextuelle Creating new entity")](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png#lightbox)

4. Sur la page Employee entity, saisissez la valeur `Employee-24612`.

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "Capture d’écran de saisie de valeur")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

5. Pour Synonyms (synonymes), ajoutez les valeurs suivantes :

    |Objectif du synonyme|Valeur du synonyme|
    |--|--|
    |Nom|John W. Smith|
    |Adresse de messagerie|john.w.smith@mycompany.com|
    |Extension de téléphone|x12345|
    |Numéro de téléphone mobile personnel|425-555-1212|
    |Numéro de sécurité sociale (USA)|123-45-6789|

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "Capture d’écran de saisie des synonymes")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

6. Saisissez la valeur `Employee-45612`.

7. Pour Synonyms (synonymes), ajoutez les valeurs suivantes :

    |Objectif du synonyme|Valeur du synonyme|
    |--|--|
    |Nom|Jill Jones|
    |Adresse de messagerie|jill-jones@mycompany.com|
    |Extension de téléphone|x23456|
    |Numéro de téléphone mobile personnel|425-555-0000|
    |Numéro de sécurité sociale (USA)|234-56-7891|

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publier l’application pour obtenir l’URL de point de terminaison

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Interroger le point de terminaison avec un autre énoncé

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `shift 123-45-6789 from Z-1242 to T-54672`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **q**uery. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `MoveEmployee` avec `Employee` extrait.

  ```JSON
  {
    "query": "shift 123-45-6789 from Z-1242 to T-54672",
    "topScoringIntent": {
      "intent": "MoveEmployee",
      "score": 0.9882801
    },
    "intents": [
      {
        "intent": "MoveEmployee",
        "score": 0.9882801
      },
      {
        "intent": "FindForm",
        "score": 0.016044287
      },
      {
        "intent": "GetJobInformation",
        "score": 0.007611245
      },
      {
        "intent": "ApplyForJob",
        "score": 0.007063288
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00684710965
      },
      {
        "intent": "None",
        "score": 0.00304174074
      },
      {
        "intent": "Utilities.Help",
        "score": 0.002981
      },
      {
        "intent": "Utilities.Confirm",
        "score": 0.00212222221
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.00191026414
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0007461446
      }
    ],
    "entities": [
      {
        "entity": "123 - 45 - 6789",
        "type": "Employee",
        "startIndex": 6,
        "endIndex": 16,
        "resolution": {
          "values": [
            "Employee-24612"
          ]
        }
      },
      {
        "entity": "123",
        "type": "builtin.number",
        "startIndex": 6,
        "endIndex": 8,
        "resolution": {
          "value": "123"
        }
      },
      {
        "entity": "45",
        "type": "builtin.number",
        "startIndex": 10,
        "endIndex": 11,
        "resolution": {
          "value": "45"
        }
      },
      {
        "entity": "6789",
        "type": "builtin.number",
        "startIndex": 13,
        "endIndex": 16,
        "resolution": {
          "value": "6789"
        }
      },
      {
        "entity": "-1242",
        "type": "builtin.number",
        "startIndex": 24,
        "endIndex": 28,
        "resolution": {
          "value": "-1242"
        }
      },
      {
        "entity": "-54672",
        "type": "builtin.number",
        "startIndex": 34,
        "endIndex": 39,
        "resolution": {
          "value": "-54672"
        }
      }
    ]
  }
  ```

  L’employé a été trouvé et retourné en tant que type `Employee` avec une valeur de résolution de `Employee-24612`.

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>Où en est le traitement en langage naturel de l’entité List ? 
Étant donné que l’entité de type liste est une correspondance de texte exacte, elle ne repose pas sur le traitement en langage naturel (ou l’apprentissage automatique). LUIS utilise le traitement en langage naturel (ou l’apprentissage automatique) pour sélectionner l’intention à notation supérieure appropriée. En outre, un énoncé peut être une combinaison de plusieurs entités, voire de plusieurs types d’entités. Chaque énoncé est traité pour toutes les entités dans l’application, y compris les entités de traitements en langage naturel (ou issues de l’apprentissage automatique).

## <a name="what-has-this-luis-app-accomplished"></a>Quel est l’accomplissement de cette application LUIS ?
Cette application, dotée d’une entité de liste, a extrait l’employé correct. 

Votre chatbot a maintenant suffisamment d’informations pour déterminer l’action principale, `MoveEmployee`, et l’employé qui doit être déplacé. 

## <a name="where-is-this-luis-data-used"></a>Où ces données LUIS sont-elles utilisées ? 
LUIS en a fini avec cette requête. L’application d’appel, par exemple un chatbot, peut prendre le résultat topScoringIntent et les données de l’entité pour passer à l’étape suivante. LUIS n’effectue pas ce travail de programmation pour le robot ou l’application d’appel. LUIS détermine uniquement l’intention de l’utilisateur. 

## <a name="clean-up-resources"></a>Supprimer les ressources

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter une entité hiérarchique à l’application](luis-quickstart-intent-and-hier-entity.md)

