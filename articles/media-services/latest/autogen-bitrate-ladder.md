---
title: Utiliser l’encodeur standard dans Azure Media Services pour encoder des vidéos à l’aide d’une échelle de débit générée automatiquement | Microsoft Docs
description: Cette rubrique montre comment utiliser l’encodeur standard dans Media Services pour encoder une vidéo d’entrée avec une échelle de débit générée automatiquement en fonction de la résolution d’entrée et du débit. La résolution d’entrée et la vitesse de transmission ne seront jamais dépassées. Par exemple, si l’entrée est 720p à 3 Mbits/s, la sortie restera à 720p maximum démarrera à des vitesses inférieures à 3 Mbits/s.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 5d13c711d7d71df7469e6408ce78cf0df611632b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Encoder avec une échelle de débit générée automatiquement

## <a name="overview"></a>Vue d'ensemble

Cet article explique comment utiliser l’encodeur standard dans Media Services pour encoder une vidéo d’entrée dans une échelle de débit générée automatiquement (paires débit-résolution) en fonction de la résolution d’entrée et du débit. Ce paramètre d’encodeur intégré, ou présélection, ne dépassera jamais la résolution d’entrée et le débit. Par exemple, si l’entrée est 720p à 3 Mbits/s, la sortie reste à 720p maximum et démarre à des vitesses inférieures à 3 Mbits/s.

### <a name="encoding-for-streaming"></a>Encodage pour la diffusion en continu

Comme son nom l’indique, si vous utilisez la présélection **AdaptiveStreaming** lors de la création d’une transformation d’encodage, vous obtenez une sortie qui convient pour une livraison via les protocoles de diffusion en continu tels que HLS, DASH, CMAF, etc. Lorsque vous utilisez cette présélection **AdaptiveStreaming**, l’encodeur détermine intelligemment le nombre de couches vidéo à générer ainsi que le débit et la résolution. L’élément multimédia de sortie contient des fichiers MP4 où l’audio encodé en AAC et la vidéo encodée en H.264 ne sont pas entrelacés.

Pour voir un exemple d’utilisation de cette présélection, consultez [Diffuser un fichier](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Sortie

Cette section présente trois exemples de couches vidéo de sortie produites par l’encodeur Media Services suite à un encodage avec la présélection **AdaptiveStreaming**. Dans tous les cas, la sortie contient des fichiers MP4 audio uniquement avec des données audio stéréo encodées à 128 Kbits/s.

### <a name="example-1"></a>Exemple 1
Une source avec une hauteur de « 1080 » et une fréquence d’images de « 29.970 » crée 6 couches vidéo :

|Couche|Hauteur|Largeur|Vitesse de transmission (Kbits/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1 280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5.|270|480|720|
|6.|180|320|380|

### <a name="example-2"></a>Exemple 2
Une source avec une hauteur de « 720 » et une fréquence d’images de « 23.970 » crée 5 couches vidéo :

|Couche|Hauteur|Largeur|Vitesse de transmission (Kbits/s)|
|---|---|---|---|
|1|720|1 280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5.|180|320|320|

### <a name="example-3"></a>Exemple 3
Une source avec une hauteur de « 360 » et une fréquence d’images de « 29.970 » crée 3 couches vidéo :

|Couche|Hauteur|Largeur|Vitesse de transmission (Kbits/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Diffuser un fichier](stream-files-dotnet-quickstart.md)
