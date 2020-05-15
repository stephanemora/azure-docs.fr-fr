---
title: Connexions hybrides Azure Relay - WebSockets dans Node
description: Écrire une application console Node.js pour des WebSockets de connexions hybrides Azure Relay
services: service-bus-relay
documentationcenter: node
author: spelluru
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 818db4db082a441877b573fd52361e63becce374
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83204604"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>Bien démarrer avec les WebSockets de connexions hybrides Relay dans Node.js

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Dans ce guide de démarrage rapide, vous créez des applications Node.js, à savoir un expéditeur et un récepteur, qui envoient et reçoivent des messages à l’aide de WebSockets de connexions hybrides dans Azure Relay. Pour en savoir plus sur Azure Relay en général, consultez [Azure Relay](relay-what-is-it.md). 

Ce démarrage rapide comporte les étapes suivantes : 

1. Créer un espace de noms Relay à l’aide du portail Azure.
2. Créer une connexion hybride dans cet espace de noms à l’aide du portail Azure.
3. Écrire une application de console (écouteur) de serveur pour recevoir des messages.
4. Écrire une application de console (expéditeur) de client pour envoyer des messages.
5. Exécuter les applications. 

## <a name="prerequisites"></a>Conditions préalables requises

- [Node.js](https://nodejs.org/en/).
- Un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-a-namespace"></a>Créer un espace de noms
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Create a hybrid connection
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Créer une application de serveur (récepteur)
Pour écouter et recevoir des messages à partir de Relay, écrivez une application console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Créer une application cliente (expéditeur)
Pour envoyer des messages à Relay, écrivez une application console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="run-the-applications"></a>Exécution des applications

1. Exécutez l’application serveur : à partir d’un type d’invite de commandes Node.js `node listener.js`.
2. Exécutez l’application cliente : à partir d’un type d’invite de commandes Node.js `node sender.js`, puis entrez du texte.
3. Vérifiez que la console d’application de serveur renvoie le texte entré dans l’application cliente.

    ![running-applications](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Vous avez créé une application de connexions hybrides complète avec Node.js : félicitations !

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez créé des applications cliente et serveur Node.js qui utilisent des WebSockets pour envoyer et recevoir des messages. La fonctionnalité Connexions hybrides d’Azure Relay permet également d’envoyer et de recevoir des messages avec HTTP. Pour apprendre à utiliser HTTP avec des connexions hybrides Azure Relay, consultez le [guide de démarrage rapide de HTTP dans Node.js](relay-hybrid-connections-http-requests-node-get-started.md).

Dans ce guide de démarrage rapide, vous avez utilisé Node.js pour créer des applications cliente et serveur. Pour apprendre à écrire des applications cliente et serveur à l’aide du .NET Framework, consultez le [guide de démarrage rapide de WebSockets dans .NET](relay-hybrid-connections-dotnet-get-started.md) ou le [guide de démarrage rapide de HTTP dans .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md).


