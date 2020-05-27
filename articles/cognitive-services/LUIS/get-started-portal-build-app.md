---
title: 'Démarrage rapide : Créer une application dans le portail LUIS'
description: Dans ce guide de démarrage rapide, vous créez les éléments de base d’une application, les intentions et les entités, et vous les testez avec l’exemple d’énoncé dans le portail LUIS.
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: 7cf55a7891b7e06c18c80d9d359b19e54f0413a9
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697282"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Démarrage rapide : Créer une application dans le portail LUIS

Dans ce guide de démarrage rapide, vous allez créer une application dans le portail LUIS. Tout d’abord, créez les composants de base d’une application, les **intentions** et les **entités**. Ensuite, testez l’application en fournissant un exemple d’énoncé utilisateur dans le panneau de test interactif afin d’obtenir l’intention prédite.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Créer une application

1. Sélectionnez **+Nouvelle application pour conversation** dans la barre d’outils du contexte, puis **Nouvelle application pour conversation**.

    > [!div class="mx-imgBorder"]
    > [![Capture d’écran de la création d’une nouvelle application dans le portail LUIS](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. Dans la fenêtre indépendante, configurez l’application avec les paramètres suivants, puis sélectionnez **Terminé**.

   |Nom du paramètre| Valeur | Objectif|
   |--|--|--|
   |Nom|`myEnglishApp`|Nom d’application LUIS unique<br>Obligatoire|
   |Culture|**Anglais**|Langue des énoncés des utilisateurs, à savoir **en-us**<br>Obligatoire|
   |Description (Facultatif)|`App made with LUIS Portal`|Description de l’application<br>facultatif|
   |Ressource de prédiction (Facultatif) |-  |Ne sélectionnez pas. LUIS vous donne une clé de démarrage à utiliser gratuitement, pour la création et 1 000 requêtes de point de terminaison de prédiction. |

   ![Capture d’écran de la saisie de nouveaux paramètres d’application](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Créer des intentions

Une fois l’application LUIS créée, vous devez créer les intentions. Les intentions sont un moyen de classer le texte des utilisateurs. Par exemple, une application de ressources humaines pourrait avoir deux fonctions. Pour aider les utilisateurs :

 1. Rechercher et poser candidatures à des postes
 1. Rechercher des formulaires pour poser candidatures à des postes

Les deux différentes _intentions_ de l’application sont alignées sur les intentions suivantes :

|Intentionnel|Exemple de texte d’utilisateur<br>connu en tant qu’_énoncé_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Pour créer des intentions, procédez comme suit :

1. Une fois l’application créée, la page **Intentions** de la section **Build** est affichée. Sélectionnez **Create** (Créer).

   [![Capture d’écran de la sélection de l’option « Créer » pour créer une nouvelle intention](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Entrez le nom de l’intention, `FindForm`, puis sélectionnez **Terminé**.

## <a name="add-an-example-utterance"></a>Ajouter un exemple d’énoncé

Après avoir créé des intentions, vous ajoutez des exemples d'énoncés. Les exemples d'énoncés sont du texte qu’un utilisateur entre dans un bot conversationnel ou une autres application cliente. Ils mappent l’intention du texte de l’utilisateur sur une intention LUIS.

Dans cet exemple d'intention d’application `FindForm`, les exemples d'énoncés incluent le numéro de formulaire. L’application cliente a besoin du numéro de formulaire pour répondre à la demande l’utilisateur. Il est donc important de l’inclure dans l’énoncé.

> [!div class="mx-imgBorder"]
> [![Capture d’écran de la saisie d’exemples d’énoncés pour l’intention FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Ajoutez les 15 exemples d'énoncés suivants à l’intention `FindForm`.

|#|Exemples d’énoncés|
|--|--|
|1|`Looking for hrf-123456`|
|2|`Where is the human resources form hrf-234591?`|
|3|`hrf-345623, where is it`|
|4|`Is it possible to send me hrf-345794`|
|5|`Do I need hrf-234695 to apply for an internal job?`|
|6|`Does my manager need to know I'm applying for a job with hrf-234091`|
|7|`Where do I send hrf-234918? Do I get an email response it was received?`|
|8|`hrf-234555`|
|9|`When was hrf-234987 updated?`|
|10|`Do I use form hrf-876345 to apply for engineering positions`|
|11|`Was a new version of hrf-765234 submitted for my open req?`|
|12|`Do I use hrf-234234 for international jobs?`|
|13|`hrf-234598 spelling mistake`|
|14|`will hrf-234567 be edited for new requirements`|
|15|`hrf-123456, hrf-123123, hrf-234567`|

De par leur conception, ces exemples d’énoncés varient comme suit :

* longueur de l’énoncé
* [ponctuation](luis-reference-application-settings.md#punctuation-normalization)
* choix des mots
* conjugaison du verbe (est, a été, sera)
* ordre des mots


## <a name="create-a-regular-expression-entity"></a>Créer une entité d’expression régulière

Pour renvoyer le numéro de formulaire dans la réponse de prédiction d’exécution, le numéro de formulaire doit être extrait en tant qu’entité. Étant donné que le texte du numéro de formulaire est très structuré, vous pouvez utiliser une entité d’expression régulière. Procédez comme suit pour créer l’entité d’expression régulière :

1. Sélectionnez **Entities** (Entités) dans le menu gauche.

1. Sélectionnez **Create** dans la page **Entities**.

1. Entrez le nom `FormNumber`, sélectionnez le type d’entité **Regex**.

1. Entrez l’expression régulière `hrf-[0-9]{6}` dans le champ **RegEx**. Cette entrée correspond aux caractères littéraux, `hrf-`, et autorise précisément six chiffres. Sélectionnez ensuite **Créer**.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la création d’une entité d’expression régulière](./media/get-started-portal-build-app/create-regular-expression-entity.png)


    Cette entité extrait tout texte qui correspond à l’expression régulière dans l’une des intentions.

## <a name="add-example-utterances-to-the-none-intent"></a>Ajouter des exemples d’énoncés à l’intention « None »

L’intention **None** est une intention de secours fourre-tout qui ne doit pas rester vide. Cette intention doit contenir un énoncé pour chaque dizaine d'exemples d'énoncés que vous avez ajoutés pour les autres intentions de l’application.

Les exemples d’énoncés de l’intention **None** doivent figurer en dehors de votre domaine d’application client.

1. Sélectionnez **Intents** (Intentions) dans le menu de gauche, puis sélectionnez **None** (Aucune) dans la liste des intentions.

1. Ajoutez les énoncés exemples suivants à l’intention :

   |Exemples d’énoncés de l’intention None|
   |--|
   |`Barking dogs are annoying`|
   |`Penguins in the ocean`|

   Pour cette application, ces exemples d’énoncés figurent en dehors du domaine. Si votre domaine a trait aux animaux ou à l’océan, vous devez utiliser d’autres exemples d’énoncés pour l’intention **Aucune**.

## <a name="train-the-app"></a>Effectuer l’apprentissage de l’application

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Examiner l’entité d’expression régulière dans les exemples d’énoncés

1. Vérifiez que l’entité se trouve dans l’intention **FindForm** en sélectionnant **Intents** (Intentions) dans le menu de gauche. Sélectionnez ensuite l’intention **FindForm**.

   L’entité est marquée à l’emplacement où elle figure dans les exemples d’énoncés.

   > [!div class="mx-imgBorder"]
   > [![Capture d’écran de tous les exemples d’énoncés marqués avec des entités](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Tester votre nouvelle application avec le volet de test interactif

Utilisez le volet interactif **Test** du portail LUIS pour valider que l’entité est extraite des nouvelles expressions que l’application n’a pas encore rencontrées.

1. Sélectionnez **Test** dans le menu en haut à droite.

1. Ajoutez un nouvel énoncé, puis appuyez sur Entrée :

   ```Is there a form named hrf-234098```

    Sélectionnez **Inspect** pour voir les prédictions d’entité.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran du test du nouvel énoncé dans le volet de test](./media/get-started-portal-build-app/test-new-utterance.png)

   L'intention prédite supérieure est bien **FindForm** avec plus de 90 % (0,977) de confiance. L’entité **FormNumber** est extraite avec la valeur hrf-234098.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous avez terminé ce démarrage rapide et si vous ne passez pas au démarrage rapide suivant, sélectionnez **Mes applications** dans le menu de navigation supérieur. Cochez ensuite la case à gauche de l’application dans la liste, puis sélectionnez **Supprimer** dans la barre d’outils contextuelle au-dessus de la liste.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [2. Déployer une application](get-started-portal-deploy-app.md)
