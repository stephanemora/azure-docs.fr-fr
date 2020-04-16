---
title: API Audio long (préversion) - Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment l’API Audio long est conçue pour la synthèse asynchrone de texte de long en parole.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: b7cca314ec59e46cf17751b1aec28b5c3ea029ed
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401070"
---
# <a name="long-audio-api-preview"></a>API Audio long (préversion)

L’API Audio long est conçue pour la synthèse asynchrone de texte de long en parole (par exemple, livres audio). Cette API ne retourne pas d’audio synthétisé en temps réel. Au lieu de cela, vous êtes supposé interroger la ou les réponses et utiliser la ou les sorties à mesure que le service les rend disponibles. Contrairement à l’API de synthèse vocale utilisée par le Kit de développement logiciel (SDK) Speech, l’API Audio long peut créer de l’audio synthétisé de plus de 10 minutes, ce qui la rend idéale pour les éditeurs et les plateformes de contenu audio.

Autres avantages de l’API Audio long  :

* La synthèse vocale que le service retourne utilise des voix neuronales, ce qui garantit des sorties audio haute fidélité.
* Les réponses en temps réel n’étant pas prises en charge, il n’est pas nécessaire de déployer un point de terminaison vocal.

> [!NOTE]
> L’API Audio long prend désormais en charge uniquement la [voix neuronale personnalisée](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

## <a name="workflow"></a>Workflow

En règle générale, lorsque vous utilisez l’API Audio long, vous envoyez un ou plusieurs fichiers texte à synthétiser, interrogez l’état, puis, si celui-ci indique une réussite, vous pouvez télécharger la sortie audio.

Le diagramme suivant donne une vue d’ensemble du workflow.

![Diagramme de workflow de l’API Audio long](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Préparer du contenu pour la synthèse

Lors de la préparation de votre fichier texte, vérifiez les points suivants :

* Il s’agit d’un texte brut (.txt) ou d’un texte SSML (.txt)
* Le texte est encodé au format [UTF-8 avec marque d’ordre d’octet (BOM, Byte Order Mark)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Il s’agit d’un fichier unique, non d’un fichier zip
* Contient plus de 400 caractères pour le texte brut ou 400 [caractères facturables](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) pour du texte SSML, et moins de 10 000 paragraphes
  * Pour du texte brut, chaque paragraphe est séparé en appuyant sur la touche **Entrée/Retour** ([exemple d’entrée de texte brut](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt))
  * Pour du texte SSML, chaque élément SSML est considéré comme un paragraphe. Les éléments SSML sont séparés par différents paragraphes - Consultez [Exemple d’entrée de texte SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> Pour le chinois (Continent), le chinois (Hong Kong, R.A.S.), le chinois (Taïwan), le japonais et le coréen, un mot sera compté comme deux caractères. 

## <a name="submit-synthesis-requests"></a>Envoyer des demandes de synthèse vocale

Après avoir préparé le contenu d’entrée, suivez le [Guide de démarrage rapide de la synthèse audio de longue durée](https://aka.ms/long-audio-python) pour envoyer la demande. Si vous avez plusieurs fichiers d’entrée, vous devrez envoyer plusieurs demandes. Il existe certaines limites à connaître : 
* Le client est autorisé à envoyer jusqu’à 5 demandes par seconde au serveur pour chaque compte d’abonnement Azure. Si la limite est dépassée, le client reçoit un code d’erreur 429 (trop de demandes). Réduisez la quantité de demandes par seconde
* Le serveur est autorisé à exécuter et à mettre en file d’attente jusqu’à 120 demandes pour chaque compte d’abonnement Azure. Si la limite est dépassée, le serveur retourne un code d’erreur 429 (trop de demandes). Attendez et évitez d’envoyer une nouvelle demande avant que quelques demandes ne soient traitées
* Le serveur conservera jusqu’à 20 000 demandes pour chaque compte d’abonnement Azure. Si la limite est dépassée, supprimez quelques demandes avant d’en envoyer de nouvelles

## <a name="audio-output-formats"></a>Formats de sortie aduio

Nous prenons en charge différents formats de sortie audio. Vous pouvez générer des sorties audio par paragraphe ou concaténer les données audio en une seule sortie en définissant le paramètre « concatenateResult ». Les formats de sortie audio suivants sont pris en charge par l’API Audio long :

> [!NOTE]
> Le format audio par défaut est riff-16 khz-16 bits-mono-pcm.

* riff-8 khz-16 bits-mono-pcm
* riff-16 khz-16 bits-mono-pcm
* riff-24 khz-16 bits-mono-pcm
* riff-48 khz-16 bits-mono-pcm
* audio-16 khz-32 kbitrate-mono-mp3
* audio-16 khz-64 kbitrate-mono-mp3
* audio-16 khz-128 kbitrate-mono-mp3
* audio-24 khz-48 kbitrate-mono-mp3
* audio-24 khz-96 kbitrate-mono-mp3
* audio-24 khz-160 kbitrate-mono-mp3

## <a name="quickstarts"></a>Démarrages rapides

Nous proposons des démarrages rapides conçus pour vous aider à exécuter correctement l’API Audio long. Ce tableau comprend une liste de démarrages rapides de l’API Audio long organisés par langue.

* [Démarrage rapide : Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Exemple de code
Un exemple de code pour l’API Audio long est disponible sur GitHub.

* [Exemple de code : Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Exemple de code : C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Exemple de code : Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
