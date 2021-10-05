---
title: Gérer la vidéo pendant les appels
titleSuffix: An Azure Communication Services how-to guide
description: Utilisez les kits de développement logiciel (SDK) Azure Communication Services pour gérer les appels vidéo.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-web-ios-android
ms.openlocfilehash: 536dc2233ca0ae0773c9e953d35bee6ee4a4da40
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700633"
---
# <a name="manage-video-during-calls"></a>Gérer la vidéo pendant les appels

Découvrez comment gérer les appels vidéo avec les kits de développement logiciel (SDK) Azure Communication Services. Nous allons apprendre à gérer la réception et l’envoi de vidéo pendant un appel.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../quickstarts/create-communication-resource.md).
- Un jeton d’accès utilisateur pour activer le client appelant. Pour plus d’informations, consultez [Créer et gérer des jetons d’accès](../../quickstarts/access-tokens.md).
- Facultatif : suivez le guide de démarrage rapide permettant d’[ajouter l’appel vocal à votre application](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-web"
[!INCLUDE [Manage Video Calls JavaScript](./includes/manage-video/manage-video-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Manage Video Calls Android](./includes/manage-video/manage-video-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Manage Video Calls iOS](./includes/manage-video/manage-video-ios.md)]
::: zone-end

## <a name="next-steps"></a>Étapes suivantes
- [Découvrez comment gérer les appels](./manage-calls.md)
- [Découvrez comment enregistrer les appels](./record-calls.md)
- [Découvrez comment transcrire les appels](./call-transcription.md)
