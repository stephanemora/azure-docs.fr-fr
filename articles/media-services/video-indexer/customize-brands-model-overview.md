---
title: Personnaliser un modèle de marques dans Video Indexer - Azure
titlesuffix: Azure Media Services
description: Cet article donne une vue d’ensemble de modèle de marques dans Video Indexer et explique comment le personnaliser.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/05/2018
ms.author: anzaman
ms.openlocfilehash: 7df709adbd8e45712c112b52fc76920f8b67fe91
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53282955"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Personnaliser un modèle de marques dans Video Indexer

Video Indexer prend en charge la détection de marques dans les messages vocaux et visuels lors de l’indexation et de la réindexation de contenu vidéo et audio. La fonctionnalité de détection de marques identifie les noms de produits, services et entreprises suggérés par la base de données de marques Bing. Par exemple, si le nom de Microsoft est mentionné dans du contenu vidéo ou audio, ou s’il apparaît sous forme de texte visuel dans une vidéo, Video Indexer le détecte et l’interprète comme un nom de marque dans le contenu. Les marques sont différenciées des autres termes à l’aide du contexte.

La détection de marques est utile dans de nombreux scénarios d’entreprise tels que l’archivage et la découverte de contenus, la publicité contextuelle, l’analyse des réseaux sociaux, l’analyse de la concurrence dans la vente au détail, et bien plus encore. La détection des marques avec Video Indexer vous permet d’indexer les mentions de marques avec la reconnaissance vocale et visuelle du texte, la base de données de marques de Bing, ainsi qu’avec la personnalisation, en créant un modèle personnalisé de marques pour chaque compte Video Indexer. La fonction de modèle de marques personnalisé vous permet de choisir si Video Indexer doit détecter ou non les marques répertoriées dans la base de données de marques Bing, ne pas détecter certaines marques (création d’une liste noire de marques) et inclure d’autres marques en plus de celles répertoriées dans la base de données de marques Bing (création d’une liste blanche de marques).

## <a name="out-of-the-box-detection-example"></a>Exemple de détection prête à l’emploi

Dans la présentation [Microsoft Build 2017 Jour 2](https://www.videoindexer.ai/media/ed6ede78ad/), la marque « Microsoft Windows » apparaît plusieurs fois. Parfois dans la transcription, parfois dans du texte visuel et jamais verbatim. Video Indexer détecte avec une grande précision qu’un terme est effectivement une marque selon le contexte, couvrant plus de 90 000 marques dès le départ, avec une mise à jour continue de la base de données. À 02:25, Video Indexer détecte la marque à partir de la reconnaissance vocale, puis à nouveau à 02:40 à partir du texte visuel, qui fait partie du logo Windows.

![Vue d’ensemble des marques](./media/content-model-customization/brands-overview.png)

La mention en anglais de windows (fenêtres) dans le contexte de la construction ne déclenche pas la détection de « Windows » en tant que marque. Il en est de même pour les termes Box, Apple, Fox, etc., grâce aux algorithmes de Machine Learning avancés qui savent différencier les termes en fonction du contexte. La détection des marques fonctionne pour toutes les langues prises en charge. Cliquez ici pour consulter [l’index et la vidéo complète du discours d’ouverture Microsoft Build 2017 Jour 2](http://www.videoindexer.ai/media/ed6ede78ad/).

Pour inclure vos propres marques, consultez la section [Étapes suivantes](#next-stpes).

## <a name="next-steps"></a>Étapes suivantes

[Personnaliser le modèle de marques à l’aide des API](customize-brands-model-with-api.md)

[Personnaliser le modèle de marques à l’aide du site web](customize-brands-model-with-website.md)
