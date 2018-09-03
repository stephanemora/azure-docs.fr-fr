---
title: Qu’est-ce que le service Speech (préversion) ?
description: 'Le service Speech fait partie des services Cognitive Services de Microsoft et regroupe plusieurs services de reconnaissance vocale Azure qui étaient auparavant disponibles séparément : la reconnaissance vocale Bing (incluant la reconnaissance vocale et la synthèse vocale), Custom Speech et la traduction vocale.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: overview
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 922320bb0b880e933b27025257e6a533fe257680
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091470"
---
# <a name="what-is-the-speech-service"></a>Qu’est-ce que le service de reconnaissance vocale ?

Le service de reconnaissance vocale réunit les fonctionnalités de reconnaissance vocale Azure précédemment disponibles via l’[API Reconnaissance vocale Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home), [Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/), [Custom Speech](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home), et les services [Custom Speech](http://customvoice.ai/). À présent, un seul abonnement donne accès à toutes ces fonctionnalités.

Comme les autres services de reconnaissance vocale Azure, le service de reconnaissance vocale est alimenté par les technologies de reconnaissance vocale éprouvées et utilisées dans des produits tels que Cortana et Microsoft Office. Vous pouvez compter sur la qualité des résultats et la fiabilité du cloud Azure.

> [!NOTE]
> Le service Speech est actuellement disponible en préversion publique. Revenez ici régulièrement pour connaître les mises à jour apportées à la documentation, obtenir de nouveaux exemples de code, etc.

## <a name="main-speech-service-functions"></a>Fonctions principales du service de reconnaissance vocale

Les principales fonctions du service de reconnaissance vocale sont la reconnaissance vocale (également appelée transcription), la synthèse vocale et la traduction vocale.

|Fonction|Caractéristiques|
|-|-|
|[Reconnaissance vocale](speech-to-text.md)| <ul><li>Retranscrit une voix en un texte de façon continue et en temps réel.<li>Peut effectuer une transcription en lot à partir d’enregistrements audio. <li>Propose des modes de reconnaissance pour des cas d'usage interactifs, des conversations et des dictées.<li>Prend en charge les résultats intermédiaires, la détection de fin de parole, la mise en forme automatique du texte et le masquage des grossièretés. <li>Peut appeler le [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) pour déduire l’intention de l’utilisateur à partir des transcriptions.\*|
|[Synthèse vocale](text-to-speech.md)| <ul><li>Convertit le texte en paroles naturelles. <li>Offre différents sexes et/ou dialectes pour de nombreuses langues prises en charge. <li>Prend en charge l’entrée de texte brut ou le langage de balisage de synthèse vocale (SSML). |
|[Traduction vocale](speech-translation.md)| <ul><li>Traduit des flux de données audio en quasi-temps réel<li> Peut également traiter des processus enregistrés<li>Fournit les résultats sous forme de texte ou de synthèse vocale. |

\* *La reconnaissance des intentions nécessite un abonnement LUIS.*


## <a name="customizing-speech-features"></a>Personnalisation des fonctionnalités de reconnaissance vocale

Le service de reconnaissance vocale vous permet d’utiliser vos propres données pour former des modèles de reconnaissance vocale pour les fonctionnalités de reconnaissance vocale et de synthèse vocale. 

|Fonctionnalité|Modèle|Objectif|
|-|-|-|
|Reconnaissance vocale|[Modèle acoustique](how-to-customize-acoustic-models.md)|Permet de transcrire des orateurs et des environnements particuliers, tels que des voitures ou des usines|
||[Modèle de langage](how-to-customize-language-model.md)|Permet de transcrire le vocabulaire et la grammaire d’un domaine spécifique, comme le domaine médical ou informatique|
||[Modèle de prononciation](how-to-customize-pronunciation.md)|Permet de transcrire des abréviations et des acronymes, tels que « IOU » pour « i oh you » |
|Synthèse vocale|[Police de la voix](how-to-customize-voice-font.md)|Offre une voix à votre application par l’apprentissage du modèle sur des échantillons de voix humaine.|

Une fois créés, vos modèles personnalisés peuvent être utilisés partout où vous utiliserez les modèles standards dans la fonctionnalité de reconnaissance vocale ou de synthèse vocale de votre application.


## <a name="using-the-speech-service"></a>Utilisation du service Speech

Pour simplifier le développement d’applications pourvues d’une reconnaissance vocale, Microsoft fournit le [kit de développement logiciel Speech](speech-sdk.md) pour une utilisation avec le nouveau service de reconnaissance vocale. Le Kit de développement logiciel Speech fournit des API de reconnaissance vocale et de traduction vocale pour C#, C++ et Java. Si vous développez avec l’un de ces langages, le kit de développement logiciel Speech facilite le développement en gérant les détails du réseau pour vous.

Le service de reconnaissance vocale a également une [API REST](rest-apis.md) qui fonctionne avec n’importe quel langage de programmation pouvant effectuer des requêtes HTTP. Toutefois, l’interface REST n’offre pas de la fonctionnalité de diffusion en continu et en temps réel du kit de développement logiciel.

|<br>Méthode|Speech<br>to Text|Text to<br>Speech|Speech<br>Traduction|<br>Description|
|-|-|-|-|-|
|[Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk.md)|Oui|Non |Oui|API natives pour C#, C++ et Java afin de simplifier le développement.|
|[REST](rest-apis.md)|Oui|Oui|Non |API simple basée sur HTTP qui permet d’ajouter facilement la reconnaissance vocale à votre application.|

### <a name="websockets"></a>WebSockets

Le service de reconnaissance vocale possède également des protocoles WebSocket pour la diffusion en continu d’une reconnaissance vocale et d’une traduction vocale. Les kits de développement logiciel Speech utilisent ces protocoles pour communiquer avec le service de reconnaissance vocale. Vous devez utiliser le kit de développement logiciel Speech plutôt que d’essayer d’implémenter votre propre communication WebSocket avec le service de reconnaissance vocale.

Cependant, si vous avez déjà le code qui utilise la reconnaissance vocale Bing ou Translator Speech via le protocole WebSocket, il est simple de le mettre à jour pour utiliser le service de reconnaissance vocale. Les protocoles WebSocket sont compatibles ; seuls les points de terminaison sont différents.

### <a name="speech-devices-sdk"></a>Kit de développement logiciel (SDK) Speech Devices

Le [Kit de développement logiciel d’appareils Speech](speech-devices-sdk.md) est une plateforme matérielle et logicielle intégrée pour les développeurs d’appareils de reconnaissance vocale. Notre partenaire de matériel fournit des modèles de référence et des unités de développement. Microsoft fournit un kit de développement optimisé pour les appareils et exploite pleinement les possibilités du matériel.


## <a name="speech-scenarios"></a>Scénarios de reconnaissance vocale

Les cas d’usage pour le service de reconnaissance vocale sont les suivants :

> [!div class="checklist"]
> * Créer des applications déclenchées par la voix
> * Transcrire les enregistrements des centres d’appel
> * Implémenter des bots vocaux

### <a name="voice-user-interface"></a>Interface utilisateur vocale

L’entrée vocale est un excellent moyen de rendre votre application flexible, accessible en mode mains libres et facile à utiliser. Dans une application activée par la voix, les utilisateurs demandent simplement les informations qu’ils souhaitent au lieu d’y accéder.

Si votre application est destinée au grand public, vous pouvez utiliser les modèles de reconnaissance vocale par défaut. Ils sont capables de reconnaître un large éventail d’orateurs dans des environnements courants.

Si votre application est utilisée dans un domaine spécifique (par exemple, en médecine ou en informatique), vous pouvez créer un [modèle de langage](how-to-customize-language-model.md) afin d’enseigner au service Speech la terminologie spéciale utilisée par votre application.

Si votre application est exécutée dans un environnement bruyant, par exemple en usine, vous pouvez créer un [modèle acoustique](how-to-customize-acoustic-models.md) personnalisé afin d’aider le service Speech à distinguer les voix du bruit ambiant.

Pour démarrer, il suffit de télécharger le [Kit de développement logiciel (SDK) Speech](speech-sdk.md) et de consulter l’article [Quickstart](quickstart-csharp-dotnet-windows.md) correspondant.

### <a name="call-center-transcription"></a>Transcription de centre d’appel

Souvent, les enregistrements des centres d’appel sont consultés uniquement en cas de problème lors d’un appel. Le service Speech permet de transcrire facilement chaque enregistrement en texte. Une fois l’enregistrement transcrit en texte, vous pouvez facilement l’indexer pour effectuer une [recherche en texte intégral](https://docs.microsoft.com/azure/search/search-what-is-azure-search) ou une [analyse de texte](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) afin de détecter le sentiment, la langue et les expressions clés.

Si vos enregistrements de centres d’appel sont centrés sur des termes spécialisés (par exemple, des noms de produits ou un jargon informatique), vous pouvez créer un [modèle de langage](how-to-customize-language-model.md) afin d’enseigner ce vocabulaire au service Speech. Un [modèle acoustique](how-to-customize-acoustic-models.md) personnalisé permet au service Speech de comprendre les communications téléphoniques pas très claires.

Pour plus d’informations sur ce scénario, reportez-vous à la fonctionnalité de [transcription par lot](batch-transcription.md) du service Speech.

### <a name="voice-bots"></a>Bots vocaux

Grâce aux [bots](https://dev.botframework.com/), de plus en plus d’utilisateurs accèdent aux informations recherchées, et de plus en plus de clients entrent en contact avec leurs entreprises préférées. L’ajout d’une interface utilisateur de conversation à votre site Web ou à votre application facilite la recherche et l’exécution de cette fonctionnalité. Grâce au service Speech, la conversation gagne en fluidité et vous permet de répondre aux requêtes parlées en nature.

Pour ajouter une personnalité unique à votre bot vocal (et renforcez votre marque), vous pouvez lui attribuer une voix personnalisée. La création d’une voix personnalisée est un processus en deux étapes. Tout d’abord, vous [effectuez des enregistrements](record-custom-voice-samples.md) de la voix à utiliser. Puis vous [envoyez ces enregistrements](how-to-customize-voice-font.md) (avec une transcription du texte) au [portail de personnalisation de la voix](https://cris.ai/Home/CustomVoice) du service Speech, qui s’occupe du reste. Une fois votre voix personnalisée créée, vous pouvez très simplement l’utiliser dans votre application.

## <a name="next-steps"></a>Étapes suivantes

Obtenir une clé d’abonnement pour le service Speech.

> [!div class="nextstepaction"]
> [Essayer le service Speech gratuitement](get-started.md)
