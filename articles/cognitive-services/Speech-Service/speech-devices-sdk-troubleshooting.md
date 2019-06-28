---
title: Dépanner le kit SDK Speech Devices - Services Speech
titleSuffix: Azure Cognitive Services
description: Cet article fournit des informations pour vous aider à résoudre des problèmes que vous pourriez rencontrer lors de l’utilisation du kit SDK Speech Devices.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: wellsi
ms.openlocfilehash: 87fb35f06dcb1d1e3fb8c3ae3be64c7448162f14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65026155"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Résoudre les problèmes liés au SDK des dispositifs vocaux

Cet article fournit des informations pour vous aider à résoudre des problèmes que vous pourriez rencontrer lors de l’utilisation du kit SDK Speech Devices.

## <a name="certificate-failures"></a>Erreurs de certificat

Si vous rencontrez des erreurs de certificat lorsque vous utilisez les services Speech, veillez à ce que la date et l’heure de votre appareil soient correctes :

1. Accédez à **Settings**. Sous **System** (Système), sélectionnez **Date & time** (Date et heure).

    ![Sélection du paramètre de date et d’heure](media/speech-devices-sdk/qsg-12.png)

1. Laissez l’option **Automatic date & time** (Date et heure automatiques) sélectionnée. Sous **Select time zone** (Sélectionner un fuseau horaire), sélectionnez votre fuseau horaire actuel.

    ![Sélection des options de date et de fuseau horaire](media/speech-devices-sdk/qsg-13.png)

    Lorsque vous voyez que l’heure du kit de développement correspond à l’heure de votre PC, le kit de développement est connecté à Internet.

## <a name="next-steps"></a>Étapes suivantes

* [Examiner les notes de publication](devices-sdk-release-notes.md)
