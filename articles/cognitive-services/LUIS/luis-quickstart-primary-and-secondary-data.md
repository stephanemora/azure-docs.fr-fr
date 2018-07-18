---
title: Didacticiel de création d’une application LUIS pour extraire des données - Azure | Microsoft Docs
description: Dans ce tutoriel, vous allez découvrir comment créer une application LUIS simple utilisant une entité simple et des intentions pour extraire les données issues de l’apprentissage automatique.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: v-geberr
ms.openlocfilehash: e6ab9d1db0144ffa68fe9dc3381ba31d57aa0cae
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130889"
---
# <a name="tutorial-6-add-simple-entity-and-phrase-list"></a>Tutoriel : 6. Ajouter une entité simple et une liste d’expressions
Dans ce tutoriel, vous allez créer une application qui montre comment extraire les données issues de l’apprentissage automatique à partir d’un énoncé utilisant l’entité **Simple**.

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendre les entités simples 
> * Créer une nouvelle application LUIS pour le domaine des ressources humaines (RH) 
> * Ajouter une entité simple pour extraire des travaux depuis l’application
> * Entraîner et publier l’application
> * Interroger un point de terminaison de l’application pour voir la réponse JSON de LUIS
> * Ajouter la liste d’expressions pour améliorer le signal de mots de travail
> * Entraîner, publier l’application et réinterroger le point de terminaison

Pour cet article, vous devez disposer d’un compte [LUIS](luis-reference-regions.md#luis-website) gratuit afin de créer votre application LUIS.

## <a name="before-you-begin"></a>Avant de commencer
Si vous ne disposez pas de l’application Ressources humaines du tutoriel [entité hiérarchique](luis-quickstart-intent-and-hier-entity.md), [importez](create-new-app.md#import-new-app) le JSON dans une application du site Web [LUIS](luis-reference-regions.md#luis-website). L’application à importer se trouve dans le référentiel Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json).

Si vous souhaitez conserver l’application Ressources humaines d’origine, clonez la version sur la page [Paramètres](luis-how-to-manage-versions.md#clone-a-version), et nommez-la `simple`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine.  

## <a name="purpose-of-the-app"></a>Objet de l’application
Cette application montre comment extraire des données d’un énoncé. Par exemple, examinez les énoncés suivants provenant d’un chatbot :

|Énoncé|Nom de la tâche extractible|
|:--|:--|
|Je souhaite postuler pour le nouveau poste de comptable.|comptable|
|Veuillez envoyer mon CV pour le poste d’ingénieur.|ingénieur|
|Remplissez l’application pour le poste 123456|123456|

Ce tutoriel ajoute une nouvelle entité pour extraire le nom du poste. 

## <a name="purpose-of-the-simple-entity"></a>Objet de l’entité simple
L’objet de l’entité simple de cette application LUIS est d’apprendre à LUIS ce qu’est un nom de poste et où il se trouve dans un énoncé. La partie de l’énoncé correspondant au poste peut changer d’un énoncé à l’autre en fonction du choix des mots et de la longueur de l’énoncé. LUIS a besoin d’exemples de postes dans tous les énoncés de toutes les intentions.  

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

Cette application LUIS comporte des noms de poste dans plusieurs intentions. En étiquetant ces mots dans tous les énoncés des intentions, LUIS apprend ce qu’est un poste et où il se trouve dans les énoncés.

## <a name="create-job-simple-entity"></a>Créer une entité simple de poste

1. Assurez-vous que votre application Ressources humaines figure dans la section **Générer** de LUIS. Vous pouvez modifier cette section en sélectionnant **Générer** dans la barre de menu en haut à droite. 

    [ ![Capture d’écran de l’application LUIS avec Générer en surbrillance dans la barre de navigation en haut à droite](./media/luis-quickstart-primary-and-secondary-data/hr-first-image.png)](./media/luis-quickstart-primary-and-secondary-data/hr-first-image.png#lightbox)

2. Dans la page **Intents** (Intentions), sélectionnez l’intention **ApplyForJob**. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png "Capture d’écran de LUIS avec intention « ApplyForJob » mise en surbrillance")](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png#lightbox)

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

## <a name="label-entity-in-example-utterances-for-getjobinformation-intent"></a>Étiquetez les entités dans les énoncés exemple pour l’intention GetJobInformation
1. Dans le menu gauche, sélectionnez **Intents** (Intentions).

2. Sélectionnez **GetJobInformation** dans la liste des intentions. 

3. Étiquetez les postes dans les énoncés exemple suivants :

    |Énoncé|Entité de poste|
    |:--|:--|
    |Existe-t-il des postes à pourvoir en bases de données ?|bases de données|
    |Recherche un nouveau poste avec des responsabilités en comptabilité|comptabilité|
    |Quels postes sont disponibles pour des ingénieurs confirmés ?|ingénieur confirmé|

    Il existe d’autres énoncés exemple mais ils ne contiennent pas de mots de poste.

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS
LUIS ne connaît pas les modifications apportées aux intentions et aux entités (modèle) tant qu’elle n’a pas été entraînée. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Former**.

    ![Sélectionner le bouton Former](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. L’apprentissage est terminé lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

    ![Notification de réussite de l’apprentissage](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publier l’application pour obtenir l’URL de point de terminaison
Pour obtenir une prédiction LUIS dans un chatbot ou une autre application, vous devez publier l’application. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Publier**. 

2. Sélectionnez l’emplacement Production et le bouton **Publier**.

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "Capture d’écran de la page Publier avec le bouton Publier vers l’emplacement Production mis en surbrillance")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. La publication est terminée lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Interroger le point de terminaison avec un autre énoncé
Dans la page **Publier**, sélectionnez le lien **Point de terminaison** en bas de la page. 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "Capture d’écran de la page Publier avec le point de terminaison mis en surbrillance")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

Cette action ouvre une autre fenêtre de navigateur avec l’URL de point de terminaison affichée dans la barre d’adresses. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Here is my c.v. for the programmer job`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner les énoncés `ApplyForJob`.

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

## <a name="to-boost-signal-add-jobs-phrase-list"></a>Pour améliorer le signal, ajoutez une liste d’expressions de postes
Ouvrez le document [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) à partir du référentiel Github LUIS-Samples. La liste comprend plus d’un millier de mots et d’expressions de poste. Consultez la liste pour trouver des mots de poste qui sont significatifs pour vous. Si les mots ou les expressions ne sont pas dans la liste, ajoutez les vôtres.

1. Dans la section **Générer** de l’application LUIS, sélectionnez **Listes d’expressions** dans le menu **Améliorer les performances de l’application**.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png "Capture d’écran de Listes d’expressions, bouton de navigation de gauche en surbrillance")](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png#lightbox)

2. Sélectionnez **Créer une liste d’expressions**. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png "Capture d’écran avec bouton de création d’une nouvelle liste d’expressions mis en surbrillance")](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png#lightbox)

3. Nommez la nouvelle liste d’expressions `Jobs` et copiez la liste depuis jobs-phrase-list.csv vers la zone de texte **Valeurs**. Sélectionnez Enter (Entrer). 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Capture d’écran avec fenêtre contextuelle de création d’une nouvelle liste d’expressions")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Si vous voulez ajouter d’autres mots à la liste d’expressions, passez en revue les **valeurs associées** et ajoutez celles qui sont pertinentes. 

4. Sélectionnez **Enregistrer** pour activer la liste d’expressions.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Capture d’écran de boîte de dialogue contextuelle de création de nouvelle liste d’expressions avec mots dans la zone de valeurs de la liste d’expressions")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. [Entraînez](#train-the-luis-app) et [publiez](#publish-the-app-to-get-the-endpoint-URL) à nouveau l’application pour utiliser la liste d’expressions.

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

## <a name="phrase-lists"></a>Listes d’expressions
L’ajout de la liste d’expressions améliore le signal des mots de la liste mais n’est **pas** utilisé comme une correspondance exacte. La liste d’expressions comprend plusieurs postes dont le premier mot est `lead` contient également le poste `welder` mais pas le poste `lead welder`. Cette liste d’expressions de postes peut ne pas être complète. Au fur et à mesure que vous [passez en revue les énoncés de point de terminaison](label-suggested-utterances.md) et trouvez d’autres mots de poste, ajoutez-les à votre liste d’expressions. Entraînez et publiez à nouveau.

## <a name="what-has-this-luis-app-accomplished"></a>Quel est l’accomplissement de cette application LUIS ?
Cette application, comptant seulement une entité simple et une liste d’expressions, a identifié une intention de requête en langage naturel et a retourné les données de la tâche. 

Votre chatbot possède maintenant suffisamment d’informations pour déterminer l’action principale, postuler pour poste, et un paramètre de cette action, le poste référencé. 

## <a name="where-is-this-luis-data-used"></a>Où ces données LUIS sont-elles utilisées ? 
LUIS en a fini avec cette requête. L’application d’appel, par exemple un chatbot, peut prendre le résultat topScoringIntent et les données de l’entité pour utiliser l’API tierce pour envoyer le message concernant le poste au représentant des Ressources Humaines. S’il existe d’autres options de programmation pour le robot ou l’application d’appel, LUIS n’effectue pas ce travail. LUIS détermine uniquement l’intention de l’utilisateur. 

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, supprimez l’application LUIS. Sélectionnez **Mes applications** dans le menu en haut à gauche. Sélectionnez le menu avec les trois points (...) à droite du nom de l’application dans la liste des applications, puis **Supprimer**. Dans la boîte de dialogue contextuelle **Supprimer l’application ?**, sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter une entité keyphrase prédéfinie](luis-quickstart-intent-and-key-phrase.md)