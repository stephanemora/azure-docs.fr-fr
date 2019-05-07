---
title: Qu’est-ce que Custom Voice ? -Services de reconnaissance vocale
titlesuffix: Azure Cognitive Services
description: Vocaux personnalisés est un ensemble d’outils en ligne qui vous permettent de créer une voix unique de genre reconnaissable pour votre marque. Il vous suffit pour bien démarrer sont un certain nombre de fichiers audio et les transcriptions associées. Suivez les liens ci-dessous pour commencer à créer une expérience de reconnaissance vocale personnalisée.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 8cf9dc6cbfc96448462aac3a64807f8beb6036ad
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156910"
---
# <a name="get-started-with-custom-voice"></a>Prise en main les vocal personnalisé

Vocaux personnalisés est un ensemble d’outils en ligne qui vous permettent de créer une voix unique de genre reconnaissable pour votre marque. Il vous suffit pour bien démarrer sont un certain nombre de fichiers audio et les transcriptions associées. Suivez les liens ci-dessous pour commencer à créer une expérience de synthèse vocale personnalisée.

## <a name="whats-in-custom-voice"></a>Nouveautés dans vocaux personnalisés

Avant de commencer avec les voix personnalisé, vous aurez besoin d’un compte Azure et un abonnement Services de reconnaissance vocale. Une fois que vous avez créé un compte, vous pouvez préparer vos données, former et tester vos modèles, évaluer la qualité de la voix et finalement déployer votre modèle vocal personnalisé.

Le diagramme ci-dessous met en évidence les étapes pour créer un modèle vocal personnalisé à l’aide du portail vocaux personnalisés. Utilisez les liens pour en savoir plus.

![Diagramme d’architecture vocal personnalisé](media/custom-voice/custom-voice-diagram.png)

1.  [S’abonner et créez un projet](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice?branch=release-build-cogserv-speech-services#set-up-your-azure-account) : créer un compte Azure et créer un abonnement aux Services de reconnaissance vocale. Cet abonnement unifié vous donne accès à la reconnaissance vocale, synthèse vocale, traduction vocale et le portail personnalisé voix. Puis, à l’aide de votre abonnement aux Services de reconnaissance vocale, créer votre premier projet personnalisé voix.

2.  [Charger des données](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#upload-your-datasets) -charger des données (audio et texte) via le portail de voix de personnalisé ou d’une API de voix personnalisé. À partir du portail, vous pouvez examiner et évaluer les scores de prononciation et des ratios de signal / bruit. Pour plus d’informations, consultez [comment préparer les données pour la fonctionnalité vocale personnalisée](how-to-custom-voice-prepare-data.md).

3.  [Former votre modèle](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#build-your-voice-font) – utiliser vos données pour créer un modèle personnalisé de voix de synthèse vocale. Vous pouvez former un modèle dans différentes langues. Après une formation, testez votre modèle, et si vous êtes satisfait du résultat, vous pouvez déployer le modèle.

4.  [Déployer votre modèle](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#create-and-use-a-custom-endpoint) - créer un point de terminaison personnalisé pour votre modèle de voix de synthèse vocale et l’utiliser pour la synthèse vocale dans vos produits, les outils et les applications.

## <a name="set-up-your-azure-account"></a>Configurer votre compte Azure

Un abonnement de Services de reconnaissance vocale est requis avant de pouvoir utiliser le portail de reconnaissance vocale personnalisé pour créer un modèle personnalisé. Suivez ces instructions pour créer un abonnement de Services de reconnaissance vocale dans Azure. Si vous n’avez pas un compte Azure, vous pouvez vous inscrire pour un nouveau.  

Une fois que vous avez créé un compte Azure et un abonnement Services de reconnaissance vocale, vous devez vous connecter au portail personnalisé voix et de se connecter à votre abonnement.

1. Obtenir votre clé d’abonnement Services de reconnaissance vocale à partir du portail Azure.
2. Se connecter à la [portail personnalisé vocale](https://aka.ms/custom-voice).
3. Sélectionnez votre abonnement et créer un projet de reconnaissance vocale.
4. Si vous souhaitez basculer vers un autre abonnement vocale, utilisez l’icône d’engrenage situé dans le volet de navigation supérieur.

> [!NOTE]
> Le service personnalisé voix ne prend pas en charge la clé de version d’évaluation gratuite de 30 jours. Vous devez disposer d’un F0 ou une clé de S0 créée dans Azure avant de pouvoir utiliser le service.

## <a name="how-to-create-a-project"></a>Comment créer un projet

Contenu comme points de terminaison, les modèles, les tests et les données sont organisées en **projets** dans le portail personnalisé voix. Chaque projet est spécifique à un pays/langage et le genre de la voix que vous souhaitez créer. Par exemple, vous pouvez créer un projet pour une voix féminine pour les robots de conversation de votre centre d’appels qui utilisent l’anglais des États-Unis (en-US).

Pour créer votre premier projet, sélectionnez le **Text-to-Speech/Custom voix** onglet, puis cliquez sur **nouveau projet**. Suivez les instructions fournies par l’Assistant pour créer votre projet. Une fois que vous avez créé un projet, vous voyez quatre onglets : **Données**, **formation**, **test**, et **déploiement**. Utilisez les liens fournis dans [étapes suivantes](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice?branch=release-build-cogserv-speech-services#next-steps) pour apprendre à utiliser chaque onglet.

## <a name="next-steps"></a>Étapes suivantes

- [Préparer les données de voix de personnalisé](how-to-custom-voice.md)
- [Créer une voix personnalisée](how-to-custom-voice-create-voice.md)
- [Guide : Enregistrer vos exemples de voix](record-custom-voice-samples.md)
