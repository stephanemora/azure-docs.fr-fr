---
title: Capture instantanée à un point dans le temps dans Azure App Configuration| Microsoft Docs
description: Vue d’ensemble du fonctionnement d’une capture instantanée à un point dans le temps dans Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 4f26f3a31dc6303e991c39fc7f85d9bf254d57bc
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885135"
---
# <a name="point-in-time-snapshot"></a>Capture instantanée à un point dans le temps

Azure App Configuration conserve les enregistrements des dates de création et de modification ultérieure précises des paires clé-valeur. Ces enregistrements forment une chronologie complète des modifications de paires clé-valeur. Un magasin de configuration d’application peut reconstruire l’historique d’une paire clé-valeur et relire son ancienne valeur à tout moment donné, jusqu’au moment présent. Cette fonctionnalité vous permet de « voyager dans le passé » pour récupérer une ancienne paire clé-valeur. Par exemple, vous pouvez obtenir les paramètres de configuration de la veille, juste avant le dernier déploiement, pour récupérer une configuration précédente si vous avez besoin de restaurer l’application.

## <a name="key-value-retrieval"></a>Récupération de paires clé-valeur

Pour récupérer des paires clé-valeur anciennes, vous avez besoin de spécifier une heure à laquelle un instantané de ces paires a été capturé dans l’en-tête HTTP d’un appel d’API REST. Par exemple : 

        GET /revisions HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Actuellement, App Configuration conserve 7 jours d’historique de modifications.

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Créer une application web ASP.NET](quickstart-aspnet-core-app.md)  
