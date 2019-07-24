---
title: Entité Simple, liste d’expressions
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez extraire les données de nom de poste de travail issues du machine learning à partir d’un énoncé utilisant l’entité Simple. Pour augmenter la précision de l’extraction, ajoutez une liste d’expressions dont les termes sont spécifiques à l’entité simple.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: a03a1224451411617f38c8cecafbef008aa08916
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276206"
---
# <a name="tutorial-extract-names-with-simple-entity-and-a-phrase-list"></a>Didacticiel : Extraire des noms avec une entité simple et une liste d’expressions

Dans ce tutoriel, vous allez extraire les données de nom de poste de travail issues du machine learning à partir d’un énoncé utilisant l’entité **Simple**. Pour augmenter la précision de l’extraction, ajoutez une liste d’expressions dont les termes sont spécifiques à l’entité simple.

L’entité simple détecte un concept de données unique contenu dans des mots ou des expressions.

**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importer l’exemple d’application
> * Ajouter l’entité simple 
> * Ajouter la liste d’expressions pour améliorer le signal de mots
> * Former 
> * Publish 
> * Obtenir les intentions et les entités à partir du point de terminaison

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]


## <a name="simple-entity"></a>Entité simple

Ce tutoriel ajoute une nouvelle entité simple pour extraire le nom du poste. L’objet de l’entité simple de cette application LUIS est d’apprendre à LUIS ce qu’est un nom de poste et où il se trouve dans un énoncé. La partie de l’énoncé correspondant au nom du poste peut changer d’un énoncé à l’autre en fonction du choix des mots et de la longueur de l’énoncé. LUIS a besoin d’exemples de noms de poste dans toutes les intentions qui utilisent des noms de poste.  

L’entité simple est appropriée pour ce type de données quand :

* Les données sont un concept unique.
* Les données ne sont pas bien formées comme peut l’être une expression régulière.
* Les données ne sont pas courantes, comme une entité prédéfinie de numéro de téléphone ou de données.
* Les données ne correspondent pas exactement à une liste de mots connus, par exemple, une entité de liste.
* Les données ne contiennent pas d’autres éléments de données comme une entité composite ou des rôles contextuels.

Par exemple, examinez les énoncés suivants provenant d’un bot conversationnel :

|Énoncé|Nom de la tâche extractible|
|:--|:--|
|Je souhaite postuler pour le nouveau poste de comptable.|comptabilité|
|Envoyez mon CV pour le poste d’ingénieur.|ingénieur|
|Remplissez l’application pour le poste 123456|123456|

Le nom du travail est difficile à déterminer, car un intitulé peut être un nom, un verbe ou une expression de plusieurs mots. Par exemple :

|Tâches|
|--|
|ingénierie|
|ingénieur informaticien|
|ingénieur informaticien confirmé|
|responsable d’équipe ingénierie |
|contrôleur du trafic aérien|
|conducteur de véhicules motorisés|
|conducteur d’ambulance|
|attendant|
|extrudeuse|
|mécanicien de chantier|

Cette application LUIS comporte des noms de poste dans plusieurs intentions. En étiquetant ces mots dans tous les énoncés des intentions, LUIS apprend ce qu’est un nom de poste et où il se trouve dans les énoncés.

Une fois les entités marquées dans les exemples d’énoncés, il est important d’ajouter une liste d’expressions pour améliorer le signal de l’entité simple. Une liste d’expressions **n’étant pas** utilisée comme une correspondance exacte, elle n’est pas censée contenir chaque valeur possible attendue. 

## <a name="import-example-app"></a>Importer l’exemple d’application

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/intentonly.json) à partir du tutoriel Intentions.

2. Importez le code JSON dans une nouvelle application.

3. À partir de la section **Manage (Gérer)** , sous l’onglet **Versions**, clonez la version et nommez-la `simple`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL.

## <a name="mark-entities-in-example-utterances-of-an-intent"></a>Marquer des entités dans des exemples d’énoncés d’une intention

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Dans la page **Intents** (Intentions), sélectionnez l’intention **ApplyForJob**. 

1. Dans l’énoncé, `I want to apply for the new accounting job`, sélectionnez `accounting`, entrez `Job` dans le premier champ de la liste déroulante, puis sélectionnez **Créer une nouvelle entité** dans le menu contextuel. 

    [![Capture d’écran de LUIS avec l’intention « ApplyForJob »avec les étapes de création de l’entité mises en surbrillance](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Capture d’écran de LUIS avec l’intention « ApplyForJob » avec les étapes de création de l’entité mises en surbrillance")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

1. Dans la fenêtre contextuelle, vérifiez le nom de l’entité et le type et sélectionnez **Terminé**.

    ![Créer une boîte de dialogue contextuelle modale de l’entité simple avec le nom du poste et le type simple](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

1. Dans les énoncés restants, marquez les mots liés aux postes avec l’entité **Job** (Poste) en sélectionnant le mot ou l’expression, puis en sélectionnant **Job** dans le menu contextuel. 

    [![Capture d’écran de LUIS avec l’étiquetage d’entité de travail mis en surbrillance](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Capture d’écran de LUIS avec l’étiquetage d’entité de travail mis en surbrillance")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)


## <a name="add-more-example-utterances-and-mark-entity"></a>Ajouter des exemples d’énoncés et marquer l’entité

Les entités simples ont besoin de nombreux exemples pour avoir un niveau de confiance de prédiction élevé. 
 
1. Ajoutez plusieurs énoncés et marquez les mots ou les expressions de poste en tant qu’entité de **Poste**. 

    |Énoncé|Entité de poste|
    |:--|:--|
    |Je postule pour le poste de Chef de programme en R & D|Chef de programme|
    |Voici ma candidature pour être cuisinier.|cuisinier|
    |Ci-joint mon CV pour être instructeur en camp.|instructeur en camp|
    |Voici mon c.v. pour être assistant administratif.|assistant administratif|
    |Je souhaite postuler au poste de gestion des ventes.|gestion, ventes|
    |Voici mon C.V. pour le nouveau poste de comptable.|comptable|
    |Ci-joint ma candidature pour être serveur.|serveur|
    |J’envoie ma candidature pour couvreur et encadreur.|couvreur, encadreur|
    |Vous trouverez ci-joint mon C.V. de conducteur de bus.|conducteur de bus|
    |Je suis une infirmière diplômée. Voici mon C.V.|infirmière diplômée|
    |Je souhaite envoyer les documents nécessaires pour le poste d’enseignant que j’ai vu dans votre journal.|enseignant|
    |Voici mon c.v. pour le poste de magasinier en fruits et légumes.|magasinier|
    |Je postule pour le travail de pose de carreaux.|vignette|
    |Ci-joint mon C.V. d’architecte paysagiste.|architecte paysagiste|
    |Vous trouverez ci-joint mon curriculum vitae de professeur de biologie.|professeur de biologie|
    |J’aimerais postuler au poste dans la photographie.|photographie|

## <a name="mark-job-entity-in-other-intents"></a>Marquer une entité de poste dans d’autres intentions

1. Dans le menu gauche, sélectionnez **Intents** (Intentions).

1. Sélectionnez **GetJobInformation** dans la liste des intentions. 

1. Étiquetez les postes dans les exemples d’énoncés.

    S’il existe plus d’exemples d’énoncés dans une intention que dans une autre, cette intention a une probabilité plus élevée d’être l’intention prédite la plus élevée. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Entraîner l’application pour que les modifications apportées à l’intention puissent être testées 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publier l’application pour que le modèle entraîné soit interrogeable à partir du point de terminaison

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obtenir l’intention et la prédiction d’entité à partir du point de terminaison 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Here is my c.v. for the engineering job`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner les énoncés `ApplyForJob`.

    ```json
    {
      "query": "Here is my c.v. for the engineering job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.98052007
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.98052007
        },
        {
          "intent": "GetJobInformation",
          "score": 0.03424581
        },
        {
          "intent": "None",
          "score": 0.0015820954
        }
      ],
      "entities": [
        {
          "entity": "engineering",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 34,
          "score": 0.668959737
        }
      ]
    }
    ```
    
    LUIS a trouvé l’intention correcte, **ApplyForJob**, et extrait l’entité correcte, **Job**, ayant pour valeur `engineering`.


## <a name="names-are-tricky"></a>Les noms sont complexes
L’application LUIS a trouvé l’intention correcte avec un niveau de confiance élevé et a extrait le nom du poste, mais les noms sont complexes. Essayez l’énoncé `This is the lead welder paperwork`.  

Dans le JSON suivant, LUIS répond avec l’intention correcte, `ApplyForJob`, mais n’a pas été extrait le nom du poste `lead welder`. 

```json
{
  "query": "This is the lead welder paperwork",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.860295951
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.860295951
    },
    {
      "intent": "GetJobInformation",
      "score": 0.07265678
    },
    {
      "intent": "None",
      "score": 0.00482481951
    }
  ],
  "entities": []
}
```

Un nom pouvant être tout et n’importe quoi, LUIS prédit les entités plus précisément s’il dispose d’une liste d’expressions de mots pour améliorer le signal.

## <a name="to-boost-signal-of-the-job-related-words-add-a-phrase-list-of-job-related-words"></a>Pour améliorer le signal des mots liés aux postes, ajouter une liste d’expressions de mots liés aux postes

Ouvrez le document [jobs-phrase-list.csv](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) à partir du dépôt GitHub Azure-Samples. La liste comprend plus de 1 000 mots et expressions de poste. Consultez la liste pour trouver des mots de poste qui sont significatifs pour vous. Si les mots ou les expressions ne sont pas dans la liste, ajoutez les vôtres.

1. Dans la section **Générer** de l’application LUIS, sélectionnez **Listes d’expressions** dans le menu **Améliorer les performances de l’application**.

1. Sélectionnez **Créer une liste d’expressions**. 

1. Nommez la nouvelle liste d’expressions `JobNames` et copiez la liste depuis jobs-phrase-list.csv vers la zone de texte **Valeurs**.

    [![Capture d’écran de boîte de dialogue contextuelle de création de liste d’expressions](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Capture d’écran de boîte de dialogue contextuelle de création de liste d’expressions")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Pour ajouter d’autres mots à la liste d’expressions, sélectionnez **Recommand** (Nouvelle commande), puis passez en revue les nouvelles **valeurs associées** et ajoutez celles qui sont pertinentes. 

    Laissez la case **Ces valeurs sont interchangeables** cochée, car ces valeurs doivent être traitées comme des synonymes pour les postes. Découvrez-en plus sur les [concepts de liste d’expressions](luis-concept-feature.md#how-to-use-phrase-lists) interchangeables et non interchangeables.

1. Sélectionnez **Terminé** pour activer la liste d’expressions.

    [![Capture d’écran de boîte de dialogue contextuelle de création de liste d’expressions avec des mots dans la zone de valeurs de liste d’expressions](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Capture d’écran de boîte de dialogue contextuelle de création de liste d’expressions avec des mots dans la zone de valeurs de liste d’expressions")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

1. Entraînez et publiez à nouveau l’application pour utiliser la liste d’expressions.

1. Procédez à une nouvelle interrogation du point de terminaison avec le même énoncé : `This is the lead welder paperwork.`

    La réponse JSON inclut l’entité extraite :

    ```json
      {
      "query": "This is the lead welder paperwork.",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.983076453
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.983076453
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0120766377
        },
        {
          "intent": "None",
          "score": 0.00248388131
        }
      ],
      "entities": [
        {
          "entity": "lead welder",
          "type": "Job",
          "startIndex": 12,
          "endIndex": 22,
          "score": 0.8373154
        }
      ]
    }
    ```

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informations connexes

* [Tutoriel Intentions sans entités](luis-quickstart-intents-only.md)
* Informations conceptuelles sur les [entités simples](luis-concept-entity-types.md)
* Informations conceptuelles sur les [listes d’expressions](luis-concept-feature.md)
* [Guide pratique pour entraîner](luis-how-to-train.md)
* [Comment publier](luis-how-to-publish-app.md)
* [Guide pratique pour tester dans le portail LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, l’application Ressources humaines utilise une entité simple issue du machine learning pour rechercher des noms de poste dans des énoncés. Les noms de poste pouvant être un large éventail de mots ou d’expressions, l’application a eu besoin d’une liste d’expressions pour améliorer les mots des noms de poste. 

> [!div class="nextstepaction"]
> [Ajouter une entité keyphrase prédéfinie](luis-quickstart-intent-and-key-phrase.md)
