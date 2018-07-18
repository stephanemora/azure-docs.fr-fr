---
title: Régions du service de reconnaissance vocale | Microsoft Docs
description: Informations de référence pour les régions du service de reconnaissance vocale.
services: cognitive-services
author: mahilleb-msft
manager: wolmfa61
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/27/2018
ms.author: mahilleb
ms.openlocfilehash: a201cc043f673e2285ea48950804d97b96f881ed
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054908"
---
# <a name="regions-and-endpoints-of-the-speech-service"></a>Régions et points de terminaison du service de reconnaissance vocale

> [!NOTE]
> Les noms de région dans le [kit SDK de reconnaissance vocale](speech-sdk.md) correspondent à la première partie du domaine des points de terminaison indiqués ci-dessous.
> Par exemple, utilisez `westus` pour spécifier la région Ouest des États-Unis dans le SDK de reconnaissance vocale.

## <a name="speech-to-text"></a>Reconnaissance vocale

[!include[](includes/endpoints-speech-to-text.md)]

## <a name="text-to-speech"></a>Synthèse vocale

[!include[](includes/endpoints-text-to-speech.md)]

## <a name="authentication"></a>Authentification

[!include[](includes/endpoints-token-service.md)]

Vous trouverez [ici](rest-apis.md#authentication) des détails sur l’obtention et l’actualisation des jetons d’autorisation.

## <a name="language-understanding-speech-sdk-only"></a>Language Understanding (SDK de reconnaissance vocale uniquement)

Les régions pour le service Language Understanding sont répertoriées [ici](/azure/cognitive-services/luis/luis-reference-regions).
Dans le SDK de reconnaissance vocale, spécifiez ces régions dans la première partie du nom de domaine du point de terminaison (par exemple, `westus`).
