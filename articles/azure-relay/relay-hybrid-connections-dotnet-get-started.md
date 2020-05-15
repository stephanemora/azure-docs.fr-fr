---
title: Connexions hybrides Azure Relay - WebSockets dans .NET
description: Écrivez une application console C# pour les WebSockets de connexions hybrides Azure Relay.
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
ms.openlocfilehash: 13612f8ffa343e483165a8dbdd54d1b2b1f5e2cf
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83204616"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>Bien démarrer avec les WebSockets de connexions hybrides Relay dans .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Dans ce guide de démarrage rapide, vous créez des applications .NET, à savoir un expéditeur et un récepteur, qui envoient et reçoivent des messages à l’aide de WebSockets de connexions hybrides dans Azure Relay. Pour en savoir plus sur Azure Relay en général, consultez [Azure Relay](relay-what-is-it.md). 

Ce démarrage rapide comporte les étapes suivantes :

1. Créer un espace de noms Relay à l’aide du portail Azure.
2. Créer une connexion hybride dans cet espace de noms à l’aide du portail Azure.
3. Écrire une application de console (écouteur) de serveur pour recevoir des messages.
4. Écrire une application de console (expéditeur) de client pour envoyer des messages.
5. Exécuter les applications. 

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

* [Visual Studio 2015 ou version ultérieure](https://www.visualstudio.com). Les exemples de ce didacticiel utilisent Visual Studio 2017.
* Un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-a-namespace"></a>Créer un espace de noms
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Create a hybrid connection
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Créer une application de serveur (récepteur)
Dans Visual Studio, écrivez une application console C# pour écouter et recevoir des messages à partir de Relay.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Créer une application cliente (expéditeur)
Dans Visual Studio, écrivez une application console C# pour envoyer des messages à Relay.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Exécution des applications
1. Exécutez l’application de serveur.
2. Exécutez l’application cliente et entrez du texte.
3. Vérifiez que la console d’application de serveur affiche le texte entré dans l’application cliente.

    ![running-applications](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Félicitations, vous avez créé une application de connexions hybrides complète !

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez créé des applications cliente et serveur .NET qui utilisent des WebSockets pour envoyer et recevoir des messages. La fonctionnalité Connexions hybrides d’Azure Relay permet également d’envoyer et de recevoir des messages avec HTTP. Pour apprendre à utiliser HTTP avec des connexions hybrides Azure Relay, consultez le [guide de démarrage rapide de HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md).

Dans ce guide de démarrage rapide, vous avez utilisé le .NET Framework pour créer des applications cliente et serveur. Pour apprendre à écrire des applications cliente et serveur à l’aide de Node.js, consultez le [guide de démarrage rapide de WebSockets dans Node.js](relay-hybrid-connections-node-get-started.md) ou le [guide de démarrage rapide de HTTP dans Node.js](relay-hybrid-connections-http-requests-dotnet-get-started.md).

