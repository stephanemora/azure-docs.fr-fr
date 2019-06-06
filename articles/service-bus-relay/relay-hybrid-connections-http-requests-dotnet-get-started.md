---
title: Bien démarrer avec les requêtes HTTP de connexions hybrides Azure Relay dans .NET | Microsoft Docs
description: Écrivez une application console C# pour des requêtes HTTP de connexions hybrides Azure Relay dans .NET.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 37227b7d0ea1b3630a3c2ce991a61543e6a1503d
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428250"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>Bien démarrer avec les requêtes HTTP de connexions hybrides Relay dans .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Dans ce guide de démarrage rapide, vous créez des applications .NET, à savoir un expéditeur et un récepteur, qui envoient et reçoivent des messages à l’aide du protocole HTTP. Les applications utilisent la fonctionnalité Connexions hybrides d’Azure Relay. Pour en savoir plus sur Azure Relay en général, consultez [Azure Relay](relay-what-is-it.md). 

Ce démarrage rapide comporte les étapes suivantes :

1. Créer un espace de noms Relay à l’aide du portail Azure.
2. Créer une connexion hybride dans cet espace de noms à l’aide du portail Azure.
3. Écrire une application de console (écouteur) de serveur pour recevoir des messages.
4. Écrire une application de console (expéditeur) de client pour envoyer des messages.
5. Exécuter les applications. 

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

* [Visual Studio 2015 ou version ultérieure](https://www.visualstudio.com). Les exemples de ce didacticiel utilisent Visual Studio 2017.
* Un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-a-namespace"></a>Créer un espace de noms
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Create a hybrid connection
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Créer une application de serveur (récepteur)
Dans Visual Studio, écrivez une application console C# pour écouter et recevoir des messages à partir de Relay.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Créer une application cliente (expéditeur)
Dans Visual Studio, écrivez une application console C# pour envoyer des messages à Relay.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Exécution des applications
1. Exécutez l’application de serveur. La fenêtre de console affiche le texte suivant :

    ```
    Online
    Server listening
    ```
1. Exécuter l’application cliente. Vous voyez `hello!` dans la fenêtre du client. Le client a envoyé une requête HTTP au serveur, et ce dernier a répondu par `hello!`. 
3. Maintenant, pour fermer les fenêtres de console, appuyez sur **ENTRÉE** dans les deux les fenêtres de console. 

Félicitations, vous avez créé une application de connexions hybrides complète.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé des applications cliente et serveur .NET qui utilisent HTTP pour envoyer et recevoir des messages. La fonctionnalité Connexions hybrides d’Azure Relay permet également d’envoyer et de recevoir des messages à l’aide de WebSockets. Pour apprendre à utiliser WebSockets avec des connexions hybrides Azure Relay, consultez le [guide de démarrage rapide de WebSockets](relay-hybrid-connections-dotnet-get-started.md).

Dans ce guide de démarrage rapide, vous avez utilisé le .NET Framework pour créer des applications cliente et serveur. Pour apprendre à écrire des applications cliente et serveur à l’aide de Node.js, consultez le [guide de démarrage rapide de WebSockets dans Node.js](relay-hybrid-connections-node-get-started.md) ou le [guide de démarrage rapide de HTTP dans Node.js](relay-hybrid-connections-http-requests-dotnet-get-started.md).
