---
title: Vue d’ensemble de Speech Studio - Service Speech
titleSuffix: Azure Cognitive Services
description: Speech Studio est un ensemble d’outils basés sur une interface utilisateur permettant de créer et d’intégrer des fonctionnalités du service Azure Speech dans vos applications.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2021
ms.author: trbye
ms.openlocfilehash: ddf7b85f5775ee25a260f19ae81e43afff95facb
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111902332"
---
# <a name="what-is-speech-studio"></a>Qu’est-ce que Speech Studio ?

[Speech Studio](https://speech.microsoft.com) est un ensemble d’outils basés sur une interface utilisateur permettant de créer et d’intégrer des fonctionnalités du service Azure Speech dans vos applications. Vous créez des projets dans Speech Studio à l’aide d’une approche sans code, puis vous référencez les ressources que vous créez dans vos applications à l’aide du [SDK Speech](speech-sdk.md), de l’[interface CLI Speech](spx-overview.md) ou de différentes API REST.

## <a name="set-up-your-azure-account"></a>Configurer votre compte Azure

Vous avez besoin d’un compte Azure et d’un abonnement au service Speech pour pouvoir utiliser [Speech Studio](https://speech.microsoft.com). Si vous n’avez pas de compte et d’abonnement, [essayez le service Speech gratuitement](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Veillez à créer un abonnement standard (S0). Les abonnements gratuits (F0) ne sont pas pris en charge.

Après avoir créé un compte Azure et un abonnement au service Speech :

1. Connectez-vous à [Speech Studio](https://speech.microsoft.com).
1. Sélectionnez l’abonnement dans lequel vous devez travailler, puis créez un projet Speech.
1. Si vous souhaitez modifier votre abonnement, sélectionnez le bouton en forme de rouage dans le menu supérieur.

## <a name="speech-studio-features"></a>Fonctionnalités de Speech Studio

Les fonctionnalités suivantes du service Speech sont disponibles sous forme de types de projet dans Speech Studio.

* **Reconnaissance vocale en temps réel** : Testez rapidement la reconnaissance vocale en déplaçant des fichiers audio par glisser-déposer sans utiliser de code. Il s’agit d’un outil de démonstration permettant de voir comment fonctionne la reconnaissance vocale sur vos échantillons audio. Consultez cependant la [vue d’ensemble](speech-to-text.md) de la reconnaissance vocale pour explorer toutes les fonctionnalités disponibles.
* **Custom Speech** : Custom Speech vous permet de créer des modèles de reconnaissance vocale adaptés à des jeux de vocabulaire et types de diction spécifiques. Contrairement aux modèles de reconnaissance vocale de base, les modèles Custom Speech contribuent à vous doter d’un avantage concurrentiel unique, car ils ne sont pas accessibles publiquement. Consultez le [guide de démarrage rapide](how-to-custom-speech-test-and-train.md) pour commencer à télécharger des échantillons audio afin de créer un modèle Custom Speech.
* **Évaluation de la prononciation** : Cette fonctionnalité évalue la prononciation des entrées vocales et fournit des commentaires aux orateurs sur la justesse et l’aisance des paroles prononcées. Speech Studio fournit un bac à sable qui vous permet de tester cette fonctionnalité rapidement sans code. Consultez cependant l’[article de procédure](how-to-pronunciation-assessment.md) expliquant comment l’utiliser avec le SDK Speech dans vos applications.
* **Galerie vocale** : Créez des applications et services qui parlent d’une façon naturelle. Choisissez parmi plus de 170 voix dans plus de 70 langues et variantes. Donnez vie à vos scénarios avec des voix neuronales très expressives et humaines.
* **Custom Voice** : Custom Voice vous permet de créer des voix personnalisées et uniques pour la synthèse vocale. Vous fournissez des fichiers audio et créez les transcriptions correspondantes dans Speech Studio, puis vous utilisez les voix personnalisées dans vos applications. Consultez l’[article de procédure](how-to-custom-voice-create-voice.md) sur la création et l’utilisation de voix personnalisées par le biais de points de terminaison. 
* **Création de contenu audio** : [Création de contenu audio](how-to-audio-content-creation.md) est un outil simple d’utilisation qui vous permet de créer du contenu audio très naturel pour un large éventail de scénarios comme les livres audio, les diffusions d’actualités, les narrations vidéo et les chatbots. Speech Studio vous permet d’exporter les fichiers audio que vous créez pour les utiliser dans vos applications.
* **Mot clé personnalisé** : Un mot clé personnalisé est un mot ou une expression courte qui permet d’activer votre produit à la voix. Vous créez un mot clé personnalisé dans Speech Studio, puis générez un fichier binaire à [utiliser avec le SDK Speech](custom-keyword-basics.md) dans vos applications.
* **Commandes personnalisées** : Ces commandes facilitent la création d’applications de commandes vocales complètes, optimisées pour les expériences d’interaction de type « voice-first ». Elles offrent une expérience de création sans code dans Speech Studio, un modèle d’hébergement automatique et une complexité relativement inférieure, vous permettant de vous concentrer sur la conception de la meilleure solution pour vos scénarios de commandes vocales. Pour plus d’informations sur la création d’applications de commandes personnalisées, consultez ce [guide pratique](how-to-develop-custom-commands-application.md). Consultez également le guide sur l’[intégration de votre application de commandes personnalisées avec le SDK Speech](how-to-custom-commands-setup-speech-sdk.md).

## <a name="next-steps"></a>Étapes suivantes

[Explorez Speech Studio](https://speech.microsoft.com) et créez un projet.




