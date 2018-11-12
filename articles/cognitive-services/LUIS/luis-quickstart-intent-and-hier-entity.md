---
title: 'Tutoriel 5 : Relations parent/enfant - Entité hiérarchique LUIS pour les données issues d’un apprentissage contextuel'
titleSuffix: Azure Cognitive Services
description: Recherchez des ensembles de données connexes en fonction du contexte. Par exemple, les emplacements d’origine et de destination d’un déménagement physique depuis un bâtiment et un bureau vers un autre bâtiment et un autre bureau sont liés.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 3903f247df0bc9dc4bc27d61b195492c585d7634
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282269"
---
# <a name="tutorial-5-extract-contextually-related-data"></a>Tutoriel 5 : Extraire des données relatives au contexte
Dans ce tutoriel, vous allez rechercher des ensembles de données connexes en fonction du contexte. Par exemple, les emplacements d’origine et de destination d’un déménagement physique depuis un bâtiment et un bureau vers un autre bâtiment et un autre bureau sont liés. Pour générer un ordre de travail, les deux ensembles de données peuvent être nécessaires et ils sont liés entre eux.  

Cette application détermine lorsqu’un employé doit déménager de son emplacement d’origine (bâtiment et bureau) à un emplacement de destination (bâtiment et bureau). Elle utilise l’entité hiérarchique pour détermine les emplacements au sein de l’énoncé. L’objectif de l’entité **hiérarchique** est de rechercher les données associées dans l’énoncé en fonction du contexte. 

L’entité hiérarchique est adaptée à ce type de données, car les deux ensembles de données :

* Sont des entités simples.
* Sont liés l’un à l’autre dans le contexte de l’énoncé.
* Utilisent des formulations spécifiques pour indiquer chaque emplacement. Exemples : de/à, quitte/arrive à, loin de/vers...
* Les deux emplacements se trouvent fréquemment dans le même énoncé. 
* Doivent être regroupées et traitées par l’application cliente en tant qu’unité d’informations.

**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Utiliser l’application de tutoriel existante
> * Ajouter une intention 
> * Ajouter une entité hiérarchique avec des enfants d’origine et de destination
> * Former
> * Publish
> * Obtenir les intentions et les entités à partir du point de terminaison

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Utiliser l’application existante
Continuez avec l’application créée dans le dernier tutoriel, nommée **HumanResources**. 

Si vous n’avez pas l’application HumanResources du tutoriel précédent, effectuez les étapes suivantes :

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-list-HumanResources.json).

2. Importez le code JSON dans une nouvelle application.

3. À partir de la section **Manage (Gérer)**, sous l’onglet **Versions**, clonez la version et nommez-la `hier`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL. 

## <a name="remove-prebuilt-number-entity-from-app"></a>Supprimer l’entité de nombre prédéfinie de l’application
Afin d’afficher l’énoncé complet et de marquer les enfants hiérarchiques, supprimez temporairement l’entité de nombre prédéfinie.

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Dans le menu gauche, sélectionnez **Entités**.

3. Cochez la case à droite de l’entité nombre de la liste. Sélectionnez **Supprimer**. 

## <a name="add-utterances-to-moveemployee-intent"></a>Ajoutez des énoncés à l’intention de MoveEmployee

1. Dans le menu gauche, sélectionnez **Intents** (Intentions).

2. Sélectionnez **MoveEmployee** dans la liste des intentions.

3. Ajoutez les énoncés exemples suivants :

    |Exemples d’énoncés|
    |--|
    |Déménager John W. Smith **vers** a-2345|
    |Rediriger Jill Jones **vers** b-3499|
    |Organiser le déménagement de x23456 **de** hh-2345 **vers** e-0234|
    |Commencer les formalités pour le **départ** de x12345 de a-3459 **vers** f-34567|
    |Faire partir 425-555-0000 **de** g-2323 **vers** hh-2345|

    [ ![Capture d’écran de LUIS avec les nouveaux énoncés dans l’intention MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

    Dans le tutoriel [liste d’entités](luis-quickstart-intent-and-list-entity.md), un employé est désigné par son nom, adresse e-mail, numéro de poste, numéro de téléphone mobile ou numéro de sécurité sociale (USA). Ces numéros relatifs à l’employé sont utilisés dans les énoncés. Les énoncés exemples précédents incluent différentes façons de noter les emplacements d’origine et de destination, marqués en gras. Quelques énoncés n’ont que les destinations, à dessein. Cela aide LUIS à comprendre comment ces emplacements sont placés dans l’énoncé lorsque l’origine n’est pas spécifiée.     

    [!INCLUDE[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="create-a-location-entity"></a>Créer une entité d’emplacement
LUIS a besoin de comprendre ce qu’est un emplacement en étiquetant l’origine et la destination dans les énoncés. Si vous devez voir l’énoncé dans l’affichage du jeton (brut), sélectionnez le bouton bascule dans la barre au-dessus des énoncés dénommé **Entities View** (affichage d’entités). Après avoir activé le bouton bascule , la commande s’appelle **Tokens View** (Vue des jetons).

Prenez l’énoncé suivant :

```JSON
mv Jill Jones from a-2349 to b-1298
```

Deux emplacements sont indiqués dans l’énoncé : `a-2349` et `b-1298`. Part du principe que la lettre correspond au nom du bâtiment et le numéro au bureau dans ce bâtiment. Il est logique qu’ils soient groupés en tant qu’enfants d’une même entité hiérarchique, `Locations` car les deux ensembles de données doivent être extraits de l’énoncé pour que soit traitée la demande dans l’application cliente et sont liés l’un à l’autre. 
 
Si un seul enfant (origine ou destination) d’une entité hiérarchique est présent, il est tout de même extrait. Il n’est pas nécessaire de trouver tous les enfants pour en extraire un seul ou quelques-uns. 

1. Dans l’énoncé `Displace 425-555-0000 away from g-2323 toward hh-2345`, sélectionnez le mot `g-2323`. Un menu déroulant apparaît avec une zone de texte à son sommet. Entrez le nom de l’entité `Locations` dans la zone de texte, puis sélectionnez **Créer une entité** dans le menu déroulant. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png "Capture d’écran de création d’une nouvelle entité sur la page d’intention")](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png#lightbox)

2. Dans la fenêtre contextuelle, sélectionnez le type d’entité **hiérarchique** avec `Origin` et `Destination` comme entités enfants. Sélectionnez **Terminé**.

    ![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Capture d’écran de la boîte de dialogue contextuelle de création d’une entité pour une nouvelle entité Emplacement")

3. L’étiquette de `g-2323` est marquée comme `Locations` car LUIS ne sait pas si le terme désigne l’origine ou la destination ou aucun des deux. Sélectionnez `g-2323`, puis **Locations** (Emplacement), puis suivez le menu à droite et cliquez sur `Origin`.

    [![](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png "Capture d’écran de la boîte de dialogue contextuelle d’étiquetage d’une entité pour modifier l’enfant d’entité de l’emplacement")](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png#lightbox)

5. Étiquetez les autres emplacements de tous les autres énoncés en sélectionnant le bâtiment et le bureau de l’énoncé, puis Locations (Emplacements), puis le menu à droite pour choisir `Origin` ou `Destination`. Lorsque tous les emplacements sont étiquetés, les énoncés dans **Tokens View** (Vue du jeton) commencent à ressembler à un modèle. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png "Capture d’écran de l’entité Locations (emplacements) étiquetée dans les énoncés")](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png#lightbox)

## <a name="add-prebuilt-number-entity-to-app"></a>Ajouter l’entité de nombre prédéfinie dans l’application
Ajoutez l’entité de nombre prédéfinie dans l’application.

1. Dans le menu de navigation de gauche, sélectionnez **Entités**.

2. Sélectionnez le bouton **Ajouter une entité prédéfinie**.

3. Sélectionnez le **numéro** de la liste des entités prédéfinies puis sélectionnez **Done** (Terminé).

    ![Capture d’écran de la sélection du nombre dans la boîte de dialogue des entités prédéfinies](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publier l’application pour obtenir l’URL de point de terminaison

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Interroger le point de terminaison avec un autre énoncé

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


2. Allez à la fin de l’URL dans la barre d’adresses, puis entrez `Please relocation jill-jones@mycompany.com from x-2345 to g-23456`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner l’intention `MoveEmployee` avec l’entité hiérarchique extraite.

    ```JSON
    {
      "query": "Please relocation jill-jones@mycompany.com from x-2345 to g-23456",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9966052
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9966052
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0325253047
        },
        {
          "intent": "FindForm",
          "score": 0.006137873
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00462633232
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00415637763
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00382325822
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00249120337
        },
        {
          "intent": "None",
          "score": 0.00130756292
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00119622645
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.26910036E-05
        }
      ],
      "entities": [
        {
          "entity": "jill - jones @ mycompany . com",
          "type": "Employee",
          "startIndex": 18,
          "endIndex": 41,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "x - 2345",
          "type": "Locations::Origin",
          "startIndex": 48,
          "endIndex": 53,
          "score": 0.8520272
        },
        {
          "entity": "g - 23456",
          "type": "Locations::Destination",
          "startIndex": 58,
          "endIndex": 64,
          "score": 0.974032
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 49,
          "endIndex": 53,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "-23456",
          "type": "builtin.number",
          "startIndex": 59,
          "endIndex": 64,
          "resolution": {
            "value": "-23456"
          }
        }
      ]
    }
    ```
    
    L’intention correcte est prédite et le tableau d’entités a les valeurs d’origine et de destination dans la propriété **entity** correspondante.
    

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>Auriez-vous pu utiliser une expression régulière pour chaque emplacement ?
Oui, créez l’entité d’expression régulière avec les rôles d’origine et de destination et utilisez-les dans un modèle.

Dans cet exemple, les emplacements tels que `a-1234`, suivent un format spécifiques : une ou deux lettres, suivies d’un tiret puis d’une série de 4 à 5 chiffres. Ces données peuvent être décrites comme une entité à expression régulière avec un rôle pour chaque emplacement. Les rôles ne sont disponibles que pour les modèles. Vous pouvez créer des modèles basés sur ces énoncés, puis créer une expression régulière pour le format de l’emplacement et l’ajouter aux modèles. 

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="hierarchical-entities-versus-roles"></a>Rôles et entités hiérarchiques

Pour plus d’informations, consultez [Rôles et entités hiérarchiques](luis-concept-roles.md#roles-versus-hierarchical-entities).

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez créé une intention et ajouté des exemples d’énoncés pour les données des emplacements d’origine et de destination issues d’un apprentissage contextuel. Une fois l’application entraînée et publiée, une application cliente peut utiliser ces informations pour créer un ticket de déménagement avec les informations appropriées.

> [!div class="nextstepaction"] 
> [Découvrez comment ajouter une entité composite](luis-tutorial-composite-entity.md) 