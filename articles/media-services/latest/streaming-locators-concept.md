---
title: Localisateurs de diffusion en continu dans Azure Media Services | Microsoft Docs
description: Cet article explique ce que sont les localisateurs de diffusion en continu et comment Azure Media Services les utilise.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/22/2019
ms.author: juliako
ms.openlocfilehash: 9a14399117971807c1d18f8eb5fab7d6e6cef2d5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120348"
---
# <a name="streaming-locators"></a>Localisateurs de diffusion en continu

Pour rendre les vidéos dans la ressource de sortie disponibles en lecture pour les clients, vous devez créer un [localisateur de diffusion en continu](https://docs.microsoft.com/rest/api/media/streaminglocators) , puis générer des URL de diffusion en continu. Pour un exemple .NET, consultez [Obtenir un localisateur de diffusion en continu](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Le processus de création d’un **localisateur de streaming** est appelée « publication ». Par défaut, le **localisateur de streaming** est valide immédiatement après avoir effectué les appels d’API et dure jusqu’à ce qu’il soit supprimé, sauf si vous configurez les durées de début et de fin optionnelles. 

Lorsque vous créez un **localisateur de diffusion en continu**, vous devez spécifier un **Asset** nom et un **stratégie de diffusion en continu** nom. Pour plus d’informations, consultez les rubriques suivantes :

* [Éléments multimédias](assets-concept.md)
* [Stratégies de diffusion en continu](streaming-policy-concept.md)
* [Stratégies de clé de contenu](content-key-policy-concept.md)

> [!IMPORTANT]
> * Les propriétés des **localisateurs de diffusion en continu** de type DateHeure sont toujours au format UTC.
> * Vous devez concevoir un ensemble limité de stratégies pour votre compte Media Services et les réutiliser pour vos éléments localisateurs de diffusion en continu chaque fois que les mêmes options sont nécessaires. Pour plus d’informations, consultez [Quotas et limitations](limits-quotas-constraints.md).

## <a name="associate-filters-with-streaming-locators"></a>Associer des filtres avec des localisateurs de diffusion en continu

Consultez [filtres : associer avec des localisateurs de diffusion en continu](filters-concept.md#associate-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtre, par ordre, les entités de localisateurs de diffusion en continu de page

Consultez [Filtrage, tri et pagination des entités Media Services](entities-overview.md).

## <a name="next-steps"></a>Étapes suivantes

[Tutoriel : Charger, encoder et diffuser en streaming des vidéos à l’aide de .NET](stream-files-tutorial-with-api.md)
