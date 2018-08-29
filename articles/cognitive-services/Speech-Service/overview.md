---
title: Qu’est-ce que le service Speech (préversion) ?
description: 'Le service Speech fait partie des services Cognitive Services de Microsoft et regroupe plusieurs services de reconnaissance vocale Azure qui étaient auparavant disponibles séparément : la reconnaissance vocale Bing (incluant la reconnaissance vocale et la synthèse vocale), Custom Speech et la traduction vocale.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 60ff2f71766a14af17ebb1cb9d20825976471296
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "41929797"
---
# <a name="what-is-the-speech-service-preview"></a>Qu’est-ce que le service Speech (préversion) ?

Avec un abonnement, le service Speech unifié offre aux développeurs un moyen simple d’ajouter de puissantes fonctionnalités de reconnaissance vocale à leurs applications. Vos applications peuvent désormais proposer un système de commande vocale, de transcription, de dictée, de synthèse vocale et de traduction vocale.

Le service Speech s’appuie sur les technologies utilisées dans d’autres produits Microsoft, notamment Cortana et Microsoft Office.

> [!NOTE]
> Le service Speech est actuellement disponible en préversion publique. Revenez ici régulièrement pour connaître les mises à jour apportées à la documentation, obtenir des exemples de code supplémentaires, etc.

## <a name="speech-service-features"></a>Fonctionnalités du service Speech

|Fonction|Description|
|-|-|
|[Reconnaissance vocale](speech-to-text.md)| Transcrit les flux audio en texte que votre application peut accepter comme entrée. S’intègre aussi au [service Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) pour déduire l’intention de l’utilisateur à partir des énoncés.|
|[Synthèse vocale](text-to-speech.md)| Convertit le texte brut en un discours plus naturel qui est remis à votre application dans un fichier audio. Plusieurs voix, avec des genres et accents variés, sont disponibles pour de nombreuses langues prises en charge. |
|[Traduction vocale](speech-translation.md)| Peut être utilisée pour traduire un streaming audio en quasi-temps réel ou pour traiter un enregistrement vocal. |
|Reconnaissance vocale personnalisée|Vous pouvez personnaliser la reconnaissance vocale en créant vos propres modèles d’[acoustique](how-to-customize-acoustic-models.md) et de [langage](how-to-customize-language-model.md) et en spécifiant des règles de [prononciation](how-to-customize-pronunciation.md) personnalisées. |
|[Synthèse vocale personnalisée](how-to-customize-voice-font.md)|Vous pouvez créer vos propres voix pour la synthèse vocale.|
|[SDK Speech Devices](speech-devices-sdk.md)| Avec l’introduction du service Speech unifié, Microsoft et ses partenaires offrent une plateforme matérielle/logicielle intégrée, optimisée pour le développement d’appareils avec des fonctionnalités vocales. |

## <a name="access-to-the-speech-service"></a>Accès au service Speech

Le service Speech est accessible de deux manières. [Le Kit de développement logiciel (SDK)](speech-sdk.md) élimine les détails des protocoles réseau pour faciliter le développement sur les plateformes prises en charge. L’[API REST](rest-apis.md) fonctionne avec n’importe quel langage de programmation, mais n’offre pas toutes les fonctions du kit de développement logiciel (SDK).

|<br>Méthode|Speech<br>to Text|Text to<br>Speech|Speech<br>Traduction|<br>Description|
|-|-|-|-|-|
|[Kits de développement logiciel (SDK)](speech-sdk.md)|Oui|Non |Oui|Les bibliothèques pour des langages de programmation spécifiques utilisent un protocole basé sur Websocket qui simplifie le développement.|
|[REST](rest-apis.md)|Oui|Oui|Non |API simple basée sur HTTP qui permet d’ajouter facilement la reconnaissance vocale à votre application.|

## <a name="speech-scenarios"></a>Scénarios de reconnaissance vocale

Quelques utilisations courantes de la technologie vocale sont brièvement présentées ci-dessous. Le [SDK Speech](speech-sdk.md) est commun à la plupart de ces scénarios.

> [!div class="checklist"]
> * Créer des applications déclenchées par la voix
> * Transcrire les enregistrements des centres d’appel
> * Implémenter des bots vocaux

### <a name="voice-triggered-apps"></a>Applications déclenchées par la voix

L’entrée vocale est un excellent moyen de rendre votre application flexible, accessible en mode mains libres et facile à utiliser. Dans une application activée par la voix, les utilisateurs demandent simplement les informations qu’ils souhaitent au lieu d’y accéder en cliquant dessus.

Si votre application est destinée au grand public, vous pouvez utiliser le modèle de reconnaissance vocale de base fourni par le service Speech. Il est capable de reconnaître un large éventail d’orateurs dans un environnement standard.

Si votre application sera utilisée dans un domaine spécifique (par exemple, en médecine ou en informatique), vous pouvez créer un [modèle de langage](how-to-customize-language-model.md) afin d’enseigner au service Speech la terminologie spéciale utilisée par votre application.

Si votre application sera exécutée dans un environnement bruyant, par exemple en usine, vous pouvez créer un [modèle acoustique](how-to-customize-acoustic-models.md) personnalisé afin d’aider le service Speech à distinguer les voix du bruit ambiant.

Pour démarrer, il suffit de télécharger le [Kit de développement logiciel (SDK) Speech](speech-sdk.md) et de consulter l’article [Quickstart](quickstart-csharp-dotnet-windows.md) correspondant.

### <a name="transcribe-call-center-recordings"></a>Transcrire les enregistrements des centres d’appel

Souvent, les enregistrements des centres d’appel sont consultés uniquement en cas de problème lors d’un appel. Le service Speech permet de transcrire facilement chaque enregistrement en texte. Une fois l’enregistrement transcrit en texte, vous pouvez facilement l’indexer pour effectuer une [recherche en texte intégral](https://docs.microsoft.com/azure/search/search-what-is-azure-search) ou une [analyse de texte](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) afin de détecter le sentiment, la langue et les expressions clés.

Si vos enregistrements de centres d’appel contiennent souvent des termes spécialisés (par exemple, des noms de produits ou un jargon informatique), vous pouvez créer un [modèle de langage](how-to-customize-language-model.md) afin d’enseigner ce vocabulaire au service Speech. Un [modèle acoustique](how-to-customize-acoustic-models.md) personnalisé permet au service Speech de comprendre les communications téléphoniques pas très claires.

Pour plus d’informations sur ce scénario, reportez-vous à la fonctionnalité de [transcription par lot](batch-transcription.md) du service Speech.

### <a name="voice-bots"></a>Bots vocaux

Grâce aux [bots](https://dev.botframework.com/), de plus en plus d’utilisateurs accèdent aux informations recherchées, et de plus en plus de clients entrent en contact avec leurs entreprises préférées. L’ajout d’une interface utilisateur de conversation à votre site Web ou à votre application facilite la recherche et l’exécution de cette fonctionnalité. Grâce au service Speech, la conversation gagne en fluidité et vous permet de répondre réellement aux requêtes parlées avec la synthèse vocale.

Pour ajouter une personnalité unique à votre bot vocal (et renforcez votre marque), vous pouvez lui attribuer une voix personnalisée. La création d’une voix personnalisée est un processus en deux étapes. Tout d’abord, vous [effectuez des enregistrements](record-custom-voice-samples.md) de la voix à utiliser. Puis vous [envoyez ces enregistrements](how-to-customize-voice-font.md) (avec une transcription du texte) au [portail de personnalisation de la voix](https://cris.ai/Home/CustomVoice) du service Speech, qui s’occupe du reste. Une fois votre voix personnalisée créée, vous pouvez très simplement l’utiliser dans votre application.

## <a name="next-steps"></a>Étapes suivantes

Obtenir une clé d’abonnement pour le service Speech.

> [!div class="nextstepaction"]
> [Essayer le service Speech gratuitement](get-started.md)
