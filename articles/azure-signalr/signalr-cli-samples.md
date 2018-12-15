---
title: Exemples Azure CLI - Azure SignalR Service
description: Exemples Azure CLI - Azure SignalR Service
author: sffamily
ms.service: signalr
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: a39f0abf4f3a8cc30d6f8f83dcad7e9069f348e4
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53258778"
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