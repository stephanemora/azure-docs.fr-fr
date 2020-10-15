---
title: Forum aux questions sur la synthèse vocale
titleSuffix: Azure Cognitive Services
description: Obtenez les réponses aux questions fréquentes sur le service Synthèse vocale.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.openlocfilehash: f06fda777cbebd8034ac4fd9254a4bd172d011a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88661501"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Forum aux questions sur la synthèse vocale

Si vous ne trouvez pas de réponses à vos questions dans ce FAQ, consultez les [autres options de support](support.md).

## <a name="general"></a>Général

**Q : Quelle est la différence entre un modèle vocal standard et un modèle vocal personnalisé ?**

**R** : Le modèle vocal standard (également appelé une _police de la voix_) a été formé à l’aide de données appartenant à Microsoft et est déjà déployé dans le cloud. Vous pouvez utiliser un modèle vocal personnalisé pour adapter un modèle moyen et transférer le timbre et l’expression du style de voix de l’orateur ou former un modèle entièrement nouveau basé sur les données d’apprentissage préparées par l’utilisateur. Aujourd’hui, de plus en plus de clients veulent une voix unique et reconnaissable pour leurs robots. Dans ce cas, la plateforme de création de voix personnalisées est la bonne option.

**Q : Par où commencer si je souhaite utiliser un modèle vocal standard ?**

**R** : Plus de 80 modèles vocaux standard dans plus de 45 langues sont disponibles via des requêtes HTTP. Commencez par obtenir une [clé d’abonnement](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). Pour effectuer des appels REST pour les modèles de voix prédéployés, consultez l’[API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

**Q : Si je souhaite utiliser un modèle vocal personnalisé, l’API est-elle la même que celle utilisée pour les voix standard ?**

**R** : Lorsqu’un modèle vocal personnalisé est créé et déployé, vous obtenez un point de terminaison unique pour votre modèle. Pour que la voix parle dans vos applications, vous devez spécifier le point de terminaison dans vos requêtes HTTP. La fonctionnalité qui est disponible dans l’API REST pour le service Synthèse vocale l’est également pour votre point de terminaison personnalisé. Découvrez comment [créer et utiliser votre point de terminaison personnalisé](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-voice-endpoint).

**Q : Dois-je préparer les données d’apprentissage pour créer moi-même des modèles vocaux personnalisés ?**

**R** : Oui, vous devez préparer les données d’apprentissage vous-même pour un modèle vocal personnalisé.

Une collection de données vocales est nécessaire pour créer un modèle vocal personnalisé. Cette collection se compose d’un ensemble de fichiers audio d’enregistrements vocaux et d’un fichier texte de transcription pour chaque fichier audio. Le résultat de votre voix numérique dépend fortement de la qualité de vos données d’apprentissage. Pour produire une bonne voix de synthèse vocale, il est important que les enregistrements soient effectués dans une pièce calme avec un micro sur support. Pour générer une voix numérique de qualité, il est essentiel de faire attention à l’homogénéité du volume, au débit et à la tonalité, ainsi qu’à la cohérence dans la manière de s’exprimer. Nous vous recommandons vivement d’enregistrer les voix dans un studio d’enregistrement.

Nous ne prenons actuellement pas en charge l’enregistrement en ligne et ne recommandons pas de studios d’enregistrement. Concernant les formats requis, consultez [Comment préparer les enregistrements et les transcriptions](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-create-voice).

**Q : Quels scripts dois-je utiliser pour enregistrer les données vocales pour l’apprentissage de la voix personnalisée ?**

**R** : Nous ne limitons pas les scripts pour l’enregistrement vocal. Vous pouvez utiliser vos propres scripts pour enregistrer la voix. Veillez simplement à disposer d’une couverture phonétique suffisante dans vos données vocales. Pour former une voix personnalisée, vous pouvez démarrer avec un petit volume de données vocales, ce qui peut correspondre à 50 phrases différentes (environ 3 à 5 minutes d’enregistrement). Plus vous fournirez de données, plus votre voix sera naturelle. Vous pouvez commencer à former une police de la voix complète lorsque vous fournissez des enregistrements de plus de 2 000 phrases (environ 3 à 4 heures d’enregistrement). Pour obtenir une voix complète de grande qualité, vous devez préparer des enregistrements de plus de 6 000 phrases (environ 8 à 10 heures d’enregistrement).

Nous fournissons des services supplémentaires afin de vous aider à préparer des scripts pour l’enregistrement. Contactez le [service clientèle chargé des voix personnalisées](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) pour obtenir des informations.

**Q : Que faire si j’ai besoin d’une concurrence plus élevée que la valeur par défaut ou que ce qui est proposé dans le portail ?**

**R** : Vous pouvez augmenter l’échelle de votre modèle par incréments de 20 requêtes simultanées. Contactez le [service clientèle chargé des voix personnalisées](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) pour obtenir des informations sur les niveaux de concurrence supérieurs.

**Q : Puis-je télécharger mon modèle et l’exécuter localement ?**

**R** : Il n’est pas possible de télécharger et d’exécuter des modèles localement.

**Q : Mes requêtes sont-elles limitées ?**

**R** : Consultez [Quotas et limites des services Speech](speech-services-quotas-and-limits.md).

## <a name="next-steps"></a>Étapes suivantes

- [Dépannage](troubleshooting.md)
- [Notes de publication](releasenotes.md)
