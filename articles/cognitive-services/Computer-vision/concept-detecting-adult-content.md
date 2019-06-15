---
title: 'Description de contenu pour adultes et choquant : Vision par ordinateur'
titleSuffix: Azure Cognitive Services
description: Concepts liés à la détection de contenu pour adultes et choquant dans les images à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 69a4c136e9c210dd40e004b8d5e1c1a2a8fceaa7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60368335"
---
# <a name="detect-adult-and-racy-content"></a>Détecter du contenu pour adultes et choquant

Vision par ordinateur peut détecter des éléments pour adultes dans les images pour permettre aux développeurs de restreindre l'affichage de telles images dans leurs logiciels. Des indicateurs de contenu sont appliquées avec un score compris entre zéro et un pour permettre aux développeurs d'interpréter les résultats en fonction de leurs préférences. 

> [!NOTE]
> Cette fonctionnalité est également proposée par le service [Azure Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview). Reportez-vous à cette alternative pour des solutions adaptées à des scénarios de modération de contenu plus rigoureux, tels que les flux de travail de modération de contenu et de révision manuelle.

## <a name="content-flag-definitions"></a>Définitions des indicateurs de contenu

Par définition, les images **pour adultes** sont de nature pornographique, et représentent souvent des actes sexuels et de nudité. 

Par définition, les images **choquantes** sont de nature sexuellement suggestive et contiennent souvent moins de contenu sexuellement explicite que les images marquées comme étant **pour adultes**. 

## <a name="identify-adult-and-racy-content"></a>Identifier du contenu pour adultes et choquant

API [Analyser](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).

La méthode Analyze Image retourne deux propriétés booléennes, `isAdultContent` et `isRacyContent`, dans la réponse JSON de la méthode pour indiquer respectivement du contenu pour adultes et choquant. Cette méthode retourne également deux propriétés, `adultScore` et `racyScore`, qui représentent respectivement les scores de confiance permettant d’identifier du contenu pour adultes et choquant.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts concernant la [détection du contenu spécifique à un domaine](concept-detecting-domain-content.md) et la [détection des visages](concept-detecting-faces.md).
