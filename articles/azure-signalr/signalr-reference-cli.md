---
title: Exemples Azure CLI - Azure SignalR Service
description: Exemples Azure CLI - Azure SignalR Service
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 1fbe96d827bcf6bb39a6cf7a4e6811174b862d59
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61071074"
---
# <a name="azure-cli-reference"></a>Informations de référence sur Azure CLI

Le tableau suivant contient des liens vers des scripts Bash pour Azure SignalR Service utilisant Azure CLI.

| | |
|-|-|
|**Créer**||
| [Créer une ressource SignalR Service et un groupe de ressources](scripts/signalr-cli-create-service.md) | Crée une ressource Azure SignalR Service dans un nouveau groupe de ressources avec un nom aléatoire.  |
|**Intégrer**||
| [Créer une ressource SignalR Service et une application web configurée pour utiliser SignalR](scripts/signalr-cli-create-with-app-service.md) | Crée une ressource Azure SignalR Service dans un nouveau groupe de ressources avec un nom aléatoire. Ajoute également une application web et un plan App Service pour héberger une application web ASP.NET Core qui utilise SignalR Service. L’application web est configurée avec un paramètre d’application pour se connecter à la nouvelle ressource SignalR Service. |
| [Créer une ressource SignalR Service et une application web configurée pour utiliser SignalR et GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Crée une ressource Azure SignalR Service dans un nouveau groupe de ressources avec un nom aléatoire. Ajoute également ajoute une application web Azure et un plan d’hébergement pour héberger une application web ASP.NET Core qui utilise SignalR Service. L’application web est configurée avec des paramètres d’application pour la chaîne de connexion à la nouvelle ressource SignalR Service et des clés secrètes client pour prendre en charge [l’authentification GitHub](https://developer.github.com/v3/guides/basics-of-authentication/) comme illustré dans le [didacticiel sur l’authentification](signalr-concept-authenticate-oauth.md). L’application web est également configurée pour utiliser une source de déploiement du référentiel git local. |
| | |
