---
title: Personnaliser un modèle de personne dans Video Indexer - Azure
titlesuffix: Azure Media Services
description: Cet article présente les modèles de personne dans Video Indexer et explique comment le personnaliser.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/05/2018
ms.author: anzaman
ms.openlocfilehash: 073cff22f17f496c2ff85cfbf716751dfea1e03e
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283235"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Personnaliser un modèle de personne dans Video Indexer


Video Indexer prend en charge la détection de visage et la reconnaissance de célébrités pour le contenu vidéo. La fonctionnalité de reconnaissance de célébrités couvre environ un million de visages en fonction de la source de données couramment demandée comme IMDB, Wikipedia et les principaux influenceurs LinkedIn. Les visages qui ne sont pas reconnus par la fonctionnalité de reconnaissance de célébrités sont détectés ; toutefois, ils sont laissés sans nom. Après avoir chargé votre vidéo sur Video Indexer et obtenu des résultats, vous pouvez revenir en arrière et nommer les visages qui n’ont pas été reconnus. Une fois que vous étiquetez un visage avec un nom, le visage et le nom sont ajoutés au modèle de personne de votre compte. Video Indexer peut alors reconnaître ce visage dans vos vidéos, futures et anciennes.

Vous pouvez utiliser le site web ou l’API Video Indexer pour modifier des visages détectés dans une vidéo sur votre compte, comme décrit dans les rubriques suivantes :

- [Personnaliser le modèle de personne à l’aide des API](customize-person-model-with-api.md)
- [Personnaliser le modèle de personne à l’aide du site web](customize-person-model-with-website.md)
