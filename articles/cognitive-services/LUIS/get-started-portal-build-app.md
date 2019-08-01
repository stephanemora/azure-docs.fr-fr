---
title: 'Démarrage rapide : Créer une application dans le portail LUIS'
titleSuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide traite de la création d’une application dans le portail LUIS. Créez les composants de base d'une application, les intentions et les entités. Ensuite, testez l'application en fournissant un exemple d’énoncé utilisateur dans le panneau de test interactif afin d’obtenir l’intention prévue. La création d’une application est gratuite et ne nécessite pas d’abonnement Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: f54643883028d93b56c7e122f43db95bb6d0b8c1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560874"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Démarrage rapide : Créer une application dans le portail LUIS

Ce guide de démarrage rapide traite de la génération d’une application dans le [portail LUIS](https://www.luis.ai). Vous commencez par créer les composants de base d’une application, les **intentions**, et les **entités**. Ensuite, vous testez l'application en fournissant un exemple d’énoncé utilisateur dans le panneau de test interactif afin d’obtenir l’intention prévue.

La création d’une application est gratuite et ne nécessite pas d’abonnement Azure. Lorsque vous êtes prêt à déployer votre application, consultez le [guide de démarrage rapide pour déployer une application](get-started-portal-deploy-app.md). Il indique comment créer une ressource Azure Cognitive Service et l'affecter à l’application.

## <a name="create-an-app"></a>Créer une application

1. Dans un navigateur, ouvrez le [portail LUIS](https://www.luis.ai) et connectez-vous. S’il s’agit de votre première connexion, vous devez créer un compte d’utilisateur gratuit du portail LUIS.

1. Sélectionnez **Créer une application** dans la barre d’outils contextuelle.

   [![Créer une application dans le portail LUIS](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. Dans la fenêtre indépendante, configurez l’application avec les paramètres suivants, puis sélectionnez **Terminé**.

   |Nom du paramètre| Valeur | Objectif|
   |--|--|--|
   |Nom|`myEnglishApp`|Nom d’application LUIS unique<br>required|
   |Culture|**Anglais**|Langue des énoncés des utilisateurs, à savoir **en-us**<br>required|
   |Description|`App made with LUIS Portal`|Description de l’application<br>facultatif|
   | | | |

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

1. Une fois l’application créée, la page **Intentions** de la section **Build** est affichée. Sélectionnez **Créer une intention**.

   [![Sélectionner le bouton Créer une intention](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Entrez le nom de l’intention, `FindForm`, puis sélectionnez **Terminé**.

   ![Entrez le nom de d’intention de FindForm](./media/get-started-portal-build-app/create-new-intent-dialog.png)

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

1. Sélectionnez **Create new entity** (Créer une entité) sur la page **Entities** (Entités).

1. Entrez le nom `Human Resources Form Number`, sélectionnez le type d'entité **Regex** et entrez l’expression régulière `hrf-[0-9]{6}`. Cette entrée correspond aux caractères littéraux, `hrf-`, et autorise précisément 6 chiffres.

   ![Entrez les informations d’entité pour l’entité d’expression régulière](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Sélectionnez **Terminé**.

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

   Pour cette application de ressources humaines, ces exemples d’énoncés figurent en dehors du domaine. Si votre domaine de ressources humaines inclut des professions liées aux animaux, à l’alimentation ou à la mer, vous devez utiliser d'autres exemples d’énoncés pour l’intention **None** (Aucune).

## <a name="train-the-app"></a>Effectuer l’apprentissage de l’application

Dans le menu de la partie supérieure droite, sélectionnez **Train** (Former) pour appliquer les changements d’intention et de modèle d’entité à la version actuelle de l’application.

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

[![Supprimer l’application de la liste Mes applications](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [2. Déployer une application](get-started-portal-deploy-app.md)
