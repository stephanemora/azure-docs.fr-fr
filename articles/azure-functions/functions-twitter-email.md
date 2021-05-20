---
title: Créer une fonction qui s’intègre avec Azure Logic Apps
description: Créer une fonction à intégrer à Azure Logic Apps et à Azure Cognitive Services. Le workflow obtenu classe les sentiments de tweet et envoie des notifications par e-mail.
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 04/10/2021
ms.author: cshoe
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47
ms.openlocfilehash: 68953a2848594cac673f133b2a8fbd4de1ae63f0
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108760136"
---
# <a name="tutorial-create-a-function-to-integrate-with-azure-logic-apps"></a>Tutoriel : Créer une fonction à intégrer à Azure Logic Apps

Azure Functions s’intègre avec Azure Logic Apps dans le Concepteur d’applications logiques. Cette intégration vous permet d’utiliser la puissance de calcul de Functions dans des orchestrations avec d’autres services Azure et tiers.

Ce tutoriel vous montre comment créer un workflow pour analyser l’activité de Twitter. À mesure que les tweets sont évalués, le workflow envoie des notifications quand des sentiments positifs sont détectés.

Ce didacticiel vous apprend à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créez une ressource API Cognitive Services.
> * Créer une fonction qui classe le sentiment des tweets.
> * Créer une application logique qui se connecte à Twitter.
> * Ajouter la détection des sentiments à l’application logique.
> * Connecter l’application logique à la fonction.
> * Envoyer un courrier électronique en fonction de la réponse de la fonction.

## <a name="prerequisites"></a>Prérequis

* Un compte [Twitter](https://twitter.com/) actif.
* Un compte [Outlook.com](https://outlook.com/) (pour l’envoi de notifications).

> [!NOTE]
> Si vous souhaitez utiliser le connecteur Gmail, seuls les comptes professionnels G-Suite peuvent utiliser ce connecteur sans restriction dans Logic Apps. Si vous disposez d’un compte de consommateur Gmail, vous pouvez utiliser le connecteur uniquement avec certains services et applications approuvés par Google, ou vous pouvez [créer une application cliente Google pour servir lors de l’authentification dans votre connecteur Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). <br><br>Pour plus d’informations, consultez [Stratégies de confidentialité et de sécurité des données pour les connecteurs Google dans Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="create-text-analytics-resource"></a>Créer une ressource Analyse de texte

Les API Cognitive Services sont disponibles dans Azure en tant que ressources individuelles. Utilisez l’API Analyse de texte pour détecter le sentiment des tweets postés.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sélectionnez **Créer une ressource** dans le coin supérieur gauche du portail Azure.

1. Sous _Catégories_, sélectionnez **IA + Machine Learning**.

1. Sous _Analyse de texte_, sélectionnez **Créer**.

1. Entrez les valeurs suivantes dans l’écran _Créer une analyse de texte_.

    | Paramètre | Valeur | Notes  |
    | ------- | ----- | ------- |
    | Abonnement | Le nom de votre abonnement Azure | |
    | Groupe de ressources | Créez un groupe de ressources nommé **tweet-sentiment-tutorial** | Par la suite, vous supprimerez ce groupe de ressources pour supprimer toutes les ressources créées pendant ce tutoriel. |
    | Région | Sélectionnez la région la plus proche de vous. | |
    | Nom | **TweetSentimentApp** | |
    | Niveau tarifaire | Sélectionnez **Gratuit F0** | |

1. Sélectionnez **Revoir + créer**.

1. Sélectionnez **Create** (Créer).

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

## <a name="get-text-analytics-settings"></a>Obtenir les paramètres Analyse de texte

Une fois la ressource Analyse de texte créée, vous allez copier quelques paramètres et les mettre de côté pour une utilisation ultérieure.

1. Sélectionnez **Clés et point de terminaison**.

1. Copiez la **Clé 1** en cliquant sur l’icône située à la fin de la zone d’entrée.

1. Collez la valeur dans un éditeur de texte.

1. Copiez le **Point de terminaison** en cliquant sur l’icône située à la fin de la zone d’entrée.

1. Collez la valeur dans un éditeur de texte.

## <a name="create-the-function-app"></a>Créer l’application de fonction

1. Dans la zone de recherche située en haut, recherchez et sélectionnez **Application de fonction**.

1. Sélectionnez **Create** (Créer).

1. Saisissez les valeurs suivantes.

    | Paramètre | Valeur suggérée | Notes  |
    | ------- | ----- | ------- |
    | Abonnement | Le nom de votre abonnement Azure | |
    | Groupe de ressources | **tweet-sentiment-tutorial** | Utilisez le même nom de groupe de ressources tout au long de ce tutoriel. |
    | Nom de l’application de fonction | **TweetSentimentAPI** + un suffixe unique | Les noms d’application de fonction sont globalement uniques. Les caractères valides sont `a-z` (insensible à la casse), `0-9`et `-`. |
    | Publier | **Code** | |
    | Pile d’exécution | **.NET** | Le code de fonction fourni pour vous est en C#. |
    | Version | Sélectionnez le numéro de version le plus récent | |
    | Région | Sélectionnez la région la plus proche de vous. | |

1. Sélectionnez **Revoir + créer**.

1. Sélectionnez **Create** (Créer).

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

## <a name="create-an-http-triggered-function"></a>Créer une fonction déclenchée via HTTP  

1. Dans le menu de gauche de la fenêtre _Fonctions_, sélectionnez **Fonctions**.

1. Sélectionnez **Ajouter** dans le menu du haut, puis entrez les valeurs suivantes.

    | Paramètre | Valeur | Notes  |
    | ------- | ----- | ------- |
    | Environnement de développement | **Développer dans le portail** | |
    | Modèle | **Déclencheur HTTP** | |
    | Nouvelle fonction | **TweetSentimentFunction** | Il s’agit du nom de votre fonction. |
    | Niveau d’autorisation | **Fonction** | |

1. Sélectionnez le bouton **Ajouter**.

1. Sélectionnez le bouton **Code + test**.

1. Collez le code suivant dans la fenêtre d’éditeur de code.

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
    
        string requestBody = String.Empty;
        using (StreamReader streamReader =  new  StreamReader(req.Body))
        {
            requestBody = await streamReader.ReadToEndAsync();
        }
    
        dynamic score = JsonConvert.DeserializeObject(requestBody);
        string value = "Positive";
    
        if(score < .3)
        {
            value = "Negative";
        }
        else if (score < .6) 
        {
            value = "Neutral";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(value)
           : new BadRequestObjectResult("Pass a sentiment score in the request body.");
    }
    ```

    Un score de sentiment est transmis à la fonction, qui retourne un nom de catégorie pour la valeur.

1. Sélectionnez le bouton **Enregistrer** dans la barre d’outils pour enregistrer vos changements.

    > [!NOTE]
    > Pour tester la fonction, sélectionnez **Tester/Exécuter** dans le menu du haut. Sous l’onglet _Entrée_, entrez la valeur `0.9` dans la zone de texte _Corps_, puis sélectionnez **Exécuter**. Vérifiez que la valeur _Positif_ est retournée dans la zone _Contenu de la réponse HTTP_ dans la section _Sortie_.

Ensuite, créez une application logique qui s’intègre à Azure Functions, à Twitter et à l’API Cognitive Services.

## <a name="create-a-logic-app"></a>Créer une application logique

1. Dans la zone de recherche située en haut, recherchez et sélectionnez **Applications logiques**.

1. Sélectionnez **Ajouter**.

1. Sélectionnez **Consommation**, puis entrez les valeurs suivantes.

    | Paramètre | Valeur suggérée |
    | ------- | --------------- |
    | Abonnement | Le nom de votre abonnement Azure |
    | Groupe de ressources | **tweet-sentiment-tutorial** |
    | Nom de l’application logique | **TweetSentimentApp** |
    | Région | Sélectionnez la région la plus proche de vous, de préférence la même région que celle que vous avez sélectionnée lors des étapes précédentes. |

    Acceptez les valeurs par défaut pour tous les autres paramètres.

1. Sélectionnez **Revoir + créer**.

1. Sélectionnez **Create** (Créer).

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

1. Sélectionnez le bouton **Application logique vide**.

    :::image type="content" source="media/functions-twitter-email/blank-logic-app-button.png" alt-text="Bouton Application logique vide":::

1. Sélectionnez le bouton **Enregistrer** dans la barre d’outils pour enregistrer votre progression.

Vous pouvez maintenant utiliser le Concepteur Logic Apps pour ajouter des services et des déclencheurs à votre application.

## <a name="connect-to-twitter"></a>Se connecter à Twitter

Créez une connexion à Twitter pour que votre application puisse déterminer s’il y a de nouveaux tweets.

1. Recherchez **Twitter** dans la zone de recherche située en haut.

1. Sélectionnez l’icône **Twitter**.

1. Sélectionnez le déclencheur **Lors de la publication d’un nouveau tweet**.

1. Entrez les valeurs suivantes pour configurer la connexion.

    | Paramètre |  Valeur |
    | ------- | ---------------- |
    | Nom de la connexion | **MyTwitterConnection** |
    | Type d’authentification | **Utilisez l’application partagée par défaut** |

1. Sélectionnez **Connexion**.

1. Suivez les invites qui s’affichent dans la fenêtre contextuelle pour effectuer la connexion à Twitter.

1. Ensuite, entrez les valeurs suivantes dans la zone _Lors de la publication d’un nouveau tweet_.

    | Paramètre | Valeur |
    | ------- | ----- |
    | Search text (Texte de recherche) | **#my-twitter-tutorial** |
    | À quelle fréquence voulez-vous vérifier les éléments ? | **1** dans la zone de texte, et <br> **Heure** dans la liste déroulante. Vous pouvez entrer des valeurs différentes, mais veillez à passer en revue les [limitations](https://docs.microsoft.com/connectors/twitterconnector/#limits) actuelles du connecteur Twitter.  |

1. Sélectionnez le bouton **Enregistrer** dans la barre d’outils pour enregistrer votre progression.

Ensuite, connectez-vous à l’analyse de texte pour détecter les sentiments des tweets collectés.

## <a name="add-text-analytics-sentiment-detection"></a>Ajouter la détection de sentiments de l’Analyse de texte

1. Sélectionnez **Nouvelle étape**.

1. Recherchez **Analyse de texte** dans la zone de recherche.

1. Sélectionnez l’icône **Analyse de texte**.

1. Sélectionnez **Détecter le sentiment**, puis entrez les valeurs suivantes.

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom de la connexion | **TextAnalyticsConnection** |
    | Clé du compte | Collez la clé de compte Analyse de texte que vous avez mis de côté précédemment. |
    | URL du site | Collez le point de terminaison Analyse de texte que vous avez mis de côté précédemment. |

1. Sélectionnez **Create** (Créer).

1. Cliquez dans la zone _Ajouter un nouveau paramètre_, puis cochez la case en regard de **Documents** qui s’affiche dans la fenêtre contextuelle.

1. Cliquez dans la zone de texte _documents id - 1_ (ID de documents - 1) pour ouvrir la fenêtre contextuelle de contenu dynamique.

1. Dans la zone de recherche _dynamic content_ (contenu dynamique), recherchez **id**, puis cliquez sur **Tweet id** (ID du tweet).

1. Cliquez dans la zone de texte _documents Text - 1_ (Texte de documents - 1) pour ouvrir la fenêtre contextuelle de contenu dynamique.

1. Dans la zone de recherche _dynamic content_ (contenu dynamique), recherchez **text** (texte), puis cliquez sur **Tweet text** (Texte du tweet).

1. Dans **Choisir une action**, saisissez **Analyse de texte**, puis cliquez sur l’action **Détecter le sentiment**.

1. Sélectionnez le bouton **Enregistrer** dans la barre d’outils pour enregistrer votre progression.

La zone _Détecter le sentiment_ doit ressembler à la capture d’écran suivante.

:::image type="content" source="media/functions-twitter-email/detect-sentiment.png" alt-text="Détecter le sentiment":::

## <a name="connect-sentiment-output-to-function-endpoint"></a>Connecter une sortie de sentiment à un point de terminaison de fonction

1. Sélectionnez **Nouvelle étape**.

1. Recherchez **Azure Functions** dans la zone de recherche.

1. Sélectionnez l’icône **Azure Functions**.

1. Recherchez le nom de votre fonction dans la zone de recherche. Si vous avez suivi les instructions ci-dessus, le nom de votre fonction commence par **TweetSentimentAPI**.

1. Sélectionnez l’icône de fonction.

1. Sélectionnez l’élément **TweetSentimentFunction**.

1. Cliquez dans la zone _Corps de la demande_, puis sélectionnez l’élément _Détecter le sentiment_ **score** dans la fenêtre contextuelle.

1. Sélectionnez le bouton **Enregistrer** dans la barre d’outils pour enregistrer votre progression.

## <a name="add-conditional-step"></a>Ajouter une étape conditionnelle

1. Sélectionnez le bouton **Ajouter une action**.

1. Cliquez dans la zone _Contrôle_, puis recherchez et sélectionnez **Contrôle** dans la fenêtre contextuelle.

1. Sélectionnez **Condition**.

1. Cliquez dans la zone _Choisir une valeur_, puis sélectionnez l’élément _TweetSentimentFunction_ **Corps** dans la fenêtre contextuelle.

1. Entrez **Positif** dans la zone _Choisir une valeur_.

1. Sélectionnez le bouton **Enregistrer** dans la barre d’outils pour enregistrer votre progression.

## <a name="add-email-notifications"></a>Ajouter des notifications par courrier électronique

1. Sous la zone _Vrai_, sélectionnez le bouton **Ajouter une action**.

1. Recherchez et sélectionnez **Office 365 Outlook** dans la zone de texte.

1. Recherchez **Envoyer**, puis sélectionnez **Envoyer un e-mail** dans la zone de texte.

1. Sélectionnez le bouton **Se connecter**.

1. Suivez les invites qui s’affichent dans la fenêtre contextuelle pour effectuer la connexion à Office 365 Outlook.

1. Entrez votre adresse e-mail dans la zone _À_.

1. Cliquez dans la zone _Objet_, puis sur l’élément **Corps** sous _TweetSentimentFunction_. Si l’élément _Corps_ n’est pas affiché dans la liste, cliquez sur le lien **Afficher plus** pour développer la liste d’options.

1. Une fois l’élément _Corps_ placé dans l’_Objet_, entrez le texte **Tweet de :** .

1. Après avoir entré le texte _Tweet de :_ , cliquez de nouveau sur la zone, puis sélectionnez **Nom d’utilisateur** dans la liste des options _Lors de la publication d’un nouveau tweet_.

1. Cliquez dans la zone _Corps_, puis sélectionnez le **Texte du tweet** sous la liste des options _Lors de la publication d’un nouveau tweet_. Si l’élément _Texte du tweet_ n’est pas affiché dans la liste, cliquez sur le lien **Afficher plus** pour développer la liste d’options.

1. Sélectionnez le bouton **Enregistrer** dans la barre d’outils pour enregistrer votre progression.

La boîte e-mail doit maintenant ressembler à cette capture d’écran.

:::image type="content" source="media/functions-twitter-email/email-notification.png" alt-text="Notification par e-mail":::

## <a name="run-the-workflow"></a>Exécuter le workflow

1. À partir de votre compte Twitter, tweetez le texte suivant : **I’m enjoying #my-twitter-tutorial** (J’adore #mon-tutoriel-twitter).

1. Retournez au Concepteur Logic Apps, puis sélectionnez le bouton **Exécuter**.

1. Consultez vos e-mails pour voir si vous avez reçu un message du workflow.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour nettoyer tous les services et comptes Azure créés lors de ce tutoriel, supprimez le groupe de ressources.

1. Recherchez **Groupes de ressources** dans la zone de recherche située en haut.

1. Sélectionnez le tutoriel **tweet-sentiment-tutorial** (tutoriel sur le sentiment des tweets).

1. Sélectionnez **Supprimer le groupe de ressources**.

1. Entrez **tweet-sentiment-tutorial** dans la zone de texte.

1. Sélectionnez le bouton **Supprimer**.

Si vous le souhaitez, vous pouvez retourner dans votre compte Twitter et supprimer tous les tweets de test de votre flux.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une API sans serveur à l’aide d’Azure Functions](functions-create-serverless-api.md)
