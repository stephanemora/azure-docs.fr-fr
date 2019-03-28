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
ms.openlocfilehash: 6238f96c9e8df0431e42caa5e5846af3fc60e681
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484013"
---
# <a name="point-in-time-snapshot"></a>Capture instantanée à un point dans le temps

Azure App Configuration conserve les enregistrements des dates de création et de modification précises des paires clé-valeur. Ces enregistrements forment une chronologie complète des modifications de paires clé-valeur. Un magasin de configuration d’application peut reconstruire l’historique d’une valeur de clé et relire son ancienne valeur à tout moment donné, jusqu’au moment présent. Avec cette fonctionnalité, vous pouvez « voyager dans le passé » pour récupérer une ancienne valeur de clé. Par exemple, vous pouvez obtenir les paramètres de configuration de la veille, juste avant le dernier déploiement en date, afin de récupérer une précédente configuration pour restaurer l’application.

## <a name="key-value-retrieval"></a>Récupération de paires clé-valeur

Pour récupérer d’anciennes valeurs de clé, spécifiez une heure à laquelle un instantané de ces valeurs de clé a été capturé dans l’en-tête HTTP d’un appel d’API REST. Par exemple : 

        GET /kv HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Actuellement, App Configuration conserve sept jours d’historique de modifications.

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Créer une application web ASP.NET](quickstart-aspnet-core-app.md)  
