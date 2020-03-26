---
title: Fichier Include
description: Fichier Include
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 73d4d4e1a5f148dce6099b2d747ee1c290bcf7c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "67177434"
---
## <a name="run-the-web-application"></a>Exécuter l’application web

1. Voici à votre disposition un exemple d’application web à page unique hébergée dans GitHub. Ouvrez votre navigateur et rendez-vous sur [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/).

    > [!NOTE]
    > La source du fichier HTML est située dans [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html).

1. Lorsque vous êtes invité à renseigner l’URL de base de l’application de fonction, indiquez `http://localhost:7071`.

1. Saisissez un nom d’utilisateur lorsque vous y êtes invité.

1. L’application web appelle la fonction *GetSignalRInfo* dans l’application de fonction afin de récupérer les informations de connexion nécessaires pour se connecter au service Azure SignalR. Une fois la connexion effectuée, la boîte de discussion s’affiche.

1. Tapez un message puis appuyez sur Entrée. L’application envoie le message à la fonction *SendMessage* dans l’application Azure Function, qui se sert ensuite de la liaison de sortie SignalR pour diffuser le message à tous les clients connectés. Si tout fonctionne normalement, le message doit s’afficher dans l’application.

    ![Exécution de l'application](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Ouvrez une autre instance de l’application web dans une fenêtre de navigation différente. Vous verrez que tous les messages envoyés s’affichent dans toutes les instances de l’application.

> [!IMPORTANT]
> Étant donné que la page HTML est prise en charge à l’aide de HTTPS, mais que le runtime Azure Functions local utilise HTTP par défaut, votre navigateur (tel que Firefox) peut appliquer une stratégie de contenu mixte qui bloque les requêtes à partir de la page web vers vos fonctions. Pour résoudre ce problème, utilisez un navigateur qui ne connaît pas cette restriction, ou démarrez un serveur HTTP local, tel que [http-server](https://www.npmjs.com/package/http-server) dans le répertoire */docs/demo/chat-v2*. Vérifiez que l’origine est ajoutée au paramètre `CORS` dans *local.settings.json*.