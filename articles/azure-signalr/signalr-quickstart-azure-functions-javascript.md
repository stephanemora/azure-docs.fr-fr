---
title: Démarrage rapide du service Azure SignalR serverless - JavaScript | Microsoft Docs
description: Un démarrage rapide pour utiliser le service Azure SignalR et Azure Functions afin de créer une salle de conversation.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.devlang: javascript
ms.topic: quickstart
ms.tgt_pltfrm: Node.js
ms.workload: tbd
ms.date: 09/23/2018
ms.author: zhshang
ms.openlocfilehash: 12ac64a89f27744e1e731633d784135c97c423e0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991026"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-javascript"></a>Démarrage rapide : Créer une salle de conversation avec Azure Functions et le service SignalR à l’aide de JavaScript

Le service Azure SignalR vous permet d’ajouter facilement des fonctionnalités en temps réel à votre application. Azure Functions est une plateforme serverless qui vous permet d’exécuter votre code sans gérer d’infrastructures. Dans ce démarrage rapide, découvrez comment utiliser le service SignalR et Functions pour générer une application serverless de conversation en temps réel.


## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide peut être exécuté sur macOS, Windows ou Linux.

Assurez-vous qu’un éditeur de code tel que [Visual Studio Code](https://code.visualstudio.com/) est installé.

Installez [Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing) pour exécuter des applications Azure Function en local.

Pour installer des extensions, Azure Functions Core Tools requiert actuellement que le [kit SDK .NET Core](https://www.microsoft.com/net/download) soit installé. Toutefois, aucune connaissance de .NET n’est requise pour générer des applications de fonction Azure JavaScript.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur <https://portal.azure.com/> avec votre compte Azure.


[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]


## <a name="configure-and-run-the-azure-function-app"></a>Configurer et exécuter l’application de fonction Azure

1. Dans le navigateur dans lequel le portail Azure est ouvert, vérifiez que l’instance du service SignalR que vous avez déployée précédemment a bien été créée en recherchant son nom dans la zone de recherche en haut du portail. Sélectionnez l’instance pour l’ouvrir.

    ![Rechercher l’instance du service SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Sélectionnez **Clés** pour afficher les chaînes de connexion de l’instance du service SignalR.

1. Sélectionnez et copiez la chaîne de connexion principale.

    ![Créer un service SignalR](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. Dans votre éditeur de code, ouvrez le dossier *chat/src/javascript* dans le référentiel cloné.

1. Renommez *local.settings.sample.json* en *local.settings.json*.

1. Dans le fichier **local.settings.json**, collez la chaîne de connexion dans la valeur du paramètre **AzureSignalRConnectionString**. Enregistrez le fichier .

1. Les fonctions JavaScript sont organisées dans des dossiers. Deux fichiers se trouvent dans chaque dossier : le fichier *function.json* définit les liaisons utilisées dans la fonction et le fichier *index.js* correspond au corps de la fonction. Il existe deux fonctions déclenchées par HTTP dans cette application de fonction :

    - **negotiate** : utilise la liaison d’entrée *SignalRConnectionInfo* pour générer et retourner des informations de connexion valides.
    - **messages** : reçoit un message de conversation dans le corps de la demande et utilise la liaison de sortie *SignalR* pour diffuser le message à toutes les applications clientes connectées.

1. Dans le terminal, vérifiez que vous vous trouvez dans le dossier *chat/src/javascript*. Utilisez Azure Functions Core Tools pour installer les extensions requises pour exécuter l’application.

    ```bash
    func extensions install
    ```

1. Exécutez l’application de fonction.

    ```bash
    func start
    ```

    ![Créer un service SignalR](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)


[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez généré et exécuté une application serverless en temps réel dans Visual Studio. Découvrez ensuite comment développer et déployer Azure Functions avec Visual Studio.

> [!div class="nextstepaction"]
> [Développer Azure Functions à l’aide de Visual Studio](../azure-functions/functions-develop-vs.md)