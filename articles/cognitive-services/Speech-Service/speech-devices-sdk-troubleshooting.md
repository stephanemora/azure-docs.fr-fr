---
title: Dépanner les appareils de Speech SDK - Services de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Cet article fournit des informations pour vous aider à résoudre les problèmes que vous pouvez rencontrer lorsque vous utilisez le SDK d’appareils de reconnaissance vocale.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: wellsi
ms.openlocfilehash: 87fb35f06dcb1d1e3fb8c3ae3be64c7448162f14
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026155"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Dépanner les appareils de Speech SDK

Cet article fournit des informations pour vous aider à résoudre les problèmes que vous pouvez rencontrer lorsque vous utilisez le SDK d’appareils de reconnaissance vocale.

## <a name="certificate-failures"></a>Erreurs de certificat

Si vous obtenez des erreurs de certificat lorsque vous utilisez les Services de reconnaissance vocale, assurez-vous que votre appareil dispose de la date et l’heure :

1. Accédez à **Settings**. Sous **System** (Système), sélectionnez **Date & time** (Date et heure).

    ![Sélection du paramètre de date et d’heure](media/speech-devices-sdk/qsg-12.png)

1. Laissez l’option **Automatic date & time** (Date et heure automatiques) sélectionnée. Sous **Select time zone** (Sélectionner un fuseau horaire), sélectionnez votre fuseau horaire actuel.

    ![Sélection des options de date et de fuseau horaire](media/speech-devices-sdk/qsg-13.png)

    Lorsque vous voyez que l’heure du kit de développement correspond à l’heure de votre PC, le kit de développement est connecté à Internet.

## <a name="next-steps"></a>Étapes suivantes

* [Examiner les notes de publication](devices-sdk-release-notes.md)
