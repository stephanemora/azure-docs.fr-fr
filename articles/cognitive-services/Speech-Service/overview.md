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
ms.openlocfilehash: fc6ef4ccbe6e392b991bd375afcc63a54f58db02
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285457"
---
# <a name="what-is-the-speech-service-preview"></a>Qu’est-ce que le service Speech (préversion) ?

Le service Speech fait partie des services Cognitive Services de Microsoft et regroupe plusieurs services de reconnaissance vocale Azure qui étaient auparavant disponibles séparément : la reconnaissance vocale Bing (incluant la reconnaissance vocale et la synthèse vocale), Custom Speech et la traduction vocale. Comme ses précurseurs, le service Speech s’appuie sur les technologies utilisées dans d’autres produits Microsoft, y compris Cortana et Microsoft Office.

> [!NOTE]
> Le service Speech est actuellement disponible en préversion publique. Revenez ici régulièrement pour connaître les mises à jour apportées à la documentation, obtenir des exemples de code supplémentaires, etc.

Avec un abonnement, le service Speech unifié offre aux développeurs un moyen simple de doter leurs applications de fonctionnalités puissantes de reconnaissance vocale. Vos applications peuvent désormais proposer un système de commande vocale, de transcription, de dictée, de synthèse vocale et de traduction.

|Fonction|Description|
|-|-|
|Reconnaissance vocale|Convertit la voix humaine continue en texte qui peut être utilisé en tant qu’entrée dans votre application. Peut s’intégrer au [service Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) pour déduire l’intention de l’utilisateur à partir des énoncés.|
|Synthèse vocale|Convertit le texte en fichiers audio utilisant une voix de synthèse naturelle.|
|Traduction&nbsp;vocale|Fournit la traduction de la voix dans d’autres langues, sous forme de sortie vocale ou texte.|

## <a name="using-the-speech-service"></a>Utilisation du service Speech

Le service Speech est accessible de deux manières. [Le kit de développement logiciel (SDK)](speech-sdk.md) élimine les détails des protocoles réseau. L’[API REST](rest-apis.md) fonctionne avec n’importe quel langage de programmation, mais n’offre pas toutes les fonctions du kit de développement logiciel (SDK).

|<br>Méthode|Speech<br>to Text|Text to<br>Speech|Speech<br>Traduction|<br>Description|
|-|-|-|-|-|
|[Kits de développement logiciel (SDK)](speech-sdk.md)|Oui|Non |Oui|Bibliothèques pour des langages de programmation spécifiques qui simplifient le développement.|
|[REST](rest-apis.md)|Oui|Oui|Non |API simple basée sur HTTP qui permet d’ajouter facilement la reconnaissance vocale à votre application.|

## <a name="speech-to-text"></a>Reconnaissance vocale

L’API [Reconnaissance vocale](speech-to-text.md) (STT) retranscrit les flux audio en texte que votre application peut accepter comme entrée. Votre application peut ensuite, par exemple, entrer le texte dans un document ou agir dessus en tant que commande.

La reconnaissance vocale a été optimisée individuellement pour les scénarios d’interaction, de conversation et de dictée. Les éléments suivants sont des cas d’utilisation courants de l’API Reconnaissance vocale. 

* Reconnaître un énoncé bref, tel qu’une commande, sans résultats intermédiaires
* Transcrire un énoncé long enregistré préalablement, comme un message vocal
* Transcrire des paroles diffusées en temps réel, avec des résultats partiels, pour la dictée
* Déterminer ce que les utilisateurs souhaitent faire en fonction d’une requête parlée en langage naturel

L’API Reconnaissance vocale prend en charge la transcription interactive de la parole avec une reconnaissance en continu et en temps réel et des résultats intermédiaires. Il prend également en charge la détection de fin de parole, l’option de mise en majuscules et de ponctuation automatique, le masquage des grossièretés et la normalisation du texte.

Vous pouvez personnaliser des modèles linguistiques et acoustiques de reconnaissance vocale pour prendre en compte le vocabulaire spécialisé, les environnements bruyants et les différentes façons de parler.

## <a name="text-to-speech"></a>Synthèse vocale

L’API [Synthèse vocale](text-to-speech.md) (TTS) convertit le texte brut en un discours plus naturel qui est remis à votre application dans un fichier audio. Plusieurs voix, avec des genres et accents variés, sont disponibles pour de nombreuses langues prises en charge.

L’API prend en charge les [balises de langage de balisage de synthèse vocale (SSML)](speech-synthesis-markup.md). Vous pouvez ainsi spécifier la prononciation phonétique exacte des mots problématiques. Le SSML peut également indiquer les caractéristiques vocales (comme l’accentuation, la fréquence, le volume, le genre et le ton) directement dans le texte.

Voici des cas d’utilisation courants pour l’API Synthèse vocale.

* La sortie vocale en tant que sortie écran alternative pour les utilisateurs malvoyants
* L’invite vocale pour les applications de voiture, telles que la navigation
* Les interfaces utilisateur de conversation conjointement avec l’API Reconnaissance vocale

Si vous avez besoin d’utiliser un dialecte non pris en charge ou souhaitez une voix unique pour votre application, l’API Synthèse vocale prend en charge des [modèles vocaux personnalisés](how-to-customize-voice-font.md).

## <a name="speech-translation"></a>Traduction vocale

L’API [Traduction vocale](speech-translation.md) peut être utilisée pour traduire une diffusion audio en quasi-temps réel ou pour traiter un enregistrement vocal. Avec la traduction vocale, le service retourne des résultats intermédiaires qui peuvent être affichés pour l’utilisateur pour indiquer la progression de la traduction. Les résultats peuvent être retournés sous forme de texte ou de voix.

Les cas d’usage de la traduction vocale incluent les éléments suivants.

* Implémenter une application mobile ou un appareil de traduction de « conversation » pour les voyageurs 
* Fournir des traductions automatiques pour le sous-titrage d’enregistrements audio et vidéo

## <a name="speech-devices-sdk"></a>Kit de développement logiciel (SDK) Speech Devices

Avec l’introduction du service Speech unifié, Microsoft et ses partenaires offrent une plateforme matérielle/logicielle intégrée, optimisée pour le développement de dispositifs vocaux : le [kit de développement logiciel (SDK) Speech Devices](speech-devices-sdk.md). Ce SDK est approprié pour le développement de dispositifs vocaux connectés pour tous les types d’applications.

Le kit de développement logiciel (SDK) Speech Devices vous permet de créer vos propres appareils ambiants avec un mot déclencheur personnalisé, de façon à ce que l’élément qui déclenche la capture audio soit spécifique à votre marque. Il fournit également un traitement audio supérieur à partir de sources multicanaux pour une reconnaissance vocale plus précise, incluant notamment la suppression de bruit, la voix en champ lointain et la formation de faisceaux.

Le Kit de développement logiciel (SDK) est basé sur des sockets web via le port 443.

## <a name="next-steps"></a>Étapes suivantes

Obtenir une clé d’abonnement à un essai gratuit pour le service Speech.

> [!div class="nextstepaction"]
> [Essayer le service Speech gratuitement](get-started.md)
