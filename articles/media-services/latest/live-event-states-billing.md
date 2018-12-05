---
title: États et facturation d’événements en temps réel dans Azure Media Services | Microsoft Docs
description: Cette rubrique fournit une vue d’ensemble des états et de la facturation d’événements en temps réel dans Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: 1a49f62d7b5e21fe9d6483f71b729a9100aff1a3
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585401"
---
# <a name="liveevent-states-and-billing"></a>États et facturation d’événements en temps réel

Dans Azure Media Services, la facturation d’un événement en temps réel commence dès que son état passe à **En cours d’exécution**. Pour mettre fin à la facturation de l’événement en temps réel, vous devez arrêter ce dernier.

Lorsque la valeur **LiveEventEncodingType** de votre [événement en temps réel](https://docs.microsoft.com/rest/api/media/liveevents) est définie sur Standard (De base), Media Services met automatiquement fin aux événements en temps réel **En cours d’exécution** 12 heures après la perte du flux d’entrée et l’absence de **sorties en temps réel** en cours d’exécution. Toutefois, vous serez toujours facturé pour la durée pendant laquelle l’événement en temps réel était à l’état **En cours d’exécution**.

## <a name="states"></a>États

L’événement en temps réel peut avoir l’un des états suivants.

|État|Description|
|---|---|
|**Arrêté**| Ceci est l’état initial de l’événement en temps réel après sa création (sauf si le démarrage automatique a été défini sur true). Aucune facturation ne survient dans cet état. Dans cet état, les propriétés de l’événement en temps réel peuvent être mises à jour, mais le streaming n’est pas autorisé.|
|**Démarrage en cours**| L’événement en temps réel est en cours de démarrage et les ressources sont allouées. Aucune facturation ne survient dans cet état. Les mises à jour et diffusions en continu ne sont pas autorisées pendant cet état. Si une erreur se produit, l’événement en temps réel retourne à l’état Arrêté.|
|**Exécution**| Les ressources de l’événement en direct ont été alloués, des URL d’ingestion et d’aperçu ont été générées, et l’événement peut recevoir des flux en direct. À ce stade, la facturation est active. Vous devez appeler explicitement la commande Stop sur la ressource de l’événement en remps réel pour arrêter toute facturation supplémentaire.|
|**En cours d’arrêt**| L’événement en temps réel est en cours d’arrêt et les ressources sont déprovisionnées. Aucune facturation ne survient dans cet état de transition. Les mises à jour et diffusions en continu ne sont pas autorisées pendant cet état.|
|**Suppression en cours**| L’événement en temps réel est en cours de suppression. Aucune facturation ne survient dans cet état de transition. Les mises à jour et diffusions en continu ne sont pas autorisées pendant cet état.|

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du streaming en direct](live-streaming-overview.md)
- [Didacticiel sur le streaming en direct](stream-live-tutorial-with-api.md)
