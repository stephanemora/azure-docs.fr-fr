---
title: Forum aux questions sur le service de reconnaissance vocale dans Azure | Microsoft Docs
description: Vous trouverez ici des réponses aux questions les plus posées sur le service de reconnaissance vocale.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 4a29435c0ace79fc3a5d3a5a42a0e91bdbc8da5e
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082822"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Forum aux questions sur la synthèse vocale

Si vous ne trouvez pas les réponses à vos questions dans ce Forum aux questions, essayez d’interroger la Communauté Custom Speech Service sur [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) et [UserVoice](https://cognitive.uservoice.com/).

## <a name="general"></a>Généralités

**Question** : Quelle est la différence entre les modèles vocaux standard et personnalisés ?

**Réponse**  : Les modèles vocaux standard (également appelés polices de la voix) ont été formés avec des données appartenant à Microsoft et sont déjà déployés dans le cloud. Les modèles vocaux personnalisés permettent à l’utilisateur soit d’adapter un modèle moyen et de transférer le timbre et la manière de s’exprimer en fonction du style de voix de l’orateur, soit de former un modèle entièrement nouveau basé sur les données d’apprentissage préparées par l’utilisateur. Aujourd'hui, de plus en plus de clients veulent avoir une voie unique et reconnaissable pour leurs robots. Dans ce cas, la plateforme de création de voix personnalisées est le bon choix.

**Question** : Par où commencer si je souhaite utiliser un modèle vocal standard ?

**Réponse**: Plus de 80 modèles vocaux standard dans plus de 45 langues sont disponibles via des requêtes HTTP. Vous devez tout d’abord obtenir une [clé d’abonnement](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started). Si vous voulez passer des appels REST à des modèles vocaux prédéployés, consultez les [informations détaillées ici](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

**Question**: Si je souhaite utiliser un modèle vocal personnalisé, l’API est-elle la même que celle des voix standard ?

**Réponse**: Lorsque vous avez créé et déployé le modèle vocal personnalisé, vous obtenez un point de terminaison unique pour votre modèle. Vous devez spécifier le point de terminaison dans vos requêtes HTTP pour que la voix parle dans vos applications. La fonctionnalité disponible via l’API REST pour le service de synthèse vocale l’est également pour votre point de terminaison personnalisé. Voyez comment [créer et utiliser votre point de terminaison personnalisé](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**Question** : Dois-je préparer les données d’apprentissage pour créer moi-même des modèles vocaux personnalisés ?

**Réponse** : Vous devrez préparer vous-même les données d’apprentissage. Une collection de données vocales est nécessaire pour créer un modèle vocal personnalisé. Cette collection se compose d’un ensemble de fichiers audio d’enregistrements vocaux et d’un fichier texte de transcription pour chaque fichier audio. Le résultat de votre voix numérique dépend fortement de la qualité de vos données d’apprentissage. Pour produire une bonne voix TTS, il est important que les enregistrements soient effectués dans une pièce calme avec un micro sur support. Pour générer une voix numérique de qualité, il est essentiel de faire attention à l’homogénéité du volume, au débit et à la tonalité, ainsi qu’à la cohérence dans la manière de s’exprimer. Nous vous recommandons vivement d’enregistrer les voix dans un studio d’enregistrement.
Pour le moment, nous ne prenons pas en charge l’enregistrement en ligne et ne recommandons pas de studios d’enregistrement. Concernant les formats requis, consultez [Comment préparer les enregistrements et les transcriptions](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts)
 
**Question** : Quels scripts dois-je utiliser pour enregistrer les données vocales pour l’apprentissage de la voix personnalisée ? 

**Réponse** : Nous ne limitons pas les scripts pour l’enregistrement vocal. Vous pouvez utiliser vos propres scripts pour enregistrer la voix. Veillez simplement à avoir suffisamment de couverture phonétique dans vos données vocales. Pour former une voix personnalisée, vous pouvez démarrer avec un petit volume de données vocales, ce qui pourrait correspondre à 50 phrases différentes (sur 3 à 5 minutes d’enregistrement). Plus vous fournirez de données, plus votre voix sera naturelle. Vous pouvez commencer à former une police de la voix complète lorsque vous fournissez des enregistrements de plus de 2 000 phrases (environ 3 à 4 heures d’enregistrement). Pour obtenir une voix complète de grande qualité, vous devrez préparer des enregistrements de plus de 6 000 phrases (environ 8 à 10 heures d’enregistrement).  
Nous fournissons des services supplémentaires afin de vous aider à préparer des scripts pour l’enregistrement. Contactez le [service clientèle chargé des voix personnalisées](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) pour obtenir des informations.

**Question** : Que faire si j’ai besoin d’une concurrence plus élevée que la valeur par défaut ou que ce qui est proposé dans le portail ?

**Réponse** : Vous pouvez augmenter la taille de votre modèle par incréments de 20 requêtes simultanées. Contactez le [service clientèle chargé des voix personnalisées](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) pour obtenir des informations sur les niveaux de concurrence supérieurs.

**Question** : Puis-je télécharger mon modèle et l’exécuter localement ?

**Réponse** : Les modèles ne peuvent pas être téléchargés et exécutés localement.

## <a name="next-steps"></a>Étapes suivantes

* [Résolution des problèmes](troubleshooting.md)
* [Notes de publication](releasenotes.md)
