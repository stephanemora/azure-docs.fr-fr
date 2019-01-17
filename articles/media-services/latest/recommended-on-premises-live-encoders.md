---
title: Découvrez le streaming en direct d’encodeurs locaux recommandé par Media Services – Azure | Microsoft Docs
description: Découvrez le streaming en direct d’encodeurs locaux recommandé par Media Services
services: media-services
keywords: encodage;encodeurs;média
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 12/14/2018
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 4d25e4cd94fec35f31594544b619aa054a35d58d
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54302338"
---
# <a name="recommended-live-streaming-encoders"></a>Encodeurs de streaming en direct recommandés

Dans Media Services, un [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) (canal) représente un pipeline de traitement du contenu de streaming en direct. Un LiveEvent reçoit des flux d’entrée en direct de l’une des deux manières suivantes :

* Un encodeur live local envoie au LiveEvent un paquet RTMP ou Smooth Streaming (MP4 fragmenté) multidébit qui n’est pas activé pour effectuer un encodage en temps réel avec Media Services. Les flux ingérés transitent par les LiveEvents sans traitement supplémentaire. Cette méthode est appelée **pass-through**. Un encodeur live peut envoyer un flux unique à un canal de transmission directe, mais cette configuration n’est pas recommandée, car elle n’offre pas de streaming à débit adaptatif au client.

  > [!NOTE]
  > L’utilisation d’une méthode pass-through est le moyen le plus économique de diffuser une vidéo en flux continu.

* Un encodeur live local envoie un flux à débit unique au LiveEvent activé pour effectuer un encodage en temps réel avec Media Services dans l’un des formats suivants : RTMP ou Smooth Streaming (MP4 fragmenté). Le LiveEvent procède ensuite à l’encodage en temps réel du flux à débit unique entrant en flux vidéo multidébit (adaptatif).

Pour des informations détaillées sur l’encodage en temps réel avec Media Services, voir [Streaming en direct avec Media Services v3](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>Encodeurs live qui génèrent une sortie RTMP

Media Services recommande l’utilisation d’un des encodeurs live suivants, qui génèrent une sortie RTMP : Les schémas d’URL pris en charge sont `rtmp://` ou `rtmps://`.

> [!NOTE]
 > Lors de la diffusion en continu via RTMP, vérifiez les paramètres de pare-feu et/ou de proxy pour confirmer que les ports TCP sortants 1935 et 1936 sont ouverts.<br/>
 Lors de la diffusion en flux continu via RTMP, vérifiez les paramètres de pare-feu et/ou de proxy pour confirmer que les ports TCP sortants 2935 et 2936 sont ouverts.

- Adobe Flash Media Live Encoder 3.2
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>Encodeurs live qui génèrent une sortie MP4 fragmenté

Media Services recommande l’utilisation d’un des encodeurs live suivants, qui génèrent une sortie Smooth Streaming multidébit (MP4 fragmenté) : Les schémas d’URL pris en charge sont `http://` ou `https://`.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3
- Media Excel Hero Live et Hero 4K (UHD/HEVC)

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Comment devenir un partenaire d’encodeur local

En tant que partenaire d’encodeur local d’Azure Media Services, Media Services promeut votre produit en recommandant votre encodeur aux clients d’entreprise. Pour devenir un partenaire d’encodeur local, vous devez vérifier la compatibilité de votre encodeur local avec Media Services. Pour ce faire, effectuez les vérifications suivantes :

### <a name="pass-through-liveevent-verification"></a>Vérification de LiveEvent pass-through

1. Créez ou visitez votre compte Azure Media Services
2. Créez et démarrez un LiveEvent **pass-through**.
3. Configurez votre encodeur pour transmettre un flux live à multidébit.
4. Créez un événement en temps réel publié.
5. Exécutez votre encodeur live pendant environ 10 minutes.
6. Arrêtez l’événement en temps réel.
7. Créez, démarrez un point de terminaison de streaming, utilisez un lecteur, par exemple [Lecteur multimédia Azure](https://ampdemo.azureedge.net/azuremediaplayer.html) pour regarder l’élément multimédia archivé pour vous assurer que la lecture est dépourvue de problèmes visibles pour tous les niveaux de qualité (vous pouvez également regarder et valider du contenu via l’URL d’aperçu pendant la session active avant l’étape 6).
8. Enregistrez l’ID de la ressource, l’URL de streaming publié pour l’archive en temps réel, ainsi que les paramètres et la version utilisée à partir de votre encodeur live.
9. Réinitialisez l’état du LiveEvent après la création de chaque exemple.
10. Répétez les étapes 3 à 9 pour toutes les configurations prises en charge par votre encodeur (avec et sans signalisation des annonces/légendes/différentes vitesses de codage).

### <a name="live-encoding-liveevent-verification"></a>Vérification de LiveEvent d’encodage en temps réel

1. Créez ou visitez votre compte Azure Media Services
2. Créer et démarrer un LiveEvent d’**encodage en temps réel**
3. Configurez votre encodeur pour transmettre un flux live à débit unique.
4. Créez un événement en temps réel publié.
5. Exécutez votre encodeur live pendant environ 10 minutes.
6. Arrêtez l’événement en temps réel.
7. Créez, démarrez un point de terminaison de streaming, utilisez un lecteur, par exemple [Lecteur multimédia Azure](https://ampdemo.azureedge.net/azuremediaplayer.html) pour regarder l’élément multimédia archivé pour vous assurer que la lecture est dépourvue de problèmes visibles pour tous les niveaux de qualité (vous pouvez également regarder et valider du contenu via l’URL d’aperçu pendant la session active avant l’étape 6).
8. Enregistrez l’ID de la ressource, l’URL de streaming publié pour l’archive en temps réel, ainsi que les paramètres et la version utilisée à partir de votre encodeur live.
9. Réinitialisez l’état du LiveEvent après la création de chaque exemple.
10. Répétez les étapes 3 à 9 pour toutes les configurations prises en charge par votre encodeur (avec et sans signalisation des annonces/légendes/différentes vitesses de codage).

### <a name="longevity-verification"></a>Vérification de longévité

1. Créez ou visitez votre compte Azure Media Services
2. Créez et démarrez un canal **pass-through**.
3. Configurez votre encodeur pour transmettre un flux live à multidébit.
4. Créez un événement en temps réel publié.
5. Exécutez votre encodeur live pendant une semaine ou plus
6. Utilisez un lecteur, par exemple [Lecteur multimédia Azure](https://ampdemo.azureedge.net/azuremediaplayer.html) pour regarder de temps en temps le streaming en direct (ou l’élément multimédia archivé) afin de vous assurer que la lecture est dépourvue de problèmes visibles.
7. Arrêtez l’événement en temps réel.
8. Enregistrez l’ID de la ressource, l’URL de streaming publié pour l’archive en temps réel, ainsi que les paramètres et la version utilisée à partir de votre encodeur live.

Enfin, envoyez par e-mail vos paramètres enregistrés et paramètres d’archivage en direct à Azure Media Services à l’adresse amsstreaming@microsoft.com en guise de notification indiquant que tous les contrôles de vérification automatique ont réussi. Incluez également vos informations de contact à des fins de suivi. Vous pouvez contacter l’équipe Azure Media Services pour toute question concernant ce processus.

## <a name="next-steps"></a>Étapes suivantes

[Streaming en direct avec Media Services v3](live-streaming-overview.md)
