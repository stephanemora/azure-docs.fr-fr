---
title: 'Démarrage rapide : Utiliser le service Azure Web PubSub pour créer une application serverless'
description: Guide de démarrage rapide sur l’utilisation du service Azure Web PubSub et d’Azure Functions pour créer une application serverless.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 03/11/2021
ms.openlocfilehash: 573e0dc028391c2eea9d412bfe68c07a2e95aec3
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963137"
---
# <a name="quickstart-create-a-serverless-simple-chat-application-with-azure-functions-and-azure-web-pubsub-service"></a>Démarrage rapide : Créer une application de conversation simple serverless avec Azure Functions et le service Azure Web PubSub 

Le service Azure Web PubSub vous aide à créer facilement des applications web de messagerie en temps réel avec des WebSockets et le modèle publication-abonnement. Azure Functions est une plateforme serverless qui vous permet d’exécuter votre code sans gérer d’infrastructures. Dans ce guide de démarrage rapide, découvrez comment utiliser le service Azure Web PubSub et Azure Functions pour créer une application serverless avec une messagerie en temps réel et le modèle publication-abonnement.

## <a name="prerequisites"></a>Prérequis

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Installez un éditeur de code, tel que [Visual Studio Code](https://code.visualstudio.com/), ainsi que la bibliothèque [Node.js](https://nodejs.org/en/download/), version 10.x.

   > [!NOTE]
   > Pour plus d’informations sur les versions prises en charge de Node.js, consultez la [documentation sur les versions du runtime d’Azure Functions](../azure-functions/functions-versions.md#languages).

Installez [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (version 2.7.1505 ou ultérieure) pour exécuter des applications Azure Function en local.

---

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Pendant le déploiement du service, passons à l’utilisation du code. Clonez l’[exemple d’application à partir de GitHub](https://github.com/Azure/azure-webpubsub/tree/main/samples/functions/js/simplechat) en guise de première étape.

1. Ouvrez une fenêtre de terminal git. Accédez au dossier dans lequel vous souhaitez cloner l’exemple de projet.

1. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur.

    ```bash
    git clone https://github.com/Azure/azure-webpubsub.git
    ```

## <a name="configure-and-run-the-azure-function-app"></a>Configurer et exécuter l’application de fonction Azure

- Dans le navigateur, ouvrez le **portail Azure** et vérifiez que l’instance de service Web PubSub que vous avez déployée a été créée. Accédez à l’instance.
- Sélectionnez **Clés** et copiez la chaîne de connexion.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

- Mettez à jour la configuration de la fonction.

  Ouvrez le dossier */samples/functions/js/simplechat* dans le dépôt cloné. Modifiez *local.settings.json* pour ajouter une chaîne de connexion de service.
  Dans *local.settings.js*, apportez les modifications suivantes, puis enregistrez le fichier.
    - Remplacez l’espace réservé *<connection-string>* par la chaîne de connexion copiée à partir du **portail Azure** pour le paramètre **`WebPubSubConnectionString`** . 
    - Pour le paramètre **`AzureWebJobsStorage`** , cela est nécessaire, car [Azure Functions nécessite un compte de stockage Azure](../azure-functions/storage-considerations.md).
        - Si l’émulateur de stockage Azure est exécuté localement, conservez les paramètres d’origine « UseDevelopmentStorage=true ».
        - Si vous disposez d’une chaîne de connexion de stockage Azure, remplacez la valeur par celle-ci.
 
- Les fonctions JavaScript sont organisées dans des dossiers. Deux fichiers se trouvent dans chaque dossier : le fichier `function.json` définit les liaisons utilisées dans la fonction et le fichier `index.js` correspond au corps de la fonction. Plusieurs fonctions sont déclenchées dans cette application de fonction :

    - **login** : fonction déclenchée par HTTP. Elle utilise la liaison d’entrée *webPubSubConnection* pour générer et retourner des informations de connexion de service valides.
    - **messages** : fonction déclenchée par `WebPubSubTrigger`. Reçoit un message de conversation dans le corps de la demande et utilise la liaison de sortie `WebPubSub` pour diffuser le message à toutes les applications clientes connectées.
    - **connect** et **connected** : fonctions déclenchées par `WebPubSubTrigger`. Gèrent les événements connect et connected.

- Dans le terminal, vérifiez que vous vous trouvez dans le dossier */samples/functions/js/simplechat*. Installez les extensions et exécutez l’application de fonction.

    ```bash
    func extensions install

    func start
    ```

- La fonction locale utilise le port défini dans le fichier `local.settings.json`. Pour le rendre disponible dans un réseau public, vous devez utiliser [ngrok](https://ngrok.com) pour exposer ce point de terminaison. Exécutez la commande ci-dessous afin d’obtenir un point de terminaison de transfert, par exemple : http://{ngrok-id}.ngrok.io -> http://localhost:7071.

    ```bash
    ngrok http 7071
    ```    

- Définissez `Event Handler` dans le service Azure Web PubSub. Accédez au **portail Azure** -> Recherchez votre ressource Web PubSub -> **Paramètres**. Ajoutez un nouveau mappage de paramètres de hub à la fonction en cours d’utilisation comme indiqué ci-dessous. Remplacez le {ngrok-id} par le vôtre.

   - Nom du hub : simplechat
   - Modèle d’URL : **http://{ngrok-id}.ngrok.io/runtime/webhooks/webpubsub**
   - Modèle d’événement utilisateur : *
   - Événements système : connect, connected, disconnected.

---

## <a name="run-the-web-application"></a>Exécuter l’application web

1. Pour simplifier le test de vos clients, ouvrez votre navigateur sur notre exemple d’[application web monopage](http://jialinxin.github.io/webpubsub/). 

1. Entrez l’URL de base de l’application de fonction dans sa forme locale : `http://localhost:7071`.

1. Entrez un nom d’utilisateur.

1. L’application web appelle la fonction *login* dans l’application de fonction afin de récupérer les informations de connexion nécessaires pour se connecter au service Azure Web PubSub. L’affichage de `Client websocket opened.` signifie que la connexion est établie. 

1. Tapez un message puis appuyez sur Entrée. L’application envoie le message à la fonction *messages* dans l’application Azure Function, qui se sert ensuite de la liaison de sortie Web PubSub pour diffuser le message à tous les clients connectés. Si tout fonctionne normalement, le message s’affiche dans l’application.

1. Ouvrez une autre instance de l’application web dans une fenêtre de navigation différente. Vous verrez que tous les messages envoyés s’affichent dans toutes les instances de l’application.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez toutes les ressources créées dans le cadre de cette documentation en procédant de la façon suivante afin d’éviter des frais :

1. Dans le portail Azure, sélectionnez **Groupes de ressources** tout à gauche, puis sélectionnez le groupe de ressources que vous avez créé. Si vous préférez, vous pouvez utiliser la zone de recherche pour trouver le groupe de ressources grâce à son nom.

1. Dans la fenêtre qui s’ouvre, sélectionnez le groupe de ressources, puis sélectionnez **Supprimer le groupe de ressources**.

1. Dans la nouvelle fenêtre, tapez le nom du groupe de ressources à supprimer, puis sélectionnez **Supprimer**.