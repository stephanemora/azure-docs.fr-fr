---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 005cf83508d25e8f44190e07336fbb4e444f8e6b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400371"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour effectuer une synthèse vocale de texte. Le service de synthèse vocale propose de nombreuses options pour les voix de synthèse, sous la [prise en charge des langues de synthèse vocale](../../../language-support.md#text-to-speech). Après avoir satisfait à quelques conditions préalables, il suffit de quatre étapes pour diriger la synthétisées vers les haut-parleurs par défaut :
> [!div class="checklist"]
> * Créez un objet `SpeechConfig` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet `SpeechSynthesizer` à l’aide de l’objet `SpeechConfig` ci-dessus.
> * Utilisation de l’objet `SpeechSynthesizer` pour le rendu audio du texte.
> * Vérifiez le résultat `SpeechSynthesisResult` retourné pour les erreurs.
