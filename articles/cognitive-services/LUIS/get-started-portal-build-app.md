---
title: 'Démarrage rapide : Créer une application dans le portail LUIS'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous créez les éléments de base d’une application, les intentions et les entités, et vous les testez avec l’exemple d’énoncé dans le portail LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: diberry
ms.openlocfilehash: cbfb0ddb0ee901545443fc127f80a1837c7bde9b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278571"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Démarrage rapide : Créer une application dans le portail LUIS

[!INCLUDE [Uses preview portal](./includes/uses-portal-preview.md)]

Dans ce guide de démarrage rapide, vous allez créer une application dans le portail LUIS. Vous commencez par créer les composants de base d’une application, les **intentions**, et les **entités**. Ensuite, vous testez l'application en fournissant un exemple d’énoncé utilisateur dans le panneau de test interactif afin d’obtenir l’intention prévue.

La création d’une application est gratuite et ne nécessite pas d’abonnement Azure. Lorsque vous êtes prêt à déployer votre application, consultez le [guide de démarrage rapide pour déployer une application](get-started-portal-deploy-app.md). Il indique comment créer une ressource Azure Cognitive Service et l'affecter à l’application.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Créer une application

1. Sélectionnez **+ Créer** dans la barre d’outils contextuelle.

   [![Créer une application dans le portail LUIS](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. Dans la fenêtre indépendante, configurez l’application avec les paramètres suivants, puis sélectionnez **Terminé**.

   |Nom du paramètre| Valeur | Objectif|
   |--|--|--|
   |Nom|`myEnglishApp`|Nom d’application LUIS unique<br>required|
   |Culture|**Anglais**|Langue des énoncés des utilisateurs, à savoir **en-us**<br>required|
   |Description (Facultatif)|`App made with LUIS Portal`|Description de l’application<br>facultatif|
   |Ressource de prédiction (Facultatif) |-  |Ne sélectionnez pas. LUIS vous donne une clé de démarrage à utiliser gratuitement, pour la création et 1 000 requêtes de point de terminaison de prédiction. |

   ![Entrer de nouveaux paramètres d’application](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Créer des intentions

Une fois l’application LUIS créée, vous devez créer les intentions. Les intentions sont un moyen de caractériser le texte des utilisateurs. Par exemple, une application de ressources humaines pourrait avoir deux fonctions. Pour aider les utilisateurs :

 1. Rechercher et poser candidatures à des postes
 1. Rechercher des formulaires pour poser candidatures à des postes

Les deux différentes _intentions_ de l’application sont alignées sur les intentions suivantes :

|Intention|Exemple de texte d’utilisateur<br>connu en tant qu’_énoncé_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Pour créer des intentions, procédez comme suit :

1. Une fois l’application créée, la page **Intentions** de la section **Build** est affichée. Sélectionnez **Create** (Créer).

   [![Sélectionner Create pour créer une intention](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Entrez le nom de l’intention, `FindForm`, puis sélectionnez **Terminé**.

## <a name="add-an-example-utterance"></a>Ajouter un exemple d’énoncé

Après avoir créé des intentions, vous ajoutez des exemples d'énoncés. Les exemples d'énoncés sont du texte qu’un utilisateur entre dans un bot conversationnel ou une autres application cliente. Ils mappent l’intention du texte de l’utilisateur sur une intention LUIS.

Dans cet exemple d'intention d’application `FindForm`, les exemples d'énoncés incluent le numéro de formulaire. L’application cliente a besoin du numéro de formulaire pour répondre à la demande l’utilisateur. Il est donc important de l’inclure dans l’énoncé.

[![Entrer des exemples d’énoncés pour l’intention FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Ajoutez les 15 exemples d'énoncés suivants à l’intention `FindForm`.

|#|Exemples d’énoncés|
|--|--|
|1|Recherche de hrf-123456|
|2|Où se trouve le formulaire de ressources humaines hrf-234591 ?|
|3|hrf-345623, où est-il|
|4|Est-il possible de m’envoyer hrf-345794|
|5\.|Ai-je besoin de hrf-234695 pour postuler à un emploi en interne ?|
|6|Mon directeur doit-il savoir que je postule à un emploi avec hrf-234091|
|7|Où envoyer hrf-234918 ? Est-ce que je reçois une réponse par messagerie ?|
|8|hrf-234555|
|9|Quand la mise à jour de hrf-234987 a-t-elle été effectuée ?|
|10|Est-ce que je dois utiliser le formulaire hrf-876345 pour postuler aux emplois d’ingénieur|
|11|Une nouvelle version de hrf-765234 a-t-elle été envoyée pour ma demande en cours ?|
|12|Est-ce que j’utilise hrf-234234 pour les emplois à l’étranger ?|
|13|fautes d’orthographe hrf-234598|
|14|hrf-234567 sera-t-il modifié pour tenir compte des nouvelles exigences|
|15|hrf-123456, hrf-123123, hrf-234567|

De par leur conception, ces exemples d’énoncés varient comme suit :

* longueur de l’énoncé
* ponctuation
* choix des mots
* conjugaison du verbe (est, a été, sera)
* ordre des mots



## <a name="create-a-regular-expression-entity"></a>Créer une entité d’expression régulière

Pour renvoyer le numéro de formulaire dans la réponse de prédiction d’exécution, le formulaire doit être marqué en tant qu’entité. Étant donné que le texte du numéro de formulaire est très structuré, vous pouvez le marquer à l’aide d'une entité d’expression régulière. Procédez comme suit pour créer l’entité :

1. Sélectionnez **Entities** (Entités) dans le menu gauche.

1. Sélectionnez **Create** dans la page **Entities**.

1. Entrez le nom `Human Resources Form Number`, sélectionnez le type d’entité **Regex**, puis sélectionnez **Next**.

   ![Créer une entité d’expression régulière](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Entrez l’expression régulière (**RegEx**), `hrf-[0-9]{6}`. Cette entrée correspond aux caractères littéraux, `hrf-`, et autorise précisément 6 chiffres. Sélectionnez ensuite **Create**.

   ![Entrer une expression régulière pour l’entité](./media/get-started-portal-build-app/create-regular-expression-entity-with-expression.png)


## <a name="add-example-utterances-to-the-none-intent"></a>Ajouter des exemples d’énoncés à l’intention « None »

L’intention **None** est une intention de secours fourre-tout qui ne doit pas rester vide. Cette intention doit contenir un énoncé pour chaque dizaine d'exemples d'énoncés que vous avez ajoutés pour les autres intentions de l’application.

Les exemples d’énoncés de l’intention **None** doivent figurer en dehors de votre domaine d’application client.

1. Sélectionnez **Intents** (Intentions) dans le menu de gauche, puis sélectionnez **None** (Aucune) dans la liste des intentions.

1. Ajoutez les énoncés exemples suivants à l’intention :

   |Exemples d’énoncés de l’intention None|
   |--|
   |Les chiens qui aboient sont agaçants|
   |Commande une pizza pour moi|
   |Pingouins dans l’océan|

   Pour cette application, ces exemples d’énoncés figurent en dehors du domaine. Si votre domaine a trait aux animaux, à la nourriture ou à l’océan, vous devez utiliser d’autres exemples d’énoncés pour l’intention **None** (Aucune).

## <a name="train-the-app"></a>Effectuer l’apprentissage de l’application

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Examiner l’entité d’expression régulière dans les exemples d’énoncés

1. Vérifiez que l’entité se trouve dans l’intention **FindForm** en sélectionnant **Intents** (Intentions) dans le menu de gauche. Sélectionnez ensuite l’intention **FindForm**.

   L’entité est marquée à l’emplacement où elle figure dans les exemples d’énoncés. Pour afficher le texte d’origine au lieu du nom d’entité, désactivez **Entities View** (Afficher les entités) dans la barre d’outils.

   [![Tous les exemples d’énoncés marqués avec des entités](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Tester votre nouvelle application avec le volet de test interactif

Utilisez le volet interactif **Test** du portail LUIS pour valider que l’entité est extraite des nouvelles expressions que l’application n’a pas encore rencontrées.

1. Sélectionnez **Test** dans le menu en haut à droite.

1. Ajoutez un nouvel énoncé, puis appuyez sur Entrée :

   ```Is there a form named hrf-234098```

   ![Tester le nouvel énoncé dans le volet de test](./media/get-started-portal-build-app/test-new-utterance.png)

   L'intention prédite supérieure est bien **FindForm** avec plus de 90 % (0,977) de confiance. L'entité **Human Resources Form Number** (numéro de formulaire de ressources humaines) est extraite avec la valeur hrf-234098.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous avez terminé ce démarrage rapide et si vous ne passez pas au démarrage rapide suivant, sélectionnez **Mes applications** dans le menu de navigation supérieur. Cochez ensuite la case à gauche de l’application dans la liste, puis sélectionnez **Supprimer** dans la barre d’outils contextuelle au-dessus de la liste.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [2. Déployer une application](get-started-portal-deploy-app.md)
