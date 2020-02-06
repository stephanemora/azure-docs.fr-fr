---
title: Créer une application de fonction sur Linux à l’aide du portail Azure
description: Apprenez à créer votre première fonction Azure pour une exécution sans serveur à l’aide du portail Azure.
ms.topic: quickstart
ms.date: 02/28/2019
ms.openlocfilehash: 1492188f72eb4a691ddceb78aa269601f192e467
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963833"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Créer une application de fonction dans un plan App Service sur Linux

Azure Functions vous permet d’héberger vos fonctions sur Linux dans un conteneur Azure App Service par défaut. Cet article vous montre comment utiliser le [portail Azure](https://portal.azure.com) pour créer une application de fonction Linux dans Azure, qui s’exécute dans un [plan App Service](functions-scale.md#app-service-plan). Vous pouvez également [apporter votre propre conteneur personnalisé](functions-create-function-linux-custom-image.md).

![Créer une Function App dans le Portail Azure](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur <https://portal.azure.com> avec votre compte Azure.

## <a name="create-a-function-app"></a>Créer une application de fonction

Vous devez disposer d’une application de fonction pour héberger l’exécution de vos fonctions sur Linux. L’application de fonction fournit un environnement pour l’exécution de votre code de fonction. Elle vous permet de regrouper les fonctions en une unité logique pour faciliter la gestion, le déploiement, la mise à l’échelle et le partage des ressources. Dans cet article, vous créez un plan App Service lorsque vous créez votre application de fonction.

1. Cliquez sur le bouton **Créer une ressource** en haut à gauche du portail Azure, puis sélectionnez **Calcul** > **Application de fonction**.

    ![Créer une application de fonction sur le Portail Azure](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Utilisez les paramètres d’application de fonction comme indiqué dans le tableau sous l’image.

    ![Définir de nouveaux paramètres d’application de fonction](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom de l’application** | Nom globalement unique | Nom qui identifie votre nouvelle Function App. Les caractères valides sont `a-z`, `0-9` et `-`.  | 
    | **Abonnement** | Votre abonnement | Abonnement sous lequel est créée cette nouvelle application de fonction. | 
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nom du nouveau groupe de ressources dans lequel créer votre Function App. |
    | **SE** | Linux | L’application de fonction s’exécute sur Linux. |
    | **Publier** | Code | Le conteneur Linux par défaut pour votre **pile d’exécution** est utilisé. Tout ce que vous avez à fournir est le code de projet de votre application de fonction. Une autre option consiste à publier une [image Docker](functions-create-function-linux-custom-image.md) personnalisée. |
    | **[Plan d’hébergement](functions-scale.md)** | Plan App Service | Plan d’hébergement qui définit la façon dont les ressources sont allouées à votre Function App. Si vous exécutez dans un plan App Service, vous pouvez gérer la [mise à l’échelle de votre application de fonction](functions-scale.md).  |
    | **Plan/lieu App Service** | Créer un plan | Choisissez **Créer** et fournissez le nom du **plan App Service**. Choisissez un **emplacement** dans une [région](https://azure.microsoft.com/regions/) proche de chez vous, ou proche d’autres services auxquels vos fonctions ont accès. Choisissez votre **[niveau tarifaire](https://azure.microsoft.com/pricing/details/app-service/linux/)** . <br/>Vous ne pouvez pas exécuter des applications de fonction Linux et Windows dans le même plan App Service. |
    | **Pile d’exécution** | Langage préféré | Choisissez un runtime qui prend en charge votre langage de programmation de fonction favori. Choisissez **.NET** pour les fonctions C# et F#. |
    | **[Stockage](../storage/common/storage-account-create.md)** |  Nom globalement unique |  Créez un compte de stockage utilisé par votre application de fonction. Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres. Vous pouvez également utiliser un compte existant qui doit répondre aux [exigences relatives aux comptes de stockage](storage-considerations.md#storage-account-requirements). |
    | **[Application Insights](functions-monitoring.md)** | activé | Application Insights est déactivé par défaut. Nous vous recommandons d’activer l’intégration d’Application Insights maintenant et de choisir un emplacement près de votre emplacement de plan App Service. Si vous souhaitez le faire ultérieurement, consultez [Surveiller Azure Functions](functions-monitoring.md).  |

3. Sélectionnez **Créer** pour configurer et déployer l’application de fonction.

4. Cliquez sur l’icône Notification en haut à droite du portail pour voir le message **Le déploiement a été effectué**.

    ![Définir de nouveaux paramètres d’application de fonction](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Sélectionnez **Accéder à la ressource** pour afficher votre nouvelle application de fonction.

Créez ensuite une fonction dans la nouvelle Function App. Même une fois votre application de fonction disponible, il peut s’écouler quelques minutes avant qu’elle ne soit entièrement initialisée.

## <a name="create-function"></a>Créer une fonction déclenchée via HTTP

Cette section vous montre comment créer une fonction dans votre nouvelle application de fonction dans le portail.

> [!NOTE]
> L’expérience de développement du portail peut être utile pour tester Azure Functions. Pour la plupart des scénarios, envisagez de développer vos fonctions localement et de publier le projet dans votre application de fonction à l’aide de [Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project) ou d’[Azure Functions Core Tools](functions-run-local.md#create-a-local-functions-project).  

1. Dans votre nouvelle application de fonction, choisissez l’onglet **Vue d’ensemble** et une fois qu’il est complètement chargé, choisissez **+Nouvelle fonction**.

    ![Créer une nouvelle fonction à partir de l’onglet Vue d’ensemble](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. Dans l’onglet **Démarrage rapide**, choisissez **Dans le portail**, puis sélectionnez **Continuer**.

    ![Choisissez votre plateforme de développement.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

1. Choisissez **WebHook + API**, puis sélectionnez **Créer**.

    ![Démarrage rapide de fonctions dans le portail Azure.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-node-webhook.png)

Une fonction est créée à l’aide d’un modèle spécifique à une langue pour une fonction déclenchée via HTTP.

Vous pouvez maintenant exécuter la nouvelle fonction en envoyant une requête HTTP.

## <a name="test-the-function"></a>Tester la fonction

1. Dans votre nouvelle fonction, cliquez sur **</> Obtenir l’URL de la fonction** en haut à droite, sélectionnez **par défaut (touche de fonction)** , puis cliquez sur **Copier**. 

    ![Copier l’URL de fonction à partir du portail Azure](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

2. Collez l’URL de fonction dans la barre d’adresse de votre navigateur. Ajoutez la valeur de la chaîne de requête `&name=<yourname>` à la fin de cette URL et appuyez sur la touche `Enter` de votre clavier pour exécuter la requête. Vous devez voir la réponse renvoyée par la fonction affichée dans le navigateur.  

    L’exemple suivant montre la réponse dans le navigateur :

    ![Réponse de la fonction dans le navigateur.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    L’URL de demande inclut une clé qui est requise, par défaut, pour accéder à votre fonction sur HTTP.

3. Lorsque votre fonction s’exécute, des informations de suivi sont écrites dans les journaux d’activité. Pour afficher la sortie de suivi de l’exécution précédente, revenez à votre fonction dans le portail et cliquez sur la flèche figurant en bas de l’écran pour développer **Journaux d’activité**.

   ![Affichage des journaux de fonction dans le portail Azure.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé une Function App avec une simple fonction déclenchée via HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Pour plus d’informations, consultez [Liaisons HTTP Azure Functions](functions-bindings-http-webhook.md).
