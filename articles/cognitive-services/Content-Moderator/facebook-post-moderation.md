---
title: 'Didacticiel : Modérer le contenu Facebook - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez apprendre à utiliser le service Content Moderator, basé sur l’apprentissage automatique, pour faciliter la modération des posts et commentaires Facebook.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/18/2019
ms.author: pafarley
ms.openlocfilehash: 662eca2a727f3112f169ab8d669bf18c81700275
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57871026"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Didacticiel : Modérer les publications et commandes Facebook avec Azure Content Moderator

Dans ce tutoriel, vous allez apprendre à utiliser Azure Content Moderator, pour faciliter la modération des publications et commentaires sur une page Facebook. Facebook enverra le contenu publié par les visiteurs au service Content Moderator. Vos flux de travail Content Moderator publient le contenu ou créent des révisions dans l’outil de révision selon les scores et les seuils de contenu. Consultez la [vidéo de démonstration du build 2017](https://channel9.msdn.com/Events/Build/2017/T6033) pour obtenir un exemple de ce scénario.

Ce didacticiel vous explique les procédures suivantes :

> [!div class="checklist"]
> * Créer une équipe Content Moderator.
> * Créer des fonctions Azure qui écoutent les événements HTTP à partir de Content Moderator et de Facebook.
> * Lier une page Facebook à Content Moderator à l’aide d’une application Facebook.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Ce diagramme illustre chaque composant du scénario :

![Diagramme de Content Moderator recevant des informations de Facebook via « FBListener » et envoyant des informations via « CMListener ».](images/tutorial-facebook-moderation.png)

## <a name="prerequisites"></a>Prérequis

- Une clé d’abonnement Content Moderator. Suivez les instructions dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner au service Content Moderator et obtenir votre clé.
- Un [compte Facebook](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Créer une équipe de révision

Reportez-vous au guide de démarrage rapide [Essayer Content Moderator sur le web](quick-start.md) pour obtenir des instructions sur la façon de vous inscrire à l’[Outil de révision Content Moderator](https://contentmoderator.cognitive.microsoft.com/) et de créer une équipe de révision. Prenez note de la valeur de l’**ID de l’équipe** dans la page **Informations d’identification**.

## <a name="configure-image-moderation-workflow"></a>Configurer un flux de travail de modération des images

Reportez-vous au guide [Définir, tester et utiliser des flux de travail](review-tool-user-guide/workflows.md) pour configurer un flux de travail d’image personnalisé. Cela permettra à Content Moderator de vérifier automatiquement les images sur Facebook et d’en envoyer certaines à l’outil de révision du code. Prenez note du **nom** du flux de travail.

## <a name="configure-text-moderation-workflow"></a>Configurer un flux de travail de modération du texte

Reportez-vous à nouveau au guide [Définir, tester et utiliser des flux de travail](review-tool-user-guide/workflows.md) afin de créer, cette fois, un flux de travail de texte personnalisé. Cela permettra à Content Moderator de vérifier automatiquement le contenu texte. Prenez note du **nom** du flux de travail.

![Configurer un flux de travail de texte](images/text-workflow-configure.PNG)

Testez votre flux de travail à l’aide du bouton **Exécuter le flux de travail**.

![Tester un flux de travail de texte](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Créer des fonctions Azure

Connectez-vous au [portail Azure](https://portal.azure.com/) et procédez comme suit :

1. Créez une application de fonction Azure, comme indiqué dans la page [Fonctions Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
2. Accédez l’application Function App nouvellement créée.
3. Dans l’application, accédez à l’onglet **Fonctionnalités de la plateforme** et sélectionnez **Paramètres d’application**. Dans la section **Paramètres d’application** de la page suivante, faites défiler vers le bas de la liste et cliquez sur **Ajouter un nouveau paramètre**. Ajoutez les paires clé/valeurs suivantes
    
    | Nom du paramètre d’application | value   | 
    | -------------------- |-------------|
    | cm:TeamId   | Votre ID d’équipe Content Moderator  | 
    | cm:SubscriptionKey | Votre clé d’abonnement Content Moderator - voir [Informations d’identification](review-tool-user-guide/credentials.md) | 
    | cm:Region | Nom de votre région Content Moderator, sans espaces. Voir la remarque précédente. |
    | cm:ImageWorkflow | Nom du flux de travail à exécuter sur les images |
    | cm:TextWorkflow | Nom du flux de travail à exécuter sur le texte |
    | cm:CallbackEndpoint | URL de l’application de fonction CMListener que vous créez plus loin dans ce guide |
    | fb:VerificationToken | Jeton secret, également utilisé pour vous abonner aux événements de flux Facebook |
    | fb:PageAccessToken | Le jeton d’accès à l’API Graph Facebook n’expire pas et permet à la fonction de masquer/supprimer des posts pour votre compte. |

    Cliquez sur le bouton **Enregistrer** en haut de la page.

1. Utilisez le bouton **+** dans le volet gauche pour afficher le volet Nouvelle fonction.

    ![Bouton Ajouter une fonction mis en surbrillance dans le volet Azure Functions.](images/new-function.png)

    Cliquez ensuite sur **+ Nouvelle fonction** en haut de la page. Cette fonction reçoit des événements de Facebook. Créez cette fonction en effectuant les étapes suivantes :

    1. Cliquez sur la vignette indiquant **Déclencheur HTTP**.
    1. Entrez le nom **FBListener**. Le champ **Niveau d’autorisation** doit être défini sur **Fonction**.
    1. Cliquez sur **Créer**.
    1. Remplacez le contenu du fichier **run.csx** par le contenu de **FbListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-160)]

1. Créez une fonction **Http trigger** nommée **CMListener**. Cette fonction reçoit des événements de Content Moderator. Remplacez le contenu du fichier **run.csx** par le contenu de **CMListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-106)]

---

## <a name="configure-the-facebook-page-and-app"></a>Configurer la page et l’application Facebook
1. Créez une application Facebook.

    ![page facebook for developers](images/facebook-developer-app.png)

    1. Accédez au [site de développement Facebook](https://developers.facebook.com/).
    2. Cliquer sur **Mes applications**.
    3. Ajoutez une nouvelle application.
    1. donnez-lui un nom
    1. Sélectionnez **Webhooks -> Configurer**
    1. Sélectionnez **Page** dans le menu déroulant, puis sélectionnez **S’abonner à cet objet**
    1. Fournissez l’**URL FBListener** comme URL de rappel et le **jeton de vérification** que vous avez configuré sous les **Paramètres de l’application de fonction**.
    1. Une fois abonné, faites défiler l’écran jusqu’au flux, puis sélectionnez **S’abonner**.

2. Créez une page Facebook.

    1. Accédez à [Facebook](https://www.facebook.com/bookmarks/pages), puis créez une **page Facebook**.
    2. Autorisez l’application Facebook à accéder à cette page en suivant les étapes suivantes :
        1. Accédez à l’[Explorateur API Graph](https://developers.facebook.com/tools/explorer/).
        2. Sélectionnez **Application**.
        3. Sélectionnez **Jeton d’accès à la page**, puis envoyez une requête **GET**.
        4. Cliquez sur l’**ID de la page** dans la réponse.
        5. Ajoutez maintenant **/subscribed_apps** à l’URL, puis envoyez une requête **GET** (réponse vide).
        6. Soumettez une requête **POST**. Vous obtenez la réponse sous la forme **success: true**.

3. Créez un jeton d’accès API Graph sans date d’expiration.

    1. Accédez à l’[Explorateur API Graph](https://developers.facebook.com/tools/explorer/).
    2. Sélectionnez l’option **Application**.
    3. Sélectionnez l’option **Obtenir un jeton d’accès utilisateur**.
    4. Sous **Sélectionner des autorisations**, sélectionnez les options **manage_pages** et **publish_pages**.
    5. Nous allons utiliser le **jeton d’accès** (jeton de courte durée) à la prochaine étape.

4. Nous utilisons Postman pour les prochaines étapes.

    1. Ouvrez **Postman** (ou obtenez-le [ici](https://www.getpostman.com/)).
    2. Importez les deux fichiers suivants :
        1. [Collection Postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Environnement Postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Mettez à jour les variables d’environnement suivantes :
    
        | Clé | Valeur   | 
        | -------------------- |-------------|
        | appId   | Insérez votre identificateur d’application Facebook ici  | 
        | appSecret | Insérez votre secret d’application Facebook ici | 
        | short_lived_token | Insérez le jeton d’accès utilisateur de courte durée que vous avez créé à l’étape précédente |
    4. Exécutez maintenant les 3 API répertoriées dans la collection : 
        1. Sélectionnez **Générer un jeton d’accès de longue durée**, puis cliquez sur **Envoyer**.
        2. Sélectionnez **Obtenir l’ID utilisateur**, puis cliquez sur **Envoyer**.
        3. Sélectionnez **Obtenir un jeton d’accès à la page permanent**, puis cliquez sur **Envoyer**.
    5. Copiez la valeur **access_token** à partir de la réponse, puis affectez-la au paramètre d’application, **fb:PageAccessToken**.

La solution envoie toutes les images et le texte publiés sur votre page Facebook à Content Moderator. Les flux de travail précédemment configurés sont appelés. Le contenu qui ne répond pas à vos critères définis dans le flux de travail sont passés aux revues dans l’outil de révision du code. Le reste du contenu est publié automatiquement.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez configuré un programme pour analyser des images de produits afin de les étiqueter par type de produit et de permettre à une équipe de révision de prendre les décisions appropriées du point de vue de la modération du contenu. À présent, découvrez les détails de la modération des images.

> [!div class="nextstepaction"]
> [Modération d’image](./image-moderation-api.md)
