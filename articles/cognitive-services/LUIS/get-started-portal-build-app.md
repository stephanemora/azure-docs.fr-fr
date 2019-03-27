---
title: 'Démarrage rapide : Créer une application avec le portail LUIS'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ce guide de démarrage rapide traite de la création d’une application dans le portail LUIS. Créez les composantes de base d’une application, des intentions et des entités, puis testez-les en fournissant un exemple d’énoncé utilisateur dans le panneau de test interactif afin d’obtenir l’intention prévue. La création d’une application est gratuite et ne nécessite pas d’abonnement Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: e97be28261d28c2a72e507adcdac0248691745c7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57783168"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Démarrage rapide : Créer une application dans le portail LUIS

Ce guide de démarrage rapide traite de la création d’une application dans le [portail LUIS](https://www.luis.ai). Créez les composantes de base d’une application, des **intentions** et des **entités**, puis testez-les en fournissant un exemple d’énoncé utilisateur dans le panneau de test interactif afin d’obtenir l’intention prévue.

La création d’une application est gratuite et ne nécessite pas d’abonnement Azure. Lorsque vous êtes prêt à déployer votre application, vous créez une ressource Azure Cognitive Service et l’affectez à l’application. Ce processus de déploiement figure dans le [prochain démarrage rapide](get-started-portal-deploy-app.md).

## <a name="create-app"></a>Créer une application 

1. Dans un navigateur, ouvrez le [portail LUIS](https://www.luis.ai) et connectez-vous. S’il s’agit de votre première connexion, vous devez créer un compte d’utilisateur gratuit du portail LUIS.

1. Sélectionnez **Créer une application** dans la barre d’outils contextuelle.

    [![Créer une application dans le portail LUIS](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. Dans la fenêtre indépendante, configurez l’application avec les paramètres suivants puis sélectionnez **Terminé**.

    |Nom du paramètre| Valeur | Objectif|
    |--|--|--|
    |Nom|`myEnglishApp`|Nom d’application LUIS unique<br>required|
    |Culture|**Anglais**|Langue des énoncés des utilisateurs, à savoir **en-us**<br>required|
    |Description|`App made with LUIS Portal`|Description de l’application<br>facultatif|

    ![Entrer de nouveaux paramètres d’application](./media/get-started-portal-build-app/create-new-app-settings.png)


## <a name="create-intent"></a>Créer une intention 

Une fois cette application créée, l’étape suivante consiste à créer les intentions. Les intentions sont un moyen de caractériser le texte des utilisateurs. Si vous disposez d’une application de ressources humaines qui a deux fonctions : la première pour aider les gens à trouver une offre d’emploi et y postuler, la seconde pour trouver des formulaires de demande d'emploi, ces deux _intentions_ différentes correspondent aux intentions suivantes :

|Intention|Exemple de texte d’utilisateur<br>connu en tant qu’_énoncé_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

1. Une fois l’application créée, la page **Intentions** de la section **Build** est affichée. Sélectionnez **Créer une intention**. 

    [![Sélectionner le bouton Créer une intention](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Entrez le nom de l’intention, `FindForm`, et sélectionnez **Terminé**.

    ![Entrez le nom de d’intention de FindForm](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-example-utterance"></a>Ajouter un exemple d’énoncé 

Après avoir créé l’intention, l’étape suivante consiste à ajouter des exemples d’énoncés. Il s’agit d’un texte, entré par l’utilisateur dans un bot conversationnel ou une autre application client, qui associe l’intention du texte de l’utilisateur à une intention LUIS. 

Pour l’intention `FindForm` de cette application exemple, les exemples d’énoncés incluent le numéro du formulaire, qui est une information importante dans l’énoncé dont l’application cliente a besoin pour répondre à la requête de l’utilisateur. 

Ajoutez les 15 exemples d’énoncés suivants à l’intention `FindForm`. 

|#|Exemples d’énoncés|
|--|--|
|1|Recherche de hrf-123456|
|2|Où se trouve le formulaire de ressources humaines hrf-234591 ?|
|3|hrf-345623, où est-il|
|4|Est-il possible de m’envoyer hrf-345794|
|5.|Ai-je besoin de hrf-234695 pour postuler à un emploi en interne ?|
|6.|Mon directeur doit-il savoir que je postule à un emploi avec hrf-234091|
|7|Où envoyer hrf-234918 ? Est-ce que je reçois une réponse par messagerie ?|
|8|hrf-234555|
|9|Quand la mise à jour de hrf-234987 a-t-elle été effectuée ?|
|10|Est-ce que je dois utiliser le formulaire hrf-876345 pour postuler aux emplois d’ingénieur|
|11|Une nouvelle version de hrf-765234 a-t-elle été envoyée pour ma demande en cours ?|
|12|Est-ce que j’utilise hrf-234234 pour les emplois à l’étranger ?|
|13|fautes d’orthographe hrf-234598|
|14|hrf-234567 sera-t-il modifié pour tenir compte des nouvelles exigences|
|15|hrf-123456, hrf-123123, hrf-234567|

Ces exemples d’énoncés varient, selon les cas, de la façon suivante :

* longueur de l’énoncé
* ponctuation
* choix des mots
* conjugaison du verbe (est, a été, sera)
* ordre des mots

[![Entrer des exemples d’énoncés pour l’intention FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

## <a name="create-regular-expression-entity"></a>Créer une entité d’expression régulière 

Afin d’obtenir le numéro de formulaire retourné dans le cadre de la réponse de prédiction d’exécution, le formulaire doit être marqué en tant qu’entité. Comme le texte du numéro de formulaire est très structuré, il peut être marqué à l’aide d'une entité d’expression régulière. Procédez comme suit pour créer l’entité. 

1. Dans le menu de navigation de gauche, sélectionnez **Entités**. 

1. Sélectionnez **Create new entity** (Créer une entité) sur la page d’entités.

1. Entrez le nom `Human Resources Form Number`, sélectionnez le type d'entité **Regex** et entrez l’expression régulière `hrf-[0-9]{6}`. Cela correspond aux caractères littéraux, `hrf-`, et autorise six chiffres précisément. 

    ![Entrez les informations d’entité pour l’entité d’expression régulière](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Sélectionnez **Terminé**. 

## <a name="add-example-utterances-to-none-intent"></a>Ajouter des exemples d’énoncés à l’intention None

L’intention **None** est une intention de secours fourre-tout qui ne doit pas rester vide. Cette intention doit posséder 1 énoncé, et cela doit se répéter toutes les 10 intentions dans les intentions restantes de l’application. 

Les exemples d’énoncés de l’intention **None** doivent figurer en dehors de votre domaine d’application client. 

1. Sélectionnez **Intentions** dans le menu de gauche, puis sélectionnez l’intention **None**.

1. Ajoutez les énoncés exemples suivants à l’intention :

    |Exemples d’énoncés de l’intention None|
    |--|
    |Les chiens qui aboient sont agaçants|
    |Commande une pizza pour moi|
    |Pingouins dans l’océan|

    Pour cette application de ressources humaines, ces exemples d’énoncés figurent en dehors du domaine. Si votre domaine de ressources humaines inclut des professions liées aux animaux, à l’alimentation ou à la mer, ces exemples d’énoncés ne doivent pas être utilisés pour l’intention **None**. 

## <a name="train-the-app"></a>Effectuer l’apprentissage de l’application

Dans le volet de navigation en haut à droite, sélectionnez **Former** pour appliquer les changements d’intention et de modèle d’entité à la version actuelle de l’application. 

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Examiner l’entité d’expression régulière dans les exemples d’énoncés

1. Vérifiez que l’entité se trouve dans l’intention **FindForm** en sélectionnant **Intents** (Intentions) dans le menu de gauche, puis sélectionnez l’intention **FindForm**. 

    L’entité est marquée à l’emplacement où elle figure dans les exemples d’énoncés. Pour afficher le texte d’origine au lieu du nom d’entité, désactivez **Afficher les entités** dans la barre d’outils.

    [![Tous les exemples d’énoncés marqués avec des entités](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Tester votre nouvelle application avec le volet de test interactif

Utilisez le volet interactif **Test** du portail LUIS pour valider que l’entité est extraite des nouvelles expressions que l’application n’a pas encore rencontrées.

1. Sélectionnez **Test** dans le menu en haut à droite.

1. Ajoutez un nouvel énoncé, puis appuyez sur Entrée :

    ```Is there a form named hrf-234098```

    ![Tester le nouvel énoncé dans le volet de test](./media/get-started-portal-build-app/test-new-utterance.png)

    L’intention prédite la plus élevée est **FindForm** avec un taux de confiance de plus de 90 % (0,977), tandis que l’entité **Human Resources Form Number** est extraite avec la valeur hrf-234098. 

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous avez terminé ce démarrage rapide et avant de passer au démarrage rapide suivant, sélectionnez **Mes applications** dans le menu de navigation supérieur. Cochez ensuite la case à gauche de l’application dans la liste, puis sélectionnez **Supprimer** dans la barre d’outils contextuelle au-dessus de la liste. 

[![Supprimer l’application de la liste Mes applications](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [2. Déployer une application](get-started-portal-deploy-app.md)