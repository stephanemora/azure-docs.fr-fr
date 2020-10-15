---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: a1de313245e826511a7e9c864165ac44bcb48530
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80234166"
---
### <a name="standard-and-neural-voices"></a>Voix standard et neuronales

Servez-vous de ce tableau pour connaître la disponibilité des voix standard et neuronales par région/point de terminaison :

| Région | Point de terminaison | Voix standard | Voix neuronales |
|--------|----------|-----------------|---------------|
| Australie Est | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| Brésil Sud | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| Centre du Canada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| USA Centre | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| Asie Est | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| USA Est | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| USA Est 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| France Centre | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| Inde Centre | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| Japon Est | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| OuJapon Est | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| Centre de la Corée | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| Centre-Nord des États-Unis | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| Europe Nord | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| États-Unis - partie centrale méridionale | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| Asie Sud-Est | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| Sud du Royaume-Uni | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| Europe Ouest | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| USA Ouest | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| USA Ouest 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |

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
