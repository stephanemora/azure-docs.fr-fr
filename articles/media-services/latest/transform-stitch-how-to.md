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
ms.openlocfilehash: c94976fd494cd4745a5da95557f3e6a4954e721a
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107930656"
---
# <a name="how-to-stitch-two-or-more-video-files-with-net"></a>Guide pratique pour assembler deux fichiers vidéo ou plus avec .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="stitch-two-or-more-video-files"></a>Assembler deux fichiers vidéo ou plus

L’exemple suivant montre comment générer un préréglage pour assembler deux fichiers vidéo ou plus. Le scénario le plus courant consiste à ajouter une amorce de début ou de fin à la vidéo principale.

> [!NOTE]
> Les fichiers vidéo modifiés doivent partager des propriétés (résolution vidéo, fréquence d’images, nombre de pistes audio, etc.). Vous devez prendre soin de ne pas mélanger des vidéos de différentes fréquences d’images ou comportant un nombre différent de pistes audio.

## <a name="prerequisites"></a>Prérequis

Clonez ou téléchargez les [exemples .NET Media Services](https://github.com/Azure-Samples/media-services-v3-dotnet/).  Le code référencé ci-dessous se trouve dans le [dossier EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs).
