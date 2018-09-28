---
title: 'Didacticiel : Authentification de service SignalR Azure avec Azure Functions | Microsoft Docs'
description: Dans ce didacticiel, vous allez découvrir comment authentifier les clients du service Azure SignalR.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/18/2018
ms.author: zhshang
ms.openlocfilehash: 8af657c39217f3edcadef6ec0981a31ec7e89aa6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978417"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>Didacticiel : Authentification de service SignalR Azure avec Azure Functions

Didacticiel étape par étape permettant de créer une salle de conversation avec l’authentification et la messagerie privée à l’aide d’Azure Functions, de l’authentification App Service et du service SignalR.

## <a name="introduction"></a>Introduction

### <a name="technologies-used"></a>Technologies utilisées

* [Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu) : API principale pour l’authentification des utilisateurs et l’envoi de messages de conversation
* [Service Azure SignalR](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu) : diffusez de nouveaux messages auprès des clients connectés à la conversation
* [Stockage Azure](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu) : hébergez le site web statique pour l’interface utilisateur cliente de la conversation

### <a name="prerequisites"></a>Prérequis

Les logiciels suivants sont nécessaires pour ce didacticiel.

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/) (version 10.x)
* [Kit de développement logiciel (SDK) .NET](https://www.microsoft.com/net/download) (version 2.x, requis pour les extensions de Functions)
* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools) (version 2)
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code) avec les extensions suivantes
    * [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) : travaillez avec Azure Functions dans VS Code
    * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) : fournissez des pages web en local pour les tests


## <a name="sign-into-the-azure-portal"></a>Se connecter au portail Azure

Accédez au [portail Azure ](https://portal.azure.com/) et connectez-vous à l’aide de vos informations d’identification.


## <a name="create-an-azure-signalr-service-instance"></a>Créer une instance de service SignalR Azure

Vous générez et testez l’application Azure Functions localement. L’application accède à une instance de service SignalR dans Azure, qui doit être créée à l’avance.

1. Cliquez sur le bouton **Créer une ressource** (**+**) pour créer une ressource Azure.

1. Recherchez **Service SignalR** et sélectionnez-le. Cliquez sur **Créer**.

    ![Nouveau service SignalR](media/signalr-authenticate-azure-functions/signalr-quickstart-new.png)

1. Entrez les informations ci-après.

    | NOM | Valeur |
    |---|---|
    | Nom de la ressource | Nom unique pour l’instance de service SignalR |
    | Groupe de ressources | Création d’un groupe de ressources |
    | Lieu | Choisissez un emplacement proche de vous |
    | Niveau de tarification | Gratuit |
    
1. Cliquez sur **Créer**.


## <a name="initialize-the-function-app"></a>Initialiser l’application de fonction

### <a name="create-a-new-azure-functions-project"></a>Créer un projet Azure Functions

1. Dans une nouvelle fenêtre VS Code, utilisez `File > Open Folder` dans le menu pour créer et ouvrir un dossier vide à un emplacement approprié. Il s’agit du dossier du projet principal pour l’application que vous allez générer.

1. À l’aide de l’extension Azure Functions dans VS Code, initialisez une application de fonction dans le dossier du projet principal.
    1. Ouvrez la palette de commandes dans VS Code en sélectionnant **Afficher > Palette de commandes** à partir du menu (raccourci `Ctrl-Shift-P`, macOS : `Cmd-Shift-P`).
    1. Recherchez la commande **Azure Functions : Créer un projet** et sélectionnez-la.
    1. Le dossier du projet principal doit apparaître. Sélectionnez-le (ou utilisez « Parcourir » pour le localiser).
    1. Dans l’invite de choix du langage, sélectionnez **JavaScript**.

    ![Créer une application de fonction](media/signalr-authenticate-azure-functions/signalr-create-vscode-app.png)


### <a name="install-function-app-extensions"></a>Installer des extensions d’application de fonction

Ce didacticiel utilise les liaisons Azure Functions pour interagir avec le service SignalR Azure. Comme la plupart des autres liaisons, les liaisons du service SignalR sont disponibles en tant qu’extension à installer avec la CLI Azure Functions Core Tools avant de pouvoir être utilisées.

1. Ouvrez un terminal dans VS Code en sélectionnant **Affichage > Terminal intégré** à partir du menu (Ctrl-`).

1. Vérifiez que le dossier du projet principal est le répertoire actif.

1. Installez l’extension d’application de fonction de service SignalR.
    ```
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0-preview1-10002
    ```

### <a name="configure-application-settings"></a>Configurer les paramètres de l’application

Lors de l’exécution et du débogage du runtime Azure Functions en local, les paramètres d’application sont lus à partir de **local.settings.json**. Mettez à jour ce fichier avec la chaîne de connexion de l’instance de service SignalR que vous avez créée précédemment.

1. Dans VS Code, sélectionnez **local.settings.json** dans le volet de l’Explorateur pour l’ouvrir.

1. Remplacez le contenu du fichier par ce qui suit.
    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.0.0",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "*"
        }
    }
    ```

    * Entrez la chaîne de connexion de service SignalR Azure dans un paramètre nommé `AzureSignalRConnectionString`. Obtenez la valeur dans la page **Clés** de la ressource de service SignalR Azure sur le portail Azure. La chaîne de connexion principale ou secondaire peut être utilisée.
    * Le paramètre `WEBSITE_NODE_DEFAULT_VERSION` n’est pas utilisé localement, mais il est requis lors du déploiement vers Azure.
    * La section `Host` configure le port et les paramètres CORS pour l’hôte Functions local (ce paramètre n’a aucun effet lors de l’exécution dans Azure).

    ![Obtenir la clé de service SignalR](media/signalr-authenticate-azure-functions/signalr-get-key.png)

1. Enregistrez le fichier .

    ![Mettre à jour les paramètres locaux](media/signalr-authenticate-azure-functions/signalr-update-local-settings.png)


## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>Créer une fonction pour authentifier les utilisateurs du service SignalR

Lorsque l’application de conversation s’ouvre pour la première fois dans le navigateur, elle demande des informations d’identification valides pour se connecter au service SignalR Azure. Vous allez créer une fonction déclenchée par le biais de HTTP et nommée *SignalRInfo* dans votre application de fonction pour retourner ces informations de connexion.

1. Ouvrez la palette de commandes VS Code (`Ctrl-Shift-P`, macOS : `Cmd-Shift-P`).

1. Recherchez et sélectionnez la commande **Azure Functions : Créer une fonction**.

1. À l’invite, fournissez les informations suivantes.

    | NOM | Valeur |
    |---|---|
    | Dossier d’application de fonction | Sélectionnez le dossier de projet principal |
    | Modèle | Déclencheur HTTP |
    | NOM | SignalRInfo |
    | Niveau d’autorisation | Anonyme |
    
    Un dossier nommé **SignalRInfo** est créé : il contient la nouvelle fonction.

1. Ouvrez **SignalRInfo/function.json** afin de configurer les liaisons pour la fonction. Modifiez le contenu du fichier comme suit. Cette opération ajoute une liaison d’entrée qui génère des informations d’identification valides pour qu’un client se connecte à un hub de service SignalR Azure nommé `chat`.

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    La propriété `userId` dans la liaison `signalRConnectionInfo` permet de créer une connexion authentifiée au service SignalR. Laissez la propriété vide pour le développement local. Vous allez l’utiliser lors du déploiement de l’application de fonction vers Azure.

1. Ouvrez **SignalRInfo/index.js** pour afficher le corps de la fonction. Modifiez le contenu du fichier comme suit.

    ```javascript
    module.exports = function (context, req, connectionInfo) {
        context.res = { body: connectionInfo };
        context.done();
    };
    ```

    Cette fonction tire les informations de connexion SignalR de la liaison d’entrée et les retourne au client dans le corps de réponse HTTP.


## <a name="create-a-function-to-send-chat-messages"></a>Créer une fonction pour envoyer des messages de conversation

L’application web nécessite également une API HTTP pour envoyer des messages de conversation. Vous allez créer une fonction déclenchée par le biais de HTTP et nommée *SendMessage*, qui envoie des messages à tous les clients connectés à l’aide du service SignalR.

1. Ouvrez la palette de commandes VS Code (`Ctrl-Shift-P`, macOS : `Cmd-Shift-P`).

1. Recherchez et sélectionnez la commande **Azure Functions : Créer une fonction**.

1. À l’invite, fournissez les informations suivantes.

    | NOM | Valeur |
    |---|---|
    | Dossier d’application de fonction | sélectionnez le dossier de projet principal |
    | Modèle | Déclencheur HTTP |
    | NOM | SendMessage |
    | Niveau d’autorisation | Anonyme |
    
    Un dossier nommé **SendMessage** est créé : il contient la nouvelle fonction.

1. Ouvrez **SendMessage/function.json** afin de configurer les liaisons pour la fonction. Modifiez le contenu du fichier comme suit.
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "signalRMessages",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    Cela apporte deux modifications à la fonction d’origine :
    * Modification de l’itinéraire vers `messages` et restriction du déclencheur HTTP à la méthode HTTP **POST**.
    * Ajout d’une liaison de sortie pour le service SignalR, qui envoie un ou plusieurs messages à tous les clients connectés à un hub de service SignalR nommé `chat`.

1. Enregistrez le fichier .

1. Ouvrez **SendMessage/index.js** pour afficher le corps de la fonction. Modifiez le contenu du fichier comme suit.
    ```javascript
    module.exports = function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';
            
        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }
    
        context.bindings.signalRMessages = [{
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        }];
        context.done();
    };
    ```
    Cette fonction prend le corps de la requête HTTP et l’envoie aux clients connectés au service SignalR, en appelant une fonction nommée `newMessage` sur chaque client.

    La fonction peut lire l’identité de l’expéditeur et accepter la valeur d’un *destinataire* dans le corps du message pour permettre l’envoi privé d’un message à un seul utilisateur. Ces fonctionnalités sont utilisées dans la suite du didacticiel.

1. Enregistrez le fichier .


## <a name="create-and-run-the-chat-client-web-user-interface"></a>Créer et exécuter l’interface utilisateur web client de conversation

L’interface utilisateur de l’application de conversation est une application monopage (SPA) créée avec l’infrastructure Vue JavaScript. Elle est hébergée séparément de l’application de fonction. Localement, vous exécutez l’interface web à l’aide de l’extension Live Server VS Code.

1. Dans VS Code, créez un dossier nommé **content** à la racine du dossier du projet principal.

1. Dans le dossier **content**, créez un fichier nommé **index.html**.

1. Copiez et collez le contenu de **[index.html](https://raw.githubusercontent.com/Azure-Samples/signalr-service-quickstart-serverless-chat/master/docs/demo/chat-with-auth/index.html)**.

1. Enregistrez le fichier .

1. Appuyez sur **F5** pour exécuter l’application de fonction localement et joindre un débogueur.

1. Alors que le fichier **index.html** est ouvert, démarrez Live Server en ouvrant la palette de commandes VS Code (`Ctrl-Shift-P`, macOS : `Cmd-Shift-P`) et en sélectionnant **Live Server : Ouvrir avec Live Server**. Live Server ouvre l’application dans un navigateur.

1. L’application s’ouvre. Entrez un message dans la zone de conversation et appuyez sur Entrée. Actualisez l’application pour voir les nouveaux messages. Comme aucune authentification n’a été configurée, tous les messages sont envoyés sous l’étiquette « anonyme ».


## <a name="deploy-to-azure-and-enable-authentication"></a>Procéder à un déploiement vers Azure et activer l’authentification

Vous avez exécuté l’application de fonction et l’application de conversation localement. Vous allez à présent les déployer vers Azure et activer l’authentification ainsi que la messagerie privée dans l’application.


### <a name="log-into-azure-with-vs-code"></a>Se connecter à Azure avec VS Code

1. Ouvrez la palette de commandes VS Code (`Ctrl-Shift-P`, macOS : `Cmd-Shift-P`).

1. Recherchez et sélectionnez la commande **Azure : Connexion**.

1. Suivez les instructions pour terminer le processus de connexion dans votre navigateur.


### <a name="configure-function-app-for-authentication"></a>Configurer l’authentification pour l’application de fonction

Jusqu’ici, l’application de conversation fonctionne de façon anonyme. Dans Azure, vous allez utiliser l’[authentification App Service](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) pour authentifier l’utilisateur. L’ID utilisateur ou le nom d’utilisateur de l’utilisateur authentifié peut être transmis à la liaison *SignalRConnectionInfo* pour générer des informations de connexion qui authentifient l’utilisateur.

Lors de l’envoi d’un message, l’application peut décider de l’envoyer à tous les clients connectés ou uniquement aux clients qui ont été authentifiés conformément à un utilisateur donné.

1. Dans VS Code, ouvrez **SendMessage/function.json**.

1. Insérez une [expression de liaison](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings#binding-expressions-and-patterns) dans la propriété *userId* de la liaison *SignalRConnectionInfo* : `{headers.x-ms-client-principal-name}`. Elle définit la valeur du nom d’utilisateur de l’utilisateur authentifié. L’attribut devrait maintenant ressembler à ceci.

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. Enregistrez le fichier .


### <a name="deploy-function-app"></a>Déployer l’application de fonction

1. Ouvrez la palette de commandes VS Code (`Ctrl-Shift-P`, macOS : `Cmd-Shift-P`) et sélectionnez **Azure Functions : Déployer vers Function App**. 

1. À l’invite, fournissez les informations suivantes.

    | NOM | Valeur |
    |---|---|
    | Dossier à déployer | Sélectionnez le dossier de projet principal |
    | Abonnement | Sélectionnez votre abonnement |
    | Conteneur de fonctions | Sélectionnez **Créer une Function App** |
    | Nom de l’application de fonction | Entrez un nom unique |
    | Groupe de ressources | Sélectionnez le même groupe de ressources que l’instance de service SignalR |
    | Compte de stockage | Sélectionnez **Créer un compte de stockage** |
    | Nom du compte de stockage | Entrez un nom unique (de 3 à 24 caractères alphanumériques uniquement) |
    | Lieu | Choisissez un emplacement proche de vous |
    
    Une application de fonction est créée dans Azure et le déploiement commence. Attendez la fin du déploiement.


### <a name="upload-function-app-local-settings"></a>Charger les paramètres locaux de l’application de fonction

1. Ouvrez la palette de commandes VS Code (`Ctrl-Shift-P`, macOS : `Cmd-Shift-P`).

1. Recherchez et sélectionnez la commande **Azure Functions : Télécharger les paramètres locaux**.

1. À l’invite, fournissez les informations suivantes.

    | NOM | Valeur |
    |---|---|
    | Fichier de paramètres locaux | local.settings.json |
    | Abonnement | Sélectionnez votre abonnement |
    | Conteneur de fonctions | Sélectionnez l’application de fonction précédemment déployée |
    | Nom de l’application de fonction | Entrez un nom unique |

Les paramètres locaux sont chargés vers l’application de fonction dans Azure. Si vous êtes invité à remplacer les paramètres existants, sélectionnez **Oui pour tout**.


### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>Activer le partage de ressources cross-origin (CORS) pour l’application de fonction

Bien qu’il existe un paramètre CORS dans **local.settings.json**, il n’est pas propagé vers l’application de fonction dans Azure. Vous devez le définir séparément.

1. Ouvrez la palette de commandes VS Code (`Ctrl-Shift-P`, macOS : `Cmd-Shift-P`).

1. Recherchez et sélectionnez la commande **Azure Functions : Ouvrir dans le portail**.

1. Sélectionnez l’abonnement et le nom de l’application de fonction pour ouvrir l’application de fonction dans le portail Azure.

1. Dans le portail ouvert, sous l’onglet **Fonctionnalités de la plateforme**, sélectionnez **CORS**.

    ![Rechercher CORS](media/signalr-authenticate-azure-functions/signalr-find-cors.png)

1. Ajoutez une entrée avec la valeur `*`.

1. Supprimez toutes les autres entrées existantes.

1. Cliquez sur **Enregistrer** pour enregistrer les paramètres CORS.

    ![Définir CORS](media/signalr-authenticate-azure-functions/signalr-set-cors.png)

> [!NOTE]
> Dans une application réelle, au lieu d’autoriser CORS sur toutes les origines (`*`), une approche plus sécurisée consiste à entrer les entrées CORS spécifiques pour chaque domaine le nécessitant.


### <a name="update-the-web-app"></a>Mettre à jour l’application web

1. Dans le portail Azure, accédez à la page de vue d’ensemble de l’application de fonction.

1. Copiez l’URL de l’application de fonction.

    ![Obtenir l’URL](media/signalr-authenticate-azure-functions/signalr-get-url.png)


1. Dans VS Code, ouvrez **index.html** et remplacez la valeur de `apiBaseUrl` par l’URL de l’application de fonction.

1. L’application peut être configurée avec l’authentification à l’aide d’Azure Active Directory, de Facebook, de Twitter, d’un compte Microsoft ou de Google. Sélectionnez le fournisseur d’authentification à utiliser en définissant la valeur de `authProvider`.

1. Enregistrez le fichier .


### <a name="deploy-the-web-application-to-blob-storage"></a>Déployer l’application web sur le stockage blob

L’application web est hébergée à l’aide de la fonctionnalité de sites web statiques Stockage Blob Azure.

1. Cliquez sur le bouton **Nouveau** (**+**) pour créer une ressource Azure.

1. Sous **Stockage**, sélectionnez **Compte de stockage**.

1. Entrez les informations ci-après.

    | NOM | Valeur |
    |---|---|
    | NOM | Nom unique pour le compte de stockage blob |
    | Type de compte | StorageV2 (usage général v2) |
    | Lieu | Sélectionnez la même région que vos autres ressources |
    | Réplication | Stockage localement redondant (LRS) |
    | Performances | standard |
    | Niveau d’accès | À chaud |
    | Groupe de ressources | Sélectionnez le même groupe de ressources que les autres ressources dans ce didacticiel |

1. Cliquez sur **Créer**.

    ![Créer le stockage](media/signalr-authenticate-azure-functions/signalr-create-storage.png)

1. Une fois le compte de stockage créé, ouvrez-le dans le portail Azure.

1. Sélectionnez **Site web statique (préversion)** dans le volet de navigation gauche.

1. Sélectionnez **Activer**.

1. Entrez `index.html` en tant que **nom du document d’index**.

1. Cliquez sur **Enregistrer**.

    ![Configurer des sites statiques](media/signalr-authenticate-azure-functions/signalr-static-websites.png)

1. Cliquez sur le lien **$web** de la page pour ouvrir le conteneur d’objets blob.

1. Cliquez sur **Charger** et chargez **index.html** dans le dossier **content**.

1. Revenez à la page **Site web statique**. Notez le **point de terminaison principal**. Il s’agit de l’URL de votre application web.


### <a name="enable-app-service-authentication"></a>Activer l’authentification App Service

L’authentification App Service prend en charge l’authentification avec Azure Active Directory, Facebook, Twitter, les comptes Microsoft et Google.

1. Alors que l’application de fonction est toujours ouverte dans le portail, accédez à l’onglet **Fonctionnalités de la plateforme**, puis sélectionnez **Authentification/autorisation**.

1. **Activer** l’authentification App Service.

1. Dans **Action à exécuter quand une demande n’est pas authentifiée**, sélectionnez Se connecter avec {fournisseur d’authentification que vous avez sélectionné précédemment}.

1. Dans **URL de redirection externes autorisées**, entrez l’URL du point de terminaison web principal de votre compte de stockage, que vous avez précédemment notée.

1. Suivez la documentation du module fournisseur d’informations de votre choix pour terminer la configuration.

    - [Azure Active Directory](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)
    - [Facebook](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-facebook-authentication)
    - [Twitter](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-twitter-authentication)
    - [Compte Microsoft](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-microsoft-authentication)
    - [Google](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-google-authentication)


### <a name="try-the-application"></a>Tester l’application

1. Dans un navigateur, accédez au point de terminaison web principal du compte de stockage.

1. Sélectionnez **Connexion** pour vous authentifier auprès du fournisseur d’authentification que vous avez choisi.

1. Envoyez des messages publics en les entrant dans la zone principale de conversation.

1. Envoyez des messages privés en cliquant sur un nom d’utilisateur dans l’historique de conversation. Seul le destinataire sélectionné reçoit ces messages.

Félicitations ! Vous avez déployé une application de conversation en temps réel et sans serveur !

![Démonstration](media/signalr-authenticate-azure-functions/signalr-serverless-chat.gif)


## <a name="clean-up-resources"></a>Supprimer des ressources

Pour supprimer les ressources créées à l’occasion de ce tutoriel, supprimez le groupe de ressources par le biais du portail Azure.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser Azure Functions avec le service Azure SignalR. En savoir plus sur la création d’applications en temps réel sans serveur avec des liaisons de service SignalR pour Azure Functions.

> [!div class="nextstepaction"]
> [Générer des applications en temps réel avec Azure Functions](signalr-overview-azure-functions.md)
