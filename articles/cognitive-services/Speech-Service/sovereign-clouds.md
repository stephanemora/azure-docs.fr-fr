---
title: Clouds souverains – Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser les clouds souverains
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 01/07/2021
ms.author: alexeyo
ms.openlocfilehash: f30b1f0f14bba54b8b4fcd7c5190f3c533f199a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98061750"
---
# <a name="speech-services-in-sovereign-clouds"></a>Services Speech dans des clouds souverains

## <a name="azure-government-united-states"></a>Azure Government (États-Unis)

Disponible uniquement pour les entités gouvernementales américaines et leurs partenaires. Pour plus d’informations sur Azure Government, consultez [cette page](../../azure-government/documentation-government-welcome.md) et [cette page.](../../azure-government/compare-azure-government-global-azure.md)

- **Portail Azure :**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **Régions :**
  - Gouvernement des États-Unis – Arizona
  - Gouvernement américain - Virginie
- **Niveaux tarifaires disponibles** :
  - Gratuit (F0) et Standard (S0). Vous trouverez plus de détails [ici](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
- **Fonctionnalités prises en charge :**
  - Reconnaissance vocale
    - Reconnaissance vocale personnalisée (modèle acoustique (AM) et modèle de langage (LM))
      - [Speech Studio](https://speech.azure.us/)
  - Synthèse vocale
    - Voix standard
    - Voix neuronale
  - Traduction vocale
- **Fonctionnalités non prises en charge :**
  - Custom Voice
- **Langues prises en charge :**
  - Consultez la liste des langues prises en charge [ici](language-support.md).

### <a name="endpoint-information"></a>Informations sur le point de terminaison

Cette section contient des informations sur le point de terminaison des services Speech pour utilisation avec le [SDK Speech](speech-sdk.md), l’[API REST de reconnaissance vocale](rest-speech-to-text.md) et l’[API REST de synthèse vocale](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>API REST Speech Services

Les points de terminaison de l’API REST des services Speech dans Azure Government ont le format suivant :

|  Type/opération de l’API REST | Format du point de terminaison |
|--|--|
| Access token (Jeton d’accès) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [API REST de reconnaissance vocale v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [API REST de reconnaissance vocale pour audio court](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [API REST de synthèse vocale](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

Remplacez `<REGION_IDENTIFIER>` par l'identificateur correspondant à la région de votre abonnement dans le tableau suivant :

|                     | Identificateur de la région |
|--|--|
| **Gouvernement des États-Unis – Arizona**  | `usgovarizona` |
| **Gouvernement américain - Virginie** | `usgovvirginia` |

#### <a name="speech-sdk"></a>Kit de développement logiciel (SDK) de reconnaissance vocale

Pour le SDK Speech dans les clouds souverains, vous devez utiliser l’instanciation « à partir de l’hôte » de la classe `SpeechConfig` ou l’option `--host` de l’[interface CLI Speech](spx-overview.md). (Vous pouvez également utiliser l’instanciation « à partir du point de terminaison » et l’option `--endpoint` d’interface CLI Speech).

La classe `SpeechConfig` doit être instanciée comme suit :
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
L’interface CLI Speech doit être utilisée comme ceci (notez l’option `--host`) :
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
Remplacez `subscriptionKey` par votre clé de ressource Speech. Remplacez `usGovHost` par l’expression qui correspond à l’offre de service requise et la région de votre abonnement à partir de cette table :

|  Région/offre de service | Expression hôte |
|--|--|
| **Gouvernement des États-Unis – Arizona** | |
| Reconnaissance vocale | `wss://usgovarizona.stt.speech.azure.us` |
| Synthèse vocale | `https://usgovarizona.tts.speech.azure.us` |
| **Gouvernement américain - Virginie** | |
| Reconnaissance vocale | `wss://usgovvirginia.stt.speech.azure.us` |
| Synthèse vocale | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure Chine

Disponible pour les organisations ayant une présence commerciale en Chine. Pour plus d’informations sur Azure Chine, [cliquez ici](/azure/china/overview-operations). 


- **Portail Azure :**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **Régions :**
  - Chine orientale 2
- **Niveaux tarifaires disponibles** :
  - Gratuit (F0) et Standard (S0). Vous trouverez plus de détails [ici](https://www.azure.cn/pricing/details/cognitive-services/index.html)
- **Fonctionnalités prises en charge :**
  - Reconnaissance vocale
    - Reconnaissance vocale personnalisée (modèle acoustique (AM) et modèle de langage (LM))
      - [Speech Studio](https://speech.azure.cn/)
  - Synthèse vocale
    - Voix standard
    - Voix neuronale
  - Traduction vocale
- **Fonctionnalités non prises en charge :**
  - Custom Voice
- **Langues prises en charge :**
  - Consultez la liste des langues prises en charge [ici](language-support.md).

### <a name="endpoint-information"></a>Informations sur le point de terminaison

Cette section contient des informations sur le point de terminaison des services Speech pour utilisation avec le [SDK Speech](speech-sdk.md), l’[API REST de reconnaissance vocale](rest-speech-to-text.md) et l’[API REST de synthèse vocale](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>API REST Speech Services

Les points de terminaison de l’API REST des services Speech dans Azure Chine ont le format suivant :

|  Type/opération de l’API REST | Format du point de terminaison |
|--|--|
| Access token (Jeton d’accès) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [API REST de reconnaissance vocale v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [API REST de reconnaissance vocale pour audio court](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [API REST de synthèse vocale](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

Remplacez `<REGION_IDENTIFIER>` par l'identificateur correspondant à la région de votre abonnement dans le tableau suivant :

|                     | Identificateur de la région |
|--|--|
| **Chine orientale 2**  | `chinaeast2` |

#### <a name="speech-sdk"></a>Kit de développement logiciel (SDK) de reconnaissance vocale

Pour le SDK Speech dans les clouds souverains, vous devez utiliser l’instanciation « à partir de l’hôte » de la classe `SpeechConfig` ou l’option `--host` de l’[interface CLI Speech](spx-overview.md). (Vous pouvez également utiliser l’instanciation « à partir du point de terminaison » et l’option `--endpoint` d’interface CLI Speech).

La classe `SpeechConfig` doit être instanciée comme suit :
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
L’interface CLI Speech doit être utilisée comme ceci (notez l’option `--host`) :
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
Remplacez `subscriptionKey` par votre clé de ressource Speech. Remplacez `azCnHost` par l’expression qui correspond à l’offre de service requise et la région de votre abonnement à partir de cette table :

|  Région/offre de service | Expression hôte |
|--|--|
| **Chine orientale 2** | |
| Reconnaissance vocale | `wss://chinaeast2.stt.speech.azure.cn` |
| Synthèse vocale | `https://chinaeast2.tts.speech.azure.cn` |