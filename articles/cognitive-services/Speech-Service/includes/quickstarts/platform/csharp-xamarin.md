---
title: 'Démarrage rapide : Configuration de la plateforme du SDK Speech pour C# (Xamarin) – Service Speech'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour configurer votre plateforme pour C# Xamarin avec le kit SDK du service Speech.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: eb5d842d5934a46b98a7707b6f567c26445df70e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253868"
---
Ce guide montre comment installer le [kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour [Xamarin](/xamarin/get-started/what-is-xamarin), une plateforme open source pour la création d’applications modernes et performantes pour iOS, Android et Windows avec .NET. Si vous voulez simplement le nom du package pour commencer seul, exécutez `Install-Package Microsoft.CognitiveServices.Speech` dans la console NuGet.

> [!NOTE]
> Le SDK Speech pour Xamarin prend en charge Windows Desktop (x86 et x64), ou la plateforme Windows universelle (x86, x64, ARM/ARM64), Android (x86, ARM32/64) et iOS (simulateur x64 et ARM64).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* Sur Windows, vous avez besoin de [Microsoft Redistributable Visual C++ pour Visual Studio 2019](https://support.microsoft.com/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) pour votre plateforme. La première installation peut demander i, redémarrage.
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Créer un projet Visual Studio et installer le Kit de développement logiciel (SDK) Speech

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

Le kit de développement logiciel (SDK) Speech est maintenant installé. Vous pouvez maintenant supprimer ou réutiliser le projet « HelloWorld » que vous avez créé au cours des étapes précédentes.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [windows](../quickstart-list.md)]
