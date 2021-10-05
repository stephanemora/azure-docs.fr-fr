---
title: Gérer les appels
titleSuffix: An Azure Communication Services how-to guide
description: Utilisez les kits de développement logiciel (SDK) Azure Communication Services pour gérer les appels.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-web-ios-android-windows
ms.openlocfilehash: 4abf92e2d3003d8944ea75c411bc9699716e4684
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700634"
---
# <a name="manage-calls"></a>Gérer les appels

Découvrez comment gérer les appels avec les kits de développement logiciel (SDK) Azure Communication Services. Nous allons apprendre à passer des appels, ainsi qu’à gérer leurs participants et leurs propriétés.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../quickstarts/create-communication-resource.md).
- Un `User Access Token` pour activer le client d’appel. Pour en savoir plus, consultez [Comment obtenir un `User Access Token`](../../quickstarts/access-tokens.md)
- Facultatif : Suivez le guide de démarrage rapide pour [prendre en main l’ajout des appels à votre application](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-web"
[!INCLUDE [Manage Calls JavaScript](./includes/manage-calls/manage-calls-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Manage Calls Android](./includes/manage-calls/manage-calls-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Manage Calls iOS](./includes/manage-calls/manage-calls-ios.md)]
::: zone-end

::: zone pivot="platform-windows"
[!INCLUDE [Manage Calls Windows](./includes/manage-calls/manage-calls-windows.md)]
::: zone-end

## <a name="next-steps"></a>Étapes suivantes
- [Découvrez comment gérer la vidéo](./manage-video.md)
- [Découvrez comment enregistrer les appels](./record-calls.md)
- [Découvrez comment transcrire les appels](./call-transcription.md)