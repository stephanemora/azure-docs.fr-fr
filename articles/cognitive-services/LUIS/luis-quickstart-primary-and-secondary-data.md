---
title: 'Tutoriel 7 : Entité simple avec une liste d’expressions dans LUIS'
titleSuffix: Azure Cognitive Services
description: Extraire d’un énoncé des données issues du machine learning
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: e0cdda629ddded121a424af61377c04ee8d958d3
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867542"
---
# <a name="tutorial-7-extract-names-with-simple-entity-and-phrase-list"></a>Tutoriel 7 : Extraire des noms avec une entité simple et une liste d’expressions

Dans ce tutoriel, vous allez extraire les données de nom de poste de travail issues du machine learning à partir d’un énoncé utilisant l’entité **Simple**. Pour augmenter la précision de l’extraction, ajoutez une liste d’expressions dont les termes sont spécifiques à l’entité simple.

Ce tutoriel ajoute une nouvelle entité simple pour extraire le nom du poste. L’objet de l’entité simple de cette application LUIS est d’apprendre à LUIS ce qu’est un nom de poste et où il se trouve dans un énoncé. La partie de l’énoncé correspondant au nom du poste peut changer d’un énoncé à l’autre en fonction du choix des mots et de la longueur de l’énoncé. LUIS a besoin d’exemples de noms de poste dans toutes les intentions qui utilisent des noms de poste.  

L’entité simple est appropriée pour ce type de données quand :

* Les données sont un concept unique.
* Les données ne sont pas bien formées comme peut l’être une expression régulière.
* Les données ne sont pas courantes, comme une entité prédéfinie de numéro de téléphone ou de données.
* Les données ne correspondent pas exactement à une liste de mots connus, par exemple, une entité de liste.
* Les données ne contiennent pas d’autres éléments de données comme une entité composite ou une entité hiérarchique.

**Ce tutoriel vous montre comment effectuer les opérations suivantes :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Utiliser l’application de tutoriel existante
> * Ajouter une entité simple pour extraire des travaux depuis l’application
> * Ajouter la liste d’expressions pour améliorer le signal de mots de travail
> * Former 
> * Publish 
> * Obtenir les intentions et les entités à partir du point de terminaison

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Utiliser l’application existante

Continuez avec l’application créée dans le dernier tutoriel, nommée **HumanResources**. 

Si vous n’avez pas l’application HumanResources du tutoriel précédent, effectuez les étapes suivantes :

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-composite-HumanResources.json).

2. Importez le code JSON dans une nouvelle application.

3. À partir de la section **Manage (Gérer)**, sous l’onglet **Versions**, clonez la version et nommez-la `simple`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL.

## <a name="simple-entity"></a>Entité simple
L’entité simple détecte un concept de données unique contenu dans des mots ou des expressions.

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

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Dans la page **Intents** (Intentions), sélectionnez l’intention **ApplyForJob**. 

3. Dans l’énoncé, `I want to apply for the new accounting job`, sélectionnez `accounting`, entrez `Job` dans le premier champ de la liste déroulante, puis sélectionnez **Créer une nouvelle entité** dans le menu contextuel. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Capture d’écran de LUIS avec intention « ApplyForJob » et étapes de la création d’entité mises en surbrillance")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. Dans la fenêtre contextuelle, vérifiez le nom de l’entité et le type et sélectionnez **Terminé**.

    ![Créer une boîte de dialogue contextuelle modale de l’entité simple avec le nom du poste et le type simple](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. Dans l’énoncé, `Submit resume for engineering position`, étiquetez le mot `engineering` comme entité Poste. Sélectionnez le mot `engineering`, puis sélectionnez **Poste** dans le menu contextuel. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Capture d’écran de LUIS, avec étiquetage des entités de poste mis en surbrillance")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)

    Tous les énoncés sont étiquetés mais cinq énoncés ne sont pas suffisants pour apprendre à LUIS ce que sont les mots et expressions liés aux postes. Les postes qui utilisent des nombres ne nécessitent pas plus d’exemples car ils utilisent une entité d’expression régulière. Les tâches qui sont des mots ou des expressions nécessitent au moins 15 exemples supplémentaires. 

6. Ajoutez plusieurs énoncés et marquez les mots ou les expressions de poste en tant qu’entité de **Poste**. Les types de postes sont généraux entre les différents services d’emploi. Si vous souhaitiez des tâches liées à un secteur d’activité spécifique, les mots de postes doivent le refléter. 

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
    |J’aimerais postuler au poste dans la photographie.|photographie|git 

## <a name="label-entity-in-example-utterances"></a>Étiqueter l’entité dans les exemples d’énoncés

L’étiquetage, ou _marquage_, de l’entité montre à LUIS où celle-ci se trouve dans les exemples d’énoncés.

1. Dans le menu gauche, sélectionnez **Intents** (Intentions).

2. Sélectionnez **GetJobInformation** dans la liste des intentions. 

3. Étiquetez les postes dans les énoncés exemple suivants :

    |Énoncé|Entité de poste|
    |:--|:--|
    |Existe-t-il des postes à pourvoir en bases de données ?|bases de données|
    |Recherche un nouveau poste avec des responsabilités en comptabilité|comptabilité|
    |Quels postes sont disponibles pour des ingénieurs confirmés ?|ingénieur confirmé|

    Il existe d’autres énoncés exemple mais ils ne contiennent pas de mots de poste.

## <a name="train"></a>Former

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publish

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obtenir l’intention et les entités à partir du point de terminaison 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Here is my c.v. for the programmer job`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner les énoncés `ApplyForJob`.

    ```JSON
    {
      "query": "Here is my c.v. for the programmer job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9826467
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9826467
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0218927357
        },
        {
          "intent": "MoveEmployee",
          "score": 0.007849265
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00349470088
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00348804821
        },
        {
          "intent": "None",
          "score": 0.00319909188
        },
        {
          "intent": "FindForm",
          "score": 0.00222647213
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00211193133
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00172086991
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00138010911
        }
      ],
      "entities": [
        {
          "entity": "programmer",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 33,
          "score": 0.5230502
        }
      ]
    }
    ```
    
    LUIS a trouvé l’intention correcte, **ApplyForJob**, et extrait l’entité correcte, **Job**, ayant pour valeur `programmer`.


## <a name="names-are-tricky"></a>Les noms sont complexes
L’application LUIS a trouvé l’intention correcte avec un niveau de confiance élevé et a extrait le nom du poste, mais les noms sont complexes. Essayez l’énoncé `This is the lead welder paperwork`.  

Dans le JSON suivant, LUIS répond avec l’intention correcte, `ApplyForJob`, mais n’a pas été extrait le nom du poste `lead welder`. 

```JSON
{
  "query": "This is the lead welder paperwork.",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.468558252
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.468558252
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0102701457
    },
    {
      "intent": "MoveEmployee",
      "score": 0.009442534
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00639619166
    },
    {
      "intent": "None",
      "score": 0.005859333
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.005087704
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00315379258
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00259344373
    },
    {
      "intent": "FindForm",
      "score": 0.00193389168
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.000420796918
    }
  ],
  "entities": []
}
```

Un nom pouvant être tout et n’importe quoi, LUIS prédit les entités plus précisément s’il dispose d’une liste d’expressions de mots pour améliorer le signal.

## <a name="to-boost-signal-add-phrase-list"></a>Ajouter une liste d’expressions pour améliorer le signal

Ouvrez le document [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) à partir du référentiel Github LUIS-Samples. La liste comprend plus d’un millier de mots et d’expressions de poste. Consultez la liste pour trouver des mots de poste qui sont significatifs pour vous. Si les mots ou les expressions ne sont pas dans la liste, ajoutez les vôtres.

1. Dans la section **Générer** de l’application LUIS, sélectionnez **Listes d’expressions** dans le menu **Améliorer les performances de l’application**.

2. Sélectionnez **Créer une liste d’expressions**. 

3. Nommez la nouvelle liste d’expressions `Job` et copiez la liste depuis jobs-phrase-list.csv vers la zone de texte **Valeurs**. Sélectionnez Enter (Entrer). 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Capture d’écran avec fenêtre contextuelle de création d’une nouvelle liste d’expressions")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Si vous voulez ajouter d’autres mots à la liste d’expressions, passez en revue les **valeurs associées** et ajoutez celles qui sont pertinentes. 

4. Sélectionnez **Enregistrer** pour activer la liste d’expressions.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Capture d’écran de boîte de dialogue contextuelle de création de nouvelle liste d’expressions avec mots dans la zone de valeurs de la liste d’expressions")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. [Entraînez](#train) et [publiez](#publish) à nouveau l’application pour utiliser la liste d’expressions.

6. Procédez à une nouvelle interrogation du point de terminaison avec le même énoncé : `This is the lead welder paperwork.`

    La réponse JSON inclut l’entité extraite :

    ```JSON
    {
        "query": "This is the lead welder paperwork.",
        "topScoringIntent": {
            "intent": "ApplyForJob",
            "score": 0.920025647
        },
        "intents": [
            {
            "intent": "ApplyForJob",
            "score": 0.920025647
            },
            {
            "intent": "GetJobInformation",
            "score": 0.003800706
            },
            {
            "intent": "Utilities.StartOver",
            "score": 0.00299335527
            },
            {
            "intent": "MoveEmployee",
            "score": 0.0027167045
            },
            {
            "intent": "None",
            "score": 0.00259556063
            },
            {
            "intent": "FindForm",
            "score": 0.00224019377
            },
            {
            "intent": "Utilities.Stop",
            "score": 0.00200693542
            },
            {
            "intent": "Utilities.Cancel",
            "score": 0.00195913855
            },
            {
            "intent": "Utilities.Help",
            "score": 0.00162656687
            },
            {
            "intent": "Utilities.Confirm",
            "score": 0.0002851904
            }
        ],
        "entities": [
            {
            "entity": "lead welder",
            "type": "Job",
            "startIndex": 12,
            "endIndex": 22,
            "score": 0.8295959
            }
        ]
    }
    ```

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, l’application Ressources humaines utilise une entité simple issue du machine learning pour rechercher des noms de poste dans des énoncés. Les noms de poste pouvant être un large éventail de mots ou d’expressions, l’application a eu besoin d’une liste d’expressions pour améliorer les mots des noms de poste. 

> [!div class="nextstepaction"]
> [Ajouter une entité keyphrase prédéfinie](luis-quickstart-intent-and-key-phrase.md)