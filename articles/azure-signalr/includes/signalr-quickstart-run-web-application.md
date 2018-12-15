---
title: Fichier Include
description: Fichier Include
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 73d40bfb5a7e691cead5a84be70398e9cbf6656a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53262765"
---
## <a name="run-the-web-application"></a>Exécuter l’application web

1. Voici à votre disposition un exemple d’application web à page unique hébergée dans GitHub. Ouvrez votre navigateur et rendez-vous sur [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat/).

    > [!NOTE]
    > La source du fichier HTML est située dans [/docs/demo/chat/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat/index.html).

1. Lorsque vous êtes invité à renseigner l’URL de base de l’application de fonction, saisissez *http://localhost:7071*.

1. Saisissez un nom d’utilisateur lorsque vous y êtes invité.

1. L’application web appelle la fonction *GetSignalRInfo* dans l’application de fonction afin de récupérer les informations de connexion nécessaires pour se connecter au service Azure SignalR. Une fois la connexion effectuée, la boîte de discussion s’affiche.

1. Tapez un message puis appuyez sur Entrée. L’application envoie le message à la fonction *SendMessage* dans l’application Azure Function, qui se sert ensuite de la liaison de sortie SignalR pour diffuser le message à tous les clients connectés. Si tout fonctionne normalement, le message doit s’afficher dans l’application.

    ![Exécution de l'application](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Ouvrez une autre instance de l’application web dans une fenêtre de navigation différente. Vous verrez que tous les messages envoyés s’affichent dans toutes les instances de l’application.