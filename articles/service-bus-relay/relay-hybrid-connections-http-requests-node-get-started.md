---
title: Bien démarrer avec les requêtes HTTP de connexions hybrides Azure Relay dans Node | Microsoft Docs
description: Écrivez une application console Node.js pour des requêtes HTTP de connexions hybrides Azure Relay dans Node.
services: service-bus-relay
documentationcenter: node
author: clemensv
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 05/02/2018
ms.author: clemensv
ms.openlocfilehash: 2bc923650425c76562161dd6f44f3a5722b5cefe
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2018
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Bien démarrer avec les requêtes HTTP de connexions hybrides Relay dans Node

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Ce tutoriel présente les requêtes HTTP de [connexions hybrides Azure Relay](relay-what-is-it.md#hybrid-connections) et montre comment utiliser Node.js pour créer une application cliente qui envoie des messages à une application d’écouteur correspondante.

## <a name="what-will-be-accomplished"></a>Les opérations que nous allons effectuer

Étant donné que les Connexions hybrides nécessitent un composant client et un composant serveur, vous allez créer deux applications console dans ce tutoriel. Voici la procédure à suivre :

1. Créer un espace de noms Relay à l’aide du portail Azure.
2. Créer une connexion hybride, à l’aide du portail Azure.
3. Écrire une application de console de serveur pour recevoir des messages.
4. Écrire une application de console de client pour envoyer des messages.

## <a name="prerequisites"></a>Prérequis


1. [Node.js](https://nodejs.org/en/).
2. Un abonnement Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Créer un espace de noms à l’aide du Portail Azure

Si vous disposez déjà créé un espace de noms Relay, passez à la section [Créer une connexion hybride à l’aide du portail Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Créer une connexion hybride à l’aide du portail Azure

Si vous disposez déjà d’une connexion hybride, passez à la section [Créer une application de serveur](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Créer une application de serveur (récepteur)

Pour écouter et recevoir des messages à partir de Relay, écrivez une application console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-http-requests-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Créer une application cliente (expéditeur)

Pour envoyer des messages à Relay, vous pouvez utiliser un client HTTP quelconque ou écrire une application console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-http-requests-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Exécution des applications

1. Exécutez l’application serveur : à partir d’un type d’invite de commandes Node.js `node listener.js`.
2. Exécutez l’application cliente : à partir d’un type d’invite de commandes Node.js `node sender.js`, puis entrez du texte.
3. Vérifiez que la console d’application de serveur renvoie le texte entré dans l’application cliente.

Vous avez créé une application de connexions hybrides complète avec Node.js : félicitations !

## <a name="next-steps"></a>Étapes suivantes

* [FAQ sur Azure Relay](relay-faq.md)
* [Créer un espace de noms](relay-create-namespace-portal.md)
* [Prise en main de .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Prise en main de Node](relay-hybrid-connections-node-get-started.md)

