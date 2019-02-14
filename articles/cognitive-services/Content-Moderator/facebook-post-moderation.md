---
title: 'Tutoriel : Modération du contenu Facebook - Azure Content Moderator'
titlesuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez apprendre à utiliser le service Content Moderator, basé sur l’apprentissage automatique, pour faciliter la modération des posts et commentaires Facebook.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 86c89164e3ccd5bf5df303b98cf6d336f3916e2b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878050"
---
# <a name="tutorial-facebook-content-moderation-with-content-moderator"></a>Tutoriel : Modération du contenu Facebook avec Content Moderator

Dans ce tutoriel, vous allez apprendre à utiliser le service Content Moderator, basé sur l’apprentissage automatique, pour faciliter la modération des posts et commentaires Facebook.

Ce tutoriel vous guide tout au long des étapes suivantes :

1. Créer une équipe Content Moderator.
2. Créer des fonctions Azure qui écoutent les événements HTTP à partir de Content Moderator et de Facebook.
3. Créer une page et une application Facebook, puis connecter cette dernière à Content Moderator.

Quand nous aurons terminé, Facebook enverra le contenu publié par les visiteurs à Content Moderator. En fonction des seuils de correspondance, vos flux de travail Content Moderator publient le contenu ou créent des révisions dans l’outil de révision. 

La figure suivante illustre les composants de la solution.

![Modération des publications Facebook](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Créer une équipe Content Moderator

Consultez le guide de démarrage rapide [Essayer Content Moderator sur le web](quick-start.md) pour savoir comment vous inscrire au service et créer une équipe.

## <a name="configure-image-moderation-workflow-threshold"></a>Configurer un flux de travail de modération des images (seuil)

Reportez-vous à la page [Flux de travail](review-tool-user-guide/workflows.md) pour configurer un flux de travail d’image personnalisé (seuil). Notez le **nom** du flux de travail.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. Configurer un flux de travail de modération du texte (seuil)

Utilisez des étapes similaires à celles de la page [Flux de travail](review-tool-user-guide/workflows.md) pour configurer un flux de travail et un seuil de texte personnalisés. Notez le **nom** du flux de travail.

![Configurer un flux de travail de texte](images/text-workflow-configure.PNG)

Testez votre flux de travail à l’aide du bouton « Exécuter le flux de travail ».

![Tester un flux de travail de texte](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Créer des fonctions Azure

Connectez-vous au [portail de gestion Azure](https://portal.azure.com/) pour créer vos fonctions Azure. Procédez comme suit :

1. Créez une application de fonction Azure, comme indiqué dans la page [Fonctions Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
2. Ouvrez l’application de fonction nouvellement créée.
3. Dans l’application, accédez à **Fonctionnalités de la plateforme -> Paramètres de l’application**.
4. Définissez les [paramètres d’application](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings) suivants :

> [!NOTE]
> La valeur **cm: region** doit être le nom de la région (sans espace).
> Par exemple, **westeurope**, pas Europe Ouest, **westcentralus**, pas USA Centre-Ouest, et ainsi de suite.
>

| Paramètre d'application | Description   | 
| -------------------- |-------------|
| cm:TeamId   | Votre ID d’équipe Content Moderator  | 
| cm:SubscriptionKey | Votre clé d’abonnement Content Moderator - voir [Informations d’identification](review-tool-user-guide/credentials.md) | 
| cm:Region | Nom de votre région Content Moderator, sans espaces. Voir la remarque précédente. |
| cm:ImageWorkflow | Nom du flux de travail à exécuter sur les images |
| cm:TextWorkflow | Nom du flux de travail à exécuter sur le texte |
| cm:CallbackEndpoint | URL de l’application de fonction CMListener que vous créez plus loin dans ce guide |
| fb:VerificationToken | Jeton secret, également utilisé pour vous abonner aux événements de flux Facebook |
| fb:PageAccessToken | Le jeton d’accès à l’API Graph Facebook n’expire pas et permet à la fonction de masquer/supprimer des posts pour votre compte. |

5. Créez une fonction **HttpTrigger-CSharp** nommée **FBListener**. Cette fonction reçoit des événements de Facebook. Créez cette fonction en effectuant les étapes suivantes :

    1. Gardez la page [Création de fonctions Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) ouverte pour référence.
    2. Cliquez sur le **+** (Ajouter) pour créer une nouvelle fonction.
    3. Au lieu des modèles intégrés, choisissez l’option **Bien démarrer tout seul/Fonction personnalisée**.
    4. Cliquez sur la vignette indiquant **HttpTrigger-CSharp**.
    5. Entrez le nom **FBListener**. Le champ **Niveau d’autorisation** doit être défini sur **Fonction**.
    6. Cliquez sur **Créer**.
    7. Remplacez le contenu du fichier **run.csx** par le contenu de [**FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx).

6. Créez une fonction **HttpTrigger-CSharp** nommée **CMListener**. Cette fonction reçoit des événements de Content Moderator. Pour la créer, effectuez les étapes suivantes.

    1. Gardez la page [Création de fonctions Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) ouverte pour référence.
    2. Cliquez sur le **+** (Ajouter) pour créer une nouvelle fonction.
    3. Au lieu des modèles intégrés, choisissez l’option **Bien démarrer tout seul/Fonction personnalisée**.
    4. Cliquez sur la vignette indiquant **HttpTrigger-CSharp**.
    5. Entrez le nom **CMListener**. Le champ **Niveau d’autorisation** doit être défini sur **Fonction**.
    6. Cliquez sur **Créer**.
    7. Remplacez le contenu du fichier **run.csx** par le contenu de [**CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx).

## <a name="configure-the-facebook-page-and-app"></a>Configurer la page et l’application Facebook
1. Créez une application Facebook.

    1. Accédez au [site de développement Facebook](https://developers.facebook.com/).
    2. Cliquer sur **Mes applications**.
    3. Ajoutez une nouvelle application.
    4. Sélectionnez **Webhooks -> Prise en main**.
    5. Sélectionnez **Page -> S’abonner à cette rubrique**.
    6. Fournissez l’**URL FBListener** comme URL de rappel et le **jeton de vérification** que vous avez configuré sous les **Paramètres de l’application de fonction**.
    7. Une fois abonné, faites défiler l’écran jusqu’au flux, puis sélectionnez **S’abonner**.

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

Et voilà !

La solution envoie toutes les images et le texte publiés sur votre page Facebook à Content Moderator. Les flux de travail précédemment configurés sont appelés. Le contenu qui ne répond pas à vos critères définis dans le flux de travail entraîne des révisions dans l’outil de révision. Le reste du contenu est publié.

## <a name="license"></a>Licence

Tous les SDK Microsoft Cognitive Services et les exemples sont fournis sous licence MIT. Pour plus d’informations, consultez [LICENCE](https://microsoft.mit-license.org/).

## <a name="next-steps"></a>Étapes suivantes

1. [Regarder une démonstration (vidéo)](https://channel9.msdn.com/Events/Build/2017/T6033) de cette solution de Microsoft Build 2017.
1. [Exemple Facebook sur GitHub](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
