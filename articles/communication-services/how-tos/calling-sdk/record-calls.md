---
title: Gérer l’enregistrement d’appel sur le client
titleSuffix: An Azure Communication Services how-to guide
description: Utilisez les Kits de développement logiciel (SDK) Azure Communication Services pour gérer l’enregistrement d’appel sur le client.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.topic: how-to
ms.subservice: calling
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-web-ios-android
ms.openlocfilehash: 5ef33ef46b208f66fa7d31b20bb603cd543880f6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700632"
---
# <a name="manage-call-recording-on-the-client"></a>Gérer l’enregistrement d’appel sur le client

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

L’[enregistrement d’appel](../../concepts/voice-video-calling/call-recording.md) permet à vos utilisateurs d’enregistrer leurs appels effectués avec Azure Communication Services. Ici, nous allons apprendre à gérer l’enregistrement côté client. Pour que cela puisse fonctionner, vous devez configurer l’enregistrement [côté serveur](../../quickstarts/voice-video-calling/call-recording-sample.md).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../quickstarts/create-communication-resource.md).
- Un jeton d’accès utilisateur pour activer le client appelant. Pour plus d’informations, consultez [Créer et gérer des jetons d’accès](../../quickstarts/access-tokens.md).
- Facultatif : Suivez le démarrage rapide permettant d’[ajouter l’appel vocal à votre application](../../quickstarts/voice-video-calling/getting-started-with-calling.md).

::: zone pivot="platform-web"
[!INCLUDE [Record Calls Client-side JavaScript](./includes/record-calls/record-calls-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Record Calls Client-side Android](./includes/record-calls/record-calls-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Record Calls Client-side iOS](./includes/record-calls/record-calls-ios.md)]
::: zone-end

## <a name="next-steps"></a>Étapes suivantes
- [Découvrir comment gérer les appels](./manage-calls.md)
- [Découvrir comment gérer la vidéo](./manage-video.md)
- [Découvrir comment transcrire les appels](./call-transcription.md)