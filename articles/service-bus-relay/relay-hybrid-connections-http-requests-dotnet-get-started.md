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
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/16/2018
ms.author: spelluru
ms.openlocfilehash: e66a1651a46cfaeb7fb8b232eeb7cf6a2fb8044d
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451220"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>Bien démarrer avec les requêtes HTTP de connexions hybrides Relay dans .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Ce didacticiel fournit une introduction des [connexions hybrides Azure Relay](relay-what-is-it.md#hybrid-connections). Découvrez comment utiliser Microsoft .NET pour créer une application cliente qui envoie des demandes à une application d’écouteur correspondante. 

## <a name="what-will-be-accomplished"></a>Les opérations que nous allons effectuer
Les connexions hybrides requièrent un composant client et un composant serveur. Dans ce didacticiel, vous effectuez ces étapes pour créer deux applications console :

1. Créer un espace de noms Relay à l’aide du portail Azure.
2. Créer une connexion hybride dans cet espace de noms à l’aide du portail Azure.
3. Écrire une application console (d’écouteur) de serveur pour recevoir des demandes.
4. Écrire une application console (d’expéditeur) de client pour envoyer des demandes.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

* [Visual Studio 2015 ou version ultérieure](http://www.visualstudio.com). Les exemples de ce didacticiel utilisent Visual Studio 2017.
* Un abonnement Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-by-using-the-azure-portal"></a>1. Créer un espace de noms à l’aide du portail Azure
Si vous avez déjà créé un espace de noms Relay, allez à [Créer une connexion hybride à l’aide du portail Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-by-using-the-azure-portal"></a>2. Créer une connexion hybride à l’aide du portail Azure
Si vous avez déjà créé une connexion hybride, allez à [Créer une application de serveur](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Créer une application de serveur (récepteur)
Dans Visual Studio, écrivez une application console C# pour écouter et recevoir des messages à partir de Relay.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Créer une application cliente (expéditeur)
Dans Visual Studio, écrivez une application console C# pour envoyer des messages à Relay.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Exécution des applications
1. Exécutez l’application de serveur. La fenêtre de console affiche le texte suivant :

    ```
    Online
    Server listening
    ```
1. Exécuter l’application cliente. Vous voyez `hello!` dans la fenêtre du client. Le client a envoyé une requête HTTP au serveur et le serveur a répondu avec un `hello!`. 
3. Maintenant, pour fermer les fenêtres de console, appuyez sur **ENTRÉE** dans les deux les fenêtres de console. 

Vous avez créé une application de connexions hybrides complète : félicitations !

## <a name="next-steps"></a>Étapes suivantes

* [FAQ sur Azure Relay](relay-faq.md)
* [Créer un espace de noms](relay-create-namespace-portal.md)
* [Prise en main de Node](relay-hybrid-connections-node-get-started.md)

