---
title: Comment enchaîner deux fichiers vidéo ou plus avec .NET | Microsoft Docs
description: Cet article vous montre comment enchaîner deux fichiers vidéo ou plus.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5eacc366961d3101a7eaf8877a34ef2d462ea76b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281315"
---
# <a name="how-to-stitch-two-or-more-video-files-with-net"></a>Comment enchaîner deux fichiers vidéo ou plus avec .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="stitch-two-or-more-video-files"></a>Enchaîner deux fichiers vidéo ou plus

L’exemple suivant décrit comment générer une présélection pour enchaîner deux fichiers vidéo ou plus. Le scénario le plus courant consiste à ajouter une amorce de début ou de fin à la vidéo principale.

> [!NOTE]
> Les fichiers vidéo en cours de modification doivent partager des propriétés (résolution vidéo, fréquence d’images, nombre de pistes audio, etc.). Vous devez prendre soin de ne pas mélanger des vidéos de différentes fréquences d’images ou comportant un nombre différent de pistes audio.

## <a name="prerequisites"></a>Prérequis

Clonez ou téléchargez les [exemples .NET Media Services](https://github.com/Azure-Samples/media-services-v3-dotnet/).  Le code référencé ci-dessous se trouve dans le [dossier EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs).

### <a name="net-code"></a>Code .NET

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs)]
