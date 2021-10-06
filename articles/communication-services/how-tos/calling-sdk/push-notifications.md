---
title: Activer les notifications Push pour des appels
titleSuffix: An Azure Communication Services how-to guide
description: Utilisez les SDK Azure Communication Services pour activer les notifications push pour des appels.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-ios-android
ms.openlocfilehash: 87e9aa1741808e60af14eb32e1837bc87c280062
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635714"
---
# <a name="enable-push-notifications-for-calls"></a>Activer les notifications Push pour des appels

Ici, nous allons découvrir comment activer les notifications Push pour les appels Azure Communication Services. La configuration de ces paramètres permet aux utilisateurs de savoir quand ils ont un appel entrant, auquel ils peuvent alors répondre.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../quickstarts/create-communication-resource.md).
- Un jeton d’accès utilisateur pour activer le client appelant. Pour plus d’informations, consultez [Créer et gérer des jetons d’accès](../../quickstarts/access-tokens.md).
- Facultatif : Suivez le guide de démarrage rapide pour [ajouter l’appel vocal à votre application](../../quickstarts/voice-video-calling/getting-started-with-calling.md).

::: zone pivot="platform-android"
[!INCLUDE [Enable push notifications Android](./includes/push-notifications/push-notifications-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Enable push notifications iOS](./includes/push-notifications/push-notifications-ios.md)]
::: zone-end

## <a name="next-steps"></a>Étapes suivantes
- [Découvrir comment s’abonner à des événements](./events.md)
- [Découvrir comment gérer les appels](./manage-calls.md)
- [Découvrir comment gérer la vidéo](./manage-video.md)
