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
ms.date: 10/24/2019
ms.author: juliako
ms.openlocfilehash: af3d4b51dadfaa99a166ca0ce475c5a110d8f6e8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933678"
---
# <a name="live-event-states-and-billing"></a>États et facturation des événements en direct

Dans Azure Media Services, la facturation d’un événement en direct commence dès que son état passe à **En cours d’exécution**. Pour interrompre la facturation de l’événement en direct, vous devez arrêter cet événement.

Quand la valeur **LiveEventEncodingType** de votre [événement en direct](https://docs.microsoft.com/rest/api/media/liveevents) est définie sur Standard ou Premium1080p, Media Services met automatiquement fin aux événements en direct **En cours d’exécution** 12 heures après la perte du flux d’entrée et l’absence de **sorties en direct** en cours d’exécution. Toutefois, vous serez toujours facturé pour la durée pendant laquelle l’événement en direct était dans l’état **En cours d’exécution**.

> [!NOTE]
> Les événements en direct de transfert ne sont pas arrêtés automatiquement mais doivent être arrêtés de façon explicite via l’API pour éviter une facturation excessive. 

## <a name="states"></a>États

L’événement en direct peut présenter l’un des états suivants.

|State|Description|
|---|---|
|**Arrêté**| C’est l’état initial de l’événement en direct après sa création (sauf si le démarrage automatique a été défini sur true). Aucune facturation ne survient dans cet état. Dans cet état, les propriétés de l’événement en direct peuvent être mises à jour, mais le streaming n’est pas autorisé.|
|**Démarrage en cours**| L’événement en direct est en cours de démarrage et les ressources sont allouées. Aucune facturation ne survient dans cet état. Les mises à jour et diffusions en continu ne sont pas autorisées pendant cet état. Si une erreur se produit, l’événement en direct retourne à l’état Arrêté.|
|**Exécution**| Les ressources de l’événement en direct ont été allouées, des URL de réception et d’aperçu ont été générées, et l’événement peut recevoir des flux temps réel. À ce stade, la facturation est active. Vous devez appeler explicitement la commande Stop sur la ressource de l’événement en direct pour arrêter toute facturation supplémentaire.|
|**En cours d’arrêt**| L’événement en direct est en cours d’arrêt et les ressources sont déprovisionnées. Aucune facturation ne survient dans cet état de transition. Les mises à jour et diffusions en continu ne sont pas autorisées pendant cet état.|
|**Suppression en cours**| L’événement en direct est en cours de suppression. Aucune facturation ne survient dans cet état de transition. Les mises à jour et diffusions en continu ne sont pas autorisées pendant cet état.|

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du streaming en direct](live-streaming-overview.md)
- [Didacticiel sur le streaming en direct](stream-live-tutorial-with-api.md)
