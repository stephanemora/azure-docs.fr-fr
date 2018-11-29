---
title: 'Tutoriel 4 : Correspondance de texte exacte - Entité de liste LUIS'
titleSuffix: Azure Cognitive Services
description: Obtenez des données qui correspondent à une liste d’éléments prédéfinie. Chaque élément de la liste peut avoir des synonymes qui correspondent également exactement
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: a4e294687b6c3ea2ba6ff8003e7a8f1ac69ea639
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425069"
---
# <a name="tutorial-4-extract-exact-text-matches"></a>Tutoriel 4 : Extraire des correspondances de texte exactes
Ce tutoriel explique comment obtenir des données correspondant à une liste de termes prédéfinie. Chaque élément de la liste peut inclure une liste de synonymes. Pour l’application de ressources humaines, un employé peut être identifié avec plusieurs éléments d’information essentiels tels que le nom, l’adresse e-mail, le numéro de téléphone et le numéro d’identification du contribuable (USA). 

L’application Ressources humaines doit déterminer l’employé qui passe d’un bâtiment à un autre. Dans le cadre d’un énoncé sur le déplacement d’un employé, LUIS détermine l’intention et extrait l’employé afin que l’application cliente puisse créer un ordre standard pour déplacer l’employé.

Cette application utilise une entité de liste pour extraire l’employé. Les employés peuvent être cités par leur nom, extension téléphonique au sein de l’entreprise, numéro de téléphone mobile, adresse e-mail ou numéro de sécurité sociale (USA). 

Une entité de type liste est un choix approprié pour ce type de données lorsque :

* Les valeurs des données sont un ensemble connu.
* L’ensemble ne dépasse pas les [limites](luis-boundaries.md) maximum de LUIS pour ce type d’entité.
* Le texte de l’énoncé est une correspondance exacte avec un synonyme ou le nom canonique. 

**Ce tutoriel vous montre comment effectuer les opérations suivantes :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Utiliser l’application de tutoriel existante
> * Ajouter l’intention MoveEmployee
> * Ajouter une entité de liste 
> * Former 
> * Publish
> * Obtenir les intentions et les entités à partir du point de terminaison

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Utiliser l’application existante
Continuez avec l’application créée dans le dernier tutoriel, nommée **HumanResources**. 

Si vous n’avez pas l’application HumanResources du tutoriel précédent, effectuez les étapes suivantes :

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-regex-HumanResources.json).

2. Importez le code JSON dans une nouvelle application.

3. À partir de la section **Manage (Gérer)**, sous l’onglet **Versions**, clonez la version et nommez-la `list`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL. 


## <a name="moveemployee-intent"></a>Intention MoveEmployee

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

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

    N’oubliez pas que number et datetimeV2 ont été ajoutés dans le tutoriel précédent et sont automatiquement étiquetés quand ils sont trouvés dans des exemples d’énoncés.

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="employee-list-entity"></a>Entité de liste d’employés
Maintenant que l’intention **MoveEmployee** dispose d’exemples d’énoncés, LUIS doit comprendre ce qu’est un employé. 

Le nom _canonique_ principal pour chaque élément est le numéro d’employé. Pour ce domaine, voici des exemples de synonymes de chaque nom canonique : 

|Objectif du synonyme|Valeur du synonyme|
|--|--|
|Nom|John W. Smith|
|Adresse de messagerie|john.w.smith@mycompany.com|
|Extension de téléphone|x12345|
|Numéro de téléphone mobile personnel|425-555-1212|
|Numéro de sécurité sociale (USA)|123-45-6789|


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

## <a name="train"></a>Former

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publish

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obtenir l’intention et les entités à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

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

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez créé une intention, ajouté des exemples d’énoncés, puis créé une entité de liste pour extraire des correspondances de texte exactes à partir des énoncés. Une fois l’application entraînée et publiée, une requête au point de terminaison a identifié l’intention et retourné les données extraites.

> [!div class="nextstepaction"]
> [Ajouter une entité hiérarchique à l’application](luis-quickstart-intent-and-hier-entity.md)

