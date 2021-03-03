---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 6b16dea3c4f9241133b91b092c90c9056da57de0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100514941"
---
### <a name="standard-and-neural-voices"></a>Voix standard et neuronales

Servez-vous de ce tableau pour connaître la disponibilité des voix standard et neuronales par région/point de terminaison :

| Région | Point de terminaison | Voix neuronales | Voix standard |
|--------|----------|-----------------|---------------|
| Australie Est | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| Brésil Sud | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Non | Oui |
| Centre du Canada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| USA Centre | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Non | Oui |
| Asie Est | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Non | Oui |
| USA Est | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| USA Est 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Non | Oui |
| France Centre | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Non | Oui |
| Inde Centre | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| Japon Est | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Non | Oui |
| OuJapon Est | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Non | Oui |
| Centre de la Corée | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Non | Oui |
| Centre-Nord des États-Unis | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Non | Oui |
| Europe Nord | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Non | Oui |
| États-Unis - partie centrale méridionale | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| Asie Sud-Est | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| Sud du Royaume-Uni | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| Europe Ouest | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| USA Ouest | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Non | Oui |
| USA Ouest 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |

> [!TIP]
> [Les voix en préversion](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) ne sont disponibles que dans les 3 régions suivantes : USA Est, Europe Ouest et Asie Sud-Est.

### <a name="custom-voices"></a>Voix personnalisées

Si vous avez créé une police de la voix personnalisée, utilisez le point de terminaison que vous avez créé. Vous pouvez aussi utiliser les points de terminaison listés ci-dessous, en remplaçant la valeur de `{deploymentId}` par l’ID de déploiement de votre modèle vocal.

| Région | Point de terminaison |
|--------|----------|
| Australie Est | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Brésil Sud | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Centre du Canada | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA Centre | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Asie Est | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA Est | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA Est 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| France Centre | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Inde Centre | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japon Est | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| OuJapon Est | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Centre de la Corée | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Centre-Nord des États-Unis | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europe Nord | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| États-Unis - partie centrale méridionale | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Asie Sud-Est | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Sud du Royaume-Uni | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europe Ouest | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA Ouest | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA Ouest 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |

### <a name="custom-neural-voice"></a>Voix neuronale personnalisée

Le tableau suivant détaille la prise en charge régionale des fonctionnalités de la voix neuronale personnalisée.

| Fonctionnalité | Régions prises en charge |
|---|---|
| Hébergement du modèle vocal | USA Est, USA Ouest 2, USA Centre Sud, Asie Sud-Est, Royaume-Uni Sud, Europe Ouest, Australie Est |
| Caractères en temps réel | USA Est, USA Ouest 2, USA Centre Sud, Asie Sud-Est, Royaume-Uni Sud, Europe Ouest, Australie Est |
| Caractères d’audio long | USA Est, Europe Ouest, Royaume-Uni Sud, Asie Sud-Est, Inde Centre |
| Formation neuronale personnalisée | USA Est, Royaume-Uni Sud |

### <a name="long-audio-api"></a>API Audio long

L’API Audio long est disponible dans plusieurs régions avec des points de terminaison uniques.

| Région | Point de terminaison |
|--------|----------|
| USA Est | `https://eastus.customvoice.api.speech.microsoft.com` |
| Inde Centre | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Asie Sud-Est | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Sud du Royaume-Uni | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europe Ouest | `https://westeurope.customvoice.api.speech.microsoft.com` |
