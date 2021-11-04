---
title: Références SKU et tarifs des services cognitifs
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2021
ms.author: pafarley
ms.openlocfilehash: f20f73c37224abe1333d2577db8a54ef07eeed34
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131521216"
---
Consultez la liste des références SKU et des informations tarifaires ci-dessous. 

#### <a name="multi-service"></a>Multiservice

| Service     | Type    |
|-------------|------------|
| Plusieurs services. Pour plus d’informations, consultez la page des [tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/).            | `CognitiveServices`     |


#### <a name="vision"></a>Vision

| Service    | Type    |
|------------|---------|
| Vision par ordinateur            | `ComputerVision`          |
| Custom Vision - Prédiction | `CustomVision.Prediction` |
| Custom Vision - Formation   | `CustomVision.Training`   |
| Face                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |

#### <a name="speech"></a>Speech

| Service            | Type                 |
|--------------------|----------------------|
| Services Speech    | `SpeechServices`     |
| Reconnaissance vocale | `SpeakerRecognition` |

#### <a name="language"></a>Langage

| Service            | Type                |
|--------------------|---------------------|
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Service Language   | `TextAnalytics`     |
| Traduction de texte   | `TextTranslation`   |

#### <a name="decision"></a>Décision

| Service           | Type               |
|-------------------|--------------------|
| Le détecteur d’anomalies  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizer      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Niveaux tarifaires et facturation

Les niveaux tarifaires (et le montant facturé) sont basés sur le nombre de transactions que vous envoyez à l’aide de vos informations d’authentification. Chaque niveau tarifaire spécifie :
* le nombre maximal de transactions par seconde (TPS) autorisées ;
* les fonctionnalités de service activées dans le niveau tarifaire ;
* le coût d'un nombre prédéfini de transactions. Si vous dépassez ce nombre, des frais supplémentaires vous seront facturés, comme indiqué dans les [détails de la tarification](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) de votre service.

> [!NOTE]
> De nombreuses ressources Cognitive Services disposent d'un niveau gratuit que vous pouvez utiliser pour tester le service. Pour utiliser le niveau gratuit, utilisez `F0` en tant que référence SKU de votre ressource.