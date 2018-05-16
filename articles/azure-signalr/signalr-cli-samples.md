---
title: Exemples Azure CLI - Azure SignalR Service | Microsoft Docs
description: Exemples Azure CLI - Azure SignalR Service
services: signalr
documentationcenter: signalr
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 6e76ee89160c84ff0f1033590d14c288f023f201
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cli-samples"></a>Exemples d’interface de ligne de commande Azure

Le tableau suivant contient des liens vers des scripts Bash pour Azure SignalR Service utilisant Azure CLI.

| | |
|-|-|
|**Créer**||
| [Créer une ressource SignalR Service et un groupe de ressources](scripts/signalr-cli-create-service.md) | Crée une ressource Azure SignalR Service dans un nouveau groupe de ressources avec un nom aléatoire.  |
|**Intégrer**||
| [Créer une ressource SignalR Service et une application web configurée pour utiliser SignalR](scripts/signalr-cli-create-with-app-service.md) | Crée une ressource Azure SignalR Service dans un nouveau groupe de ressources avec un nom aléatoire. Ajoute également une application web et un plan App Service pour héberger une application web ASP.NET Core qui utilise SignalR Service. L’application web est configurée avec un paramètre d’application pour se connecter à la nouvelle ressource SignalR Service. |
| [Créer une ressource SignalR Service et une application web configurée pour utiliser SignalR et GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Crée une ressource Azure SignalR Service dans un nouveau groupe de ressources avec un nom aléatoire. Ajoute également ajoute une application web Azure et un plan d’hébergement pour héberger une application web ASP.NET Core qui utilise SignalR Service. L’application web est configurée avec des paramètres d’application pour la chaîne de connexion à la nouvelle ressource SignalR Service et des clés secrètes client pour prendre en charge [l’authentification GitHub](https://developer.github.com/v3/guides/basics-of-authentication/) comme illustré dans le [didacticiel sur l’authentification](signalr-authenticate-oauth.md). L’application web est également configurée pour utiliser une source de déploiement du référentiel git local. |
| | |
