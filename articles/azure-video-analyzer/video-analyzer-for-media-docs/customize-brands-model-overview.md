---
title: Personnaliser un modèle de marques dans Azure Video Analyzer for Media (anciennement Video Indexer) - Azure
titleSuffix: Azure Media Services
description: Cet article décrit ce qu’est un modèle de marques dans Azure Video Analyzer for Media (anciennement Video Indexer) et explique comment le personnaliser.
services: media-services
author: Juliako
manager: femila
ms.topic: article
ms.date: 12/15/2019
ms.author: juliako
ms.openlocfilehash: dda0777bace36699f9f02cadebc357a0d9d4fc84
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386020"
---
# <a name="customize-a-brands-model-in-video-analyzer-for-media"></a>Personnaliser un modèle de marques dans Video Analyzer for Media

Azure Video Analyzer for Media (anciennement Video Indexer) prend en charge la détection de marques dans les messages vocaux et visuels lors de l’indexation et de la réindexation de contenu vidéo et audio. La fonctionnalité de détection de marques identifie les noms de produits, services et entreprises suggérés par la base de données de marques Bing. Par exemple, si le nom de Microsoft est mentionné dans du contenu vidéo ou audio, ou s’il apparaît sous forme de texte visuel dans une vidéo, Video Analyzer for Media le détecte et l’interprète comme un nom de marque dans le contenu. Les marques sont différenciées des autres termes à l’aide du contexte.

La détection de marques est utile dans de nombreux scénarios d’entreprise tels que l’archivage et la découverte de contenus, la publicité contextuelle, l’analyse des réseaux sociaux, l’analyse de la concurrence dans la vente au détail, et bien plus encore. La détection des marques avec Video Analyzer for Media vous permet d’indexer les mentions de marques avec la reconnaissance vocale et visuelle du texte, la base de données de marques de Bing ainsi qu’avec la personnalisation, en créant un modèle personnalisé de marques pour chaque compte Video Analyzer for Media. La fonctionnalité de modèle de marques personnalisé vous permet de choisir si Video Analyzer for Media doit détecter ou non les marques répertoriées dans la base de données de marques Bing, ne pas détecter certaines marques (création d’une liste de marques non approuvées) et inclure d’autres marques en plus de celles répertoriées dans la base de données de marques Bing (création d’une liste de marques approuvées). Le modèle de marque que vous créez ne sera disponible que dans le compte dans lequel vous l’avez créé.

## <a name="out-of-the-box-detection-example"></a>Exemple de détection prête à l’emploi

Dans la présentation « Microsoft Build 2017 Jour 2 », la marque « Microsoft Windows » apparaît plusieurs fois. Parfois dans la transcription, parfois dans du texte visuel et jamais verbatim. Video Analyzer for Media détecte avec une grande précision qu’un terme est effectivement une marque selon le contexte, couvrant plus de 90 000 marques dès le départ, avec une mise à jour continue de la base de données. À 02:25, Video Analyzer for Media détecte la marque à partir de la reconnaissance vocale, puis à nouveau à 02:40 à partir du texte visuel, qui fait partie du logo Windows.

![Vue d’ensemble des marques](./media/content-model-customization/brands-overview.png)

La mention en anglais de windows (fenêtres) dans le contexte de la construction ne déclenche pas la détection de « Windows » en tant que marque. Il en est de même pour les termes Box, Apple, Fox, etc., grâce aux algorithmes de Machine Learning avancés qui savent différencier les termes en fonction du contexte. La détection des marques fonctionne pour toutes les langues prises en charge.  

## <a name="next-steps"></a>Étapes suivantes

Pour inclure vos propres marques, consultez les rubriques suivantes :

[Personnaliser le modèle de marques à l’aide des API](customize-brands-model-with-api.md)

[Personnaliser le modèle de marques à l’aide du site web](customize-brands-model-with-website.md)
