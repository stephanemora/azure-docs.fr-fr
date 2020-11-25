---
title: Contenu pour adultes, choquant ou sordide - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Concepts liés à la détection de contenu pour adultes dans les images à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5cc8a4508ceeda245fbc10a81e16f3ecf05284c7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013609"
---
# <a name="detect-adult-content"></a>Détecter du contenu pour adultes

Vision par ordinateur peut détecter des éléments pour adultes dans les images afin de permettre aux développeurs de restreindre l'affichage de telles images dans leurs logiciels. Des indicateurs de contenu sont appliquées avec un score compris entre zéro et un pour permettre aux développeurs d'interpréter les résultats en fonction de leurs préférences.

> [!NOTE]
> Cette fonctionnalité est en grande partie proposée par le service [Azure Content Moderator](../content-moderator/overview.md). Reportez-vous à cette alternative pour des solutions adaptées à des scénarios de modération de contenu plus rigoureux, tels que les flux de travail de modération de contenu et de révision manuelle.

## <a name="content-flag-definitions"></a>Définitions des indicateurs de contenu

La classification « pour adultes » regroupe plusieurs catégories :

- Par définition, les images **pour adultes** sont de nature explicitement sexuel, et représentent souvent des actes sexuels et de nudité.
- Par définition, les images **choquantes** sont de nature sexuellement suggestive et contiennent souvent moins de contenu sexuellement explicite que les images marquées comme étant **pour adultes**.
- Les images **sordides** sont définies en tant qu'images correspondant à du contenu sordide.

## <a name="use-the-api"></a>Utilisation de l’API

L'API [Analyser l'image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) permet de détecter du contenu pour adultes. Lorsque vous ajoutez la valeur de `Adult` au paramètre de requête **visualFeatures**, l’API renvoie les trois propriétés booléennes &mdash;`isAdultContent`, `isRacyContent` et `isGoryContent`&mdash;dans sa réponse JSON. La méthode renvoie également les propriétés correspondantes &mdash;`adultScore`, `racyScore` et `goreScore`&mdash;qui représentent des scores de confiance compris entre zéro et un pour chaque catégorie respective.

- [Démarrage rapide : Analyser une image (SDK .NET)](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
- [Démarrage rapide : Analyser une image (API REST)](./quickstarts/csharp-analyze.md)