---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: d5a4b3a07854c2664de7ec60f3677b666798a9bd
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237072"
---
### <a name="standard-and-neural-voices"></a>Voix standard et neuronaux

Utilisez ce tableau pour déterminer la disponibilité de voix standard et neuronaux par région/point de terminaison :

| Région | Point de terminaison | Voix standard | Voix neuronaux |
|--------|----------|-----------------|---------------|
| Australie Est | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| Centre du Canada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| USA Centre | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| Asie Est | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| USA Est | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| USA Est 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| France Centre | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| Inde Centre | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| Japon Est | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| Centre de la Corée | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| USA Centre Nord | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| Europe Nord | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| USA Centre Sud | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| Asie du Sud-Est | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| Sud du Royaume-Uni | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| Europe Ouest | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |
| USA Ouest | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Non |
| USA Ouest 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Oui | Oui |

### <a name="custom-voices"></a>Voix personnalisées

Si vous avez créé une police vocaux personnalisés, utilisez le point de terminaison que vous avez créée. Vous pouvez également utiliser des points de terminaison répertoriés ci-dessous, en remplaçant le `{deploymentId}` avec l’ID de déploiement pour votre modèle de voix.

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
| Asie du Sud-Est | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Sud du Royaume-Uni | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europe de l'Ouest | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA Ouest | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA Ouest 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
