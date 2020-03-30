---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961427"
---
Dans ce guide de démarrage rapide, vous allez utiliser le [SDK Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour effectuer la synthèse vocale du texte dans un fichier audio. Le service de synthèse vocale propose de nombreuses options pour les voix de synthèse, sous la [prise en charge des langues de synthèse vocale](../../../language-support.md#text-to-speech). Après avoir satisfait à quelques conditions préalables, la synthèse vocale dans un fichier ne contient que cinq étapes :
> [!div class="checklist"]
> * Créez un objet `SpeechConfig` à partir de votre clé d’abonnement et de votre région.
> * Créez un objet Configuration audio qui spécifie le nom du fichier WAV.
> * Créez un objet `SpeechSynthesizer` à l’aide des objets de configuration ci-dessus.
> * À l’aide de l’objet `SpeechSynthesizer`, convertissez votre texte en synthèse vocale, en l’enregistrant dans le fichier audio spécifié.
> * Inspectez le résultat `SpeechSynthesizer` retourné pour vérifier la présence éventuelle d’erreurs.
