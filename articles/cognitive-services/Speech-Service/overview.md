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
ms.openlocfilehash: 0cd6d984ac9329112aa388e8d8ee808d4c3e6227
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445632"
---
# <a name="what-is-the-speech-service-preview"></a>Qu’est-ce que le service Speech (préversion) ?

Le service Speech s’appuie sur les technologies utilisées dans d’autres produits Microsoft, notamment Cortana et Microsoft Office.  Ce même service est disponible pour tous les clients en tant que Cognitive Service. 

> [!NOTE]
> Le service Speech est actuellement disponible en préversion publique. Revenez ici régulièrement pour connaître les mises à jour apportées à la documentation, obtenir des exemples de code supplémentaires, etc.

Avec un abonnement, notre service Speech unifié offre aux développeurs un moyen simple de doter leurs applications de puissantes fonctionnalités de reconnaissance vocale. Vos applications peuvent désormais proposer un système de commande vocale, de transcription, de dictée, de synthèse vocale et de traduction.

## <a name="speech-service-features"></a>Fonctionnalités du service Speech

|Fonction|Description|
|-|-|
|[Reconnaissance vocale](speech-to-text.md)| Transcrit les flux audio en texte que votre application peut accepter comme entrée. S’intègre aussi au [service Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) pour déduire l’intention de l’utilisateur à partir des énoncés.|
|[Synthèse vocale](text-to-speech.md)| Convertit le texte brut en un discours plus naturel qui est remis à votre application dans un fichier audio. Plusieurs voix, avec des genres et accents variés, sont disponibles pour de nombreuses langues prises en charge. |
|[Traduction vocale](speech-translation.md)| Peut être utilisée pour traduire un streaming audio en quasi-temps réel ou pour traiter un enregistrement vocal. |
|[SDK Speech Devices](speech-devices-sdk.md)| Avec l’introduction du service Speech unifié, Microsoft et ses partenaires offrent une plateforme matérielle/logicielle intégrée, optimisée pour le développement d’appareils avec des fonctionnalités vocales. |

## <a name="using-the-speech-service"></a>Utilisation du service Speech

Le service Speech est accessible de deux manières. [Le kit de développement logiciel (SDK)](speech-sdk.md) élimine les détails des protocoles réseau. L’[API REST](rest-apis.md) fonctionne avec n’importe quel langage de programmation, mais n’offre pas toutes les fonctions du kit de développement logiciel (SDK).

|<br>Méthode|Speech<br>to Text|Text to<br>Speech|Speech<br>Traduction|<br>Description|
|-|-|-|-|-|
|[Kits de développement logiciel (SDK)](speech-sdk.md)|Oui|Non |Oui|Bibliothèques pour des langages de programmation spécifiques qui simplifient le développement.|
|[REST](rest-apis.md)|Oui|Oui|Non |API simple basée sur HTTP qui permet d’ajouter facilement la reconnaissance vocale à votre application.|

## <a name="next-steps"></a>Étapes suivantes

Obtenir une clé d’abonnement à un essai gratuit pour le service Speech.

> [!div class="nextstepaction"]
> [Essayer le service Speech gratuitement](get-started.md)
