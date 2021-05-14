---
title: Guide pratique pour assembler deux fichiers vidéo ou plus avec .NET | Microsoft Docs
description: Cet article vous montre comment assembler deux fichiers vidéo ou plus.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 562398b17ec5adaaa590b36c1e30e9818592a485
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109684808"
---
# <a name="how-to-stitch-two-or-more-video-files-with-net"></a>Guide pratique pour assembler deux fichiers vidéo ou plus avec .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="stitch-two-or-more-video-files"></a>Assembler deux fichiers vidéo ou plus

L’exemple suivant montre comment générer un préréglage pour assembler deux fichiers vidéo ou plus. Le scénario le plus courant consiste à ajouter une amorce de début ou de fin à la vidéo principale.

> [!NOTE]
> Les fichiers vidéo modifiés doivent partager des propriétés (résolution vidéo, fréquence d’images, nombre de pistes audio, etc.). Vous devez prendre soin de ne pas mélanger des vidéos de différentes fréquences d’images ou comportant un nombre différent de pistes audio.

## <a name="prerequisites"></a>Prérequis

Clonez ou téléchargez les [exemples .NET Media Services](https://github.com/Azure-Samples/media-services-v3-dotnet/).  Le code référencé ci-dessous se trouve dans le [dossier EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoEncoding/Encoding_StitchTwoAssets/Program.cs).
