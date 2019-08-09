---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: d5a4b3a07854c2664de7ec60f3677b666798a9bd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177353"
---
### <a name="standard-and-neural-voices"></a>Voix standard et neuronales

Servez-vous de ce tableau pour connaître la disponibilité des voix standard et neuronales par région/point de terminaison :

| Région | Point de terminaison | Voix standard | Voix neuronales |
|--------|----------|-----------------|---------------|
| Australie Est | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | OUI |
| Centre du Canada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | OUI |
| USA Centre | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | Non |
| Asie Est | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | Non |
| USA Est | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | OUI |
| USA Est 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | Non |
| France Centre | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | Non |
| Inde Centre | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | OUI |
| Japon Est | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | Non |
| Centre de la Corée | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | Non |
| USA Centre Nord | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | Non |
| Europe Nord | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | Non |
| USA Centre Sud | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | OUI |
| Asie Sud-Est | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | OUI |
| Sud du Royaume-Uni | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | OUI |
| Europe Ouest | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | OUI |
| USA Ouest | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | Non |
| USA Ouest 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | OUI | OUI |

### <a name="custom-voices"></a>Voix personnalisées

Si vous avez créé une police de la voix personnalisée, utilisez le point de terminaison que vous avez créé. Vous pouvez aussi utiliser les points de terminaison listés ci-dessous, en remplaçant la valeur de `{deploymentId}` par l’ID de déploiement de votre modèle vocal.

| Région | Point de terminaison |
|--------|----------|
| Australie Est | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Centre du Canada | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA Centre | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Asie Est | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA Est | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA Est 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| France Centre | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Inde Centre | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japon Est | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Centre de la Corée | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA Centre Nord | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europe Nord | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA Centre Sud | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Asie Sud-Est | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Sud du Royaume-Uni | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europe de l'Ouest | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA Ouest | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA Ouest 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
