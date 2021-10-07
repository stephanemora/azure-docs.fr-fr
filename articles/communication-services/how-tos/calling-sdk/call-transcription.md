---
title: Afficher l’état de transcription des appels sur le client
titleSuffix: An Azure Communication Services how-to guide
description: Utiliser les SDK Azure Communication Services pour afficher l’état de transcription des appels
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-ios-android
ms.openlocfilehash: 9304f7905b12362f8026e59af8cf294ed76fc42e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635738"
---
# <a name="display-call-transcription-state-on-the-client"></a>Afficher l’état de transcription des appels sur le client

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

Quand vous utilisez la transcription d’appel, vous pouvez indiquer à vos utilisateurs qu’un appel est en cours de transcription. Voici comment procéder.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../quickstarts/create-communication-resource.md).
- Un jeton d’accès utilisateur pour activer le client appelant. Pour plus d’informations, consultez [Créer et gérer des jetons d’accès](../../quickstarts/access-tokens.md).
- Facultatif : Suivez le guide de démarrage rapide pour [ajouter l’appel vocal à votre application](../../quickstarts/voice-video-calling/getting-started-with-calling.md).

::: zone pivot="platform-android"
[!INCLUDE [Call transcription client-side Android](./includes/call-transcription/call-transcription-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Call transcription client-side iOS](./includes/call-transcription/call-transcription-ios.md)]
::: zone-end

## <a name="next-steps"></a>Étapes suivantes
- [Découvrir comment gérer la vidéo](./manage-video.md)
- [Découvrir comment gérer les appels](./manage-calls.md)
- [Découvrez comment enregistrer des appels](./record-calls.md)