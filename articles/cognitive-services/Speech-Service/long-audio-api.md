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
ms.openlocfilehash: 550579b40470d7a1ad02031b8140e7d0a7164f46
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310595"
---
# <a name="long-audio-api-preview"></a>API Audio long (préversion)

L’API Audio long est conçue pour la synthèse asynchrone de texte long en parole (par exemple, livres audio, articles de presse et documents). Cette API ne retourne pas d’audio synthétisé en temps réel. Au lieu de cela, vous êtes supposé interroger la ou les réponses et utiliser la ou les sorties à mesure que le service les rend disponibles. Contrairement à l’API de synthèse vocale utilisée par le Kit de développement logiciel (SDK) Speech, l’API Audio long peut créer de l’audio synthétisé de plus de 10 minutes, ce qui la rend idéale pour les éditeurs et les plateformes de contenu audio.

Autres avantages de l’API Audio long  :

* La synthèse vocale renvoyée par le service utilise les meilleures voix neuronales.
* Il n’est pas nécessaire de déployer de point de terminaison vocal, car celui-ci synthétise les voix dans un mode batch qui n’est pas en temps réel.

> [!NOTE]
> L’API Audio long prend désormais en charge les [voix neuronales publiques](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) et les [voix neuronales personnalisées](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

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

Après avoir préparé le contenu d’entrée, suivez le [Guide de démarrage rapide de la synthèse audio de longue durée](https://aka.ms/long-audio-python) pour envoyer la demande. Si vous avez plusieurs fichiers d’entrée, vous devrez envoyer plusieurs demandes. 

Les **codes d’état HTTP** indiquent les erreurs courantes.

| API | Code d'état HTTP | Description | Proposition |
|-----|------------------|-------------|----------|
| Créer | 400 | La synthèse vocale n’est pas activée dans cette région. | Modifiez la clé d’abonnement au service Speech de façon à utiliser une région prise en charge. |
|        | 400 | Seuls sont valides les abonnements de niveau **Standard** au service Speech pour cette région. | Modifiez la clé d’abonnement au service Speech de façon à utiliser le niveau de tarification « Standard ». |
|        | 400 | Dépassement de la limite de 20 000 demandes pour le compte Azure. Supprimez des demandes pour pouvoir en envoyer de nouvelles. | Le serveur conserve un maximum de 20 000 demandes pour chaque compte Azure. Supprimez des demandes pour pouvoir en envoyer de nouvelles. |
|        | 400 | Ce modèle ne peut pas être utilisé dans la synthèse vocale : {modelID}. | Vérifiez que l’état de {modelID} est correct. |
|        | 400 | La région de la demande ne correspond pas à celle du modèle : {modelID}. | Vérifiez que la région de {modelID} correspond à celle de la demande. |
|        | 400 | La synthèse vocale ne prend en charge que le fichier texte au format d’encodage UTF-8 avec le marqueur d’ordre d’octet. | Vérifiez que les fichiers d’entrée sont au format d’encodage UTF-8 avec le marqueur d’ordre d’octet. |
|        | 400 | Seules les entrées SSML valides sont autorisées dans la demande de synthèse vocale. | Vérifiez que les expressions SSML d’entrée sont correctes. |
|        | 400 | Le nom de voix {voiceName} est introuvable dans le fichier d’entrée. | Le nom de voix SSML d’entrée n’est pas aligné avec l’ID de modèle. |
|        | 400 | Le nombre de paragraphes dans le fichier d’entrée ne doit pas excéder 10 000. | Vérifiez que le nombre de paragraphes dans le fichier est inférieur à 10 000. |
|        | 400 | Le fichier d’entrée doit comprendre plus de 400 caractères. | Vérifiez que votre fichier d’entrée comporte plus de 400 caractères. |
|        | 404 | Le modèle déclaré dans la définition de synthèse vocale est introuvable : {modelID}. | Vérifiez que le {modelID} est correct. |
|        | 429 | Dépassement de la limite de synthèse vocale active. Veuillez attendre que des demandes soient traitées. | Le serveur est autorisé à exécuter et à mettre en file d’attente un maximum de 120 demandes pour chaque compte Azure. Attendez et évitez d’envoyer de nouvelles demandes avant que quelques demandes ne soient traitées. |
| Tous       | 429 | Il y a trop de demandes. | Le client est autorisé à envoyer un maximum de 5 demandes par seconde au serveur pour chaque compte Azure. Réduisez la quantité de demandes par seconde. |
| DELETE    | 400 | La tâche de synthèse vocale est toujours en cours d’utilisation. | Vous ne pouvez supprimer que les demandes ayant l’état **Terminé** ou **Échec**. |
| GetById   | 404 | L’entité spécifiée est introuvable. | Vérifiez que l’ID de synthèse est correct. |

## <a name="regions-and-endpoints"></a>Régions et points de terminaison

L’API Audio long est disponible dans plusieurs régions avec des points de terminaison uniques.

| Région | Point de terminaison |
|--------|----------|
| Australie Est | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| Centre du Canada | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| USA Est | `https://eastus.customvoice.api.speech.microsoft.com` |
| Inde Centre | `https://centralindia.customvoice.api.speech.microsoft.com` |
| États-Unis - partie centrale méridionale | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| Asie Sud-Est | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Sud du Royaume-Uni | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europe Ouest | `https://westeurope.customvoice.api.speech.microsoft.com` |
| USA Ouest 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Formats de sortie aduio

Nous prenons en charge différents formats de sortie audio. Vous pouvez générer des sorties audio par paragraphe ou concaténer les sorties audio en une seule sortie en définissant le paramètre « concatenateResult ». Les formats de sortie audio suivants sont pris en charge par l’API Audio long :

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
