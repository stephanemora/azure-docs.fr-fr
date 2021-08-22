---
title: Lecture d’enregistrements vidéo – Azure Video Analyzer
description: Vous pouvez utiliser Azure Video Analyzer pour l’enregistrement vidéo continu qui vous permet d’enregistrer des vidéos dans le cloud pendant des semaines ou des mois. Vous pouvez également limiter votre enregistrement aux clips qui présentent un intérêt, par le biais de l’enregistrement basé sur les événements. Cet article explique comment lire de tels enregistrements.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 415edea10eaf635b88786d33bbd439bc386a57fc
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602182"
---
# <a name="playback-of-video-recordings"></a>Lecture d’enregistrements vidéo 

## <a name="pre-read"></a>Lecture préalable  

* [Enregistrement vidéo en continu](continuous-video-recording.md)
* [Enregistrement de vidéo basé sur les événements](event-based-video-recording-concept.md)

## <a name="background"></a>Arrière-plan  

Vous pouvez utiliser Azure Video Analyzer pour l’[enregistrement vidéo continu](continuous-video-recording.md) (CVR), ce qui vous permet d’enregistrer de la vidéo dans le cloud pendant des semaines ou des mois. Vous pouvez également limiter votre enregistrement aux clips qui présentent un intérêt, par le biais de l’[enregistrement de vidéo basé sur les événements](event-based-video-recording-concept.md) (EVR). Dans les deux cas, si votre [pipeline](pipeline.md) utilise l’intelligence artificielle pour générer des résultats d’inférence, vous devez [enregistrer ces résultats en même temps que la vidéo](record-stream-inference-data-with-video.md). 

Si vous évaluez les capacités du service Video Analyzer, vous devez suivre le [tutoriel sur CVR](use-continuous-video-recording.md) ou le [tutoriel sur la lecture de vidéo avec des métadonnées d’inférence](record-stream-inference-data-with-video.md) dans lesquelles vous liriez les enregistrements à l’aide du portail Azure.

Si vous créez une application ou un service à l’aide d’API de Video Analyzer, vous devez consulter les informations suivantes pour comprendre comment lire des enregistrements, en plus de consulter l’article sur les [stratégies d’accès](access-policies.md) et sur le [widget du lecteur de Video Analyzer](player-widget.md).

<!-- TODO - add a section here about 1P/3P SaaS and how to use widgets to allow end users to view videos without talking to ARM APIs -->

## <a name="determining-that-a-video-recording-is-ready-for-playback"></a>Détermination du fait qu’un enregistrement vidéo est prêt pour la lecture

Votre compte Video Analyzer est lié à un compte de stockage Azure et, quand vous enregistrez une vidéo dans le cloud, le contenu est écrit sur une [ressource vidéo](terminology.md#video). Vous pouvez [diffuser ce contenu en continu](terminology.md#streaming), soit une fois l’enregistrement terminé, soit pendant l’enregistrement. Cela est signalé à l’aide de l’[indicateur](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-05-01-preview/Videos.json) `canStream` qui sera défini sur `true` pour la ressource vidéo. 

## <a name="video-analyzer-player-widget"></a>Widget du lecteur de Video Analyzer
Le service Video Analyzer offre les capacités nécessaires pour diffuser des flux via les protocoles HLS ou MPEG-DASH à des appareils de lecture (clients). Vous pouvez utiliser le widget du lecteur de Video Analyzer pour obtenir l’URL de diffusion en continu et le jeton d’autorisation de lecture, et utiliser ceux-ci dans des applications clientes pour lire la vidéo et les métadonnées de l’inférence.

Vous pouvez installer le widget du lecteur de Video Analyzer pour lire les enregistrements vidéo. Vous pouvez installer le widget l’aide de `npm` ou de `yarn`, ce qui vous permet de l’inclure dans votre propre application côté client. Exécutez l’une des commandes suivantes pour inclure le widget dans votre propre application :

NPM :
```
npm install –-save @azure/video-analyzer-widgets
```
YARN :
```
yarn add @azure/video-analyzer-widgets 
```
Vous pouvez également incorporer un script prédéfini existant en ajoutant type="module" à l’élément de script référençant l’emplacement prédéfini en suivant l’exemple suivant :

```
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script> 
``` 

## <a name="recording-and-playback-latencies"></a>Latences d’enregistrement et de lecture

Lorsque vous utilisez Video Analyzer pour enregistrer sur une ressource vidéo, vous spécifiez une [propriété `segmentLength`](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzer.json) qui indique au module d’agréger une durée minimale de vidéo (en secondes) avant de l’écrite dans le cloud. Par exemple, si `segmentLength` est défini sur 300, le module accumule 5 minutes de vidéo avant de charger un « bloc » de 5 minutes, puis passe en mode accumulation pendant les 5 minutes suivantes avant de charger à nouveau. L’augmentation de la valeur de `segmentLength` présente l’avantage de réduire les coûts de transaction du service Stockage Azure, car le nombre de lectures et d’écritures n’est pas plus fréquent qu’une fois toutes les `segmentLength` secondes.

Par conséquent, la diffusion en continu de la vidéo à partir de votre compte Video Analyzer sera retardée d’au moins autant de temps. 

Un autre facteur qui détermine la latence de lecture (retard entre le moment où un événement se produit devant la caméra et le moment où il peut être lu sur un périphérique de lecture) est la durée du groupe d’images [GOP](https://en.wikipedia.org/wiki/Group_of_pictures). Comme l’explique [la réduction du retard des streams en direct par l’utilisation de trois techniques simples](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641), plus la durée du GOP est longue, plus la latence est importante. Il est courant d’utiliser des caméras IP dans des scénarios de surveillance et de sécurité qui soient configurées pour utiliser des GOP de plus de 30 secondes. Cela a un impact important sur la latence globale.

## <a name="next-steps"></a>Étapes suivantes

[Didacticiel sur l’enregistrement de vidéo en continu](use-continuous-video-recording.md)
