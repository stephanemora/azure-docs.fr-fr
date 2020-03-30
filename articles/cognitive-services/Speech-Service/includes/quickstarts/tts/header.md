---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: e9f02f95693552180a0eed1550cc59d8f975416b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961499"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour effectuer une synthèse vocale de texte. Le service de synthèse vocale propose de nombreuses options pour les voix de synthèse, sous la [prise en charge des langues de synthèse vocale](../../../language-support.md#text-to-speech). Après avoir satisfait à quelques conditions préalables, il suffit de quatre étapes pour diriger la synthétisées vers les haut-parleurs par défaut :
> [!div class="checklist"]
> * Créez un objet `SpeechConfig` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet `SpeechSynthesizer` à l’aide de l’objet `SpeechConfig` ci-dessus.
> * Utilisation de l’objet `SpeechSynthesizer` pour le rendu audio du texte.
> * Vérifiez le résultat `SpeechSynthesisResult` retourné pour les erreurs.
