---
title: Vue d’ensemble de Custom Speech - Service Speech
titleSuffix: Azure Cognitive Services
description: Custom Speech est un ensemble d’outils en ligne qui vous permettent d’évaluer et d’améliorer l’exactitude de la reconnaissance vocale de Microsoft pour vos applications, outils et produits.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: lajanuar
ms.custom: contperf-fy21q2, references_regions
ms.openlocfilehash: 588afaaa95ef871de11583e65d2a9ab831d28fa6
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525545"
---
# <a name="what-is-custom-speech"></a>Qu’est-ce que Custom Speech ?

Custom Speech vous permet d’évaluer et d’améliorer l’exactitude de la reconnaissance vocale de Microsoft pour vos applications et produits. Suivez les liens fournis dans cet article pour commencer à créer une expérience de reconnaissance vocale personnalisée.

## <a name="whats-in-custom-speech"></a>Qu’y a-t-il dans Custom Speech ?

Pour pouvoir tirer parti de Custom Speech, vous avez besoin d’un compte Azure et d’un abonnement au service Speech. Une fois que vous disposez d’un compte, vous pouvez préparer vos données, effectuer l’apprentissage de vos modèles et les tester, inspecter la qualité de la reconnaissance, évaluer l’exactitude et en dernier lieu déployer et utiliser le modèle de reconnaissance vocale personnalisé.

Ce diagramme met en évidence les éléments qui composent la [zone Custom Speech de Speech Studio](https://aka.ms/speechstudio/customspeech). Utilisez les liens ci-dessous pour en savoir plus sur chaque étape.

![Diagramme qui met en évidence les composants de la zone Custom Speech de Speech Studio.](./media/custom-speech/custom-speech-overview.png)

1. [Abonnez-vous et créez un projet](#set-up-your-azure-account). Créez un compte Azure et abonnez-vous au service Speech. Cet abonnement unifié vous donne accès à la conversion de parole en texte, à la conversion de texte par synthèse vocale, à la traduction vocale et à [Speech Studio](https://speech.microsoft.com/customspeech). Ensuite, servez-vous de votre abonnement au service Speech pour créer votre premier projet Custom Speech.

1. [Charger des données de test](./how-to-custom-speech-test-and-train.md). Chargez des données de test (fichiers audio) pour évaluer l’offre de reconnaissance vocale de Microsoft pour vos applications, outils et produits.

1. [Inspecter la qualité de la reconnaissance](how-to-custom-speech-inspect-data.md). Utilisez [Speech Studio](https://speech.microsoft.com/customspeech) pour lire le contenu audio chargé et inspecter la qualité de la reconnaissance vocale de vos données de test. Pour effectuer des mesures quantitatives, consultez [Inspecter des données](how-to-custom-speech-inspect-data.md).

1. [Évaluer et améliorer l’exactitude](how-to-custom-speech-evaluate-data.md). Évaluez et améliorez l’exactitude du modèle de reconnaissance vocale. [Speech Studio](https://speech.microsoft.com/customspeech) indique un *taux d’erreur de mots*, qui vous permet de déterminer si un entraînement supplémentaire est nécessaire. Si le résultat vous paraît suffisamment juste, vous pouvez directement utiliser les API du service Speech. Si vous souhaitez améliorer l’exactitude de 5 à 20 % en moyenne relative, utilisez l’onglet **Formation** du portail pour charger des données d’entraînement supplémentaires, comme des transcriptions étiquetées à la main et le texte associé.

1. [Effectuer l’apprentissage d’un modèle et le déployer](how-to-custom-speech-train-model.md). Améliorez l’exactitude de votre modèle de reconnaissance vocale en joignant des transcriptions écrites (entre 10 et 1 000 heures) et un texte associé (< 200 Mo) à vos données de test audio. Ces données vous aident à entraîner le modèle de reconnaissance vocale. Après l’apprentissage, retestez. Si vous êtes satisfait du résultat, vous pouvez déployer votre modèle sur un point de terminaison personnalisé.

## <a name="set-up-your-azure-account"></a>Configurer votre compte Azure

Vous avez besoin d’un compte Azure et d’un abonnement au service Speech pour pouvoir créer un modèle personnalisé à partir de [Speech Studio](https://speech.microsoft.com/customspeech). Si vous n’avez pas de compte et d’abonnement, [essayez le service Speech gratuitement](overview.md#try-the-speech-service-for-free).

Si vous comptez effectuer l’entraînement d’un modèle personnalisé avec des **données audio**, choisissez l’une des régions suivantes. Elles disposent d’un matériel dédié à l’entraînement. Cela réduira le temps nécessaire à l’apprentissage d’un modèle, et vous permettra d’utiliser plus de données audio pour l’apprentissage. Dans ces régions, le service Speech utilisera jusqu’à 20 heures d’audio pour l’apprentissage. Dans d’autres, il utilisera moins de 8 heures.

* Australie Est
* Centre du Canada
* Inde centrale
* USA Est
* USA Est 2
* Centre-Nord des États-Unis
* Europe Nord
* États-Unis - partie centrale méridionale
* Asie Sud-Est
* Sud du Royaume-Uni
* Gouvernement des États-Unis – Arizona
* Gouvernement américain - Virginie
* Europe Ouest
* USA Ouest 2

Une fois que vous avez créé un compte Azure et un abonnement au service Speech, vous devez vous connecter à [Speech Studio](https://speech.microsoft.com/customspeech) et connecter votre abonnement.

1. Connectez-vous à [Speech Studio](https://aka.ms/custom-speech).
1. Sélectionnez l’abonnement dans lequel vous devez travailler, puis créez un projet Speech.
1. Si vous souhaitez modifier votre abonnement, sélectionnez le bouton en forme de rouage dans le menu supérieur.

## <a name="how-to-create-a-project"></a>Comment créer un projet

Les contenus tels que les données, les modèles, les tests et les points de terminaison sont organisés en *projets* dans [Speech Studio](https://speech.microsoft.com/customspeech). Chaque projet est propre à un domaine et à un pays/langue. Par exemple, vous pourriez créer un projet pour des centres d’appel utilisant l’anglais aux États-Unis.

Pour créer votre premier projet, sélectionnez **Speech-to-text/Custom speech**, puis **Nouveau projet**. Suivez les instructions fournies par l’Assistant pour créer votre projet. Une fois le projet créé, vous devriez voir quatre onglets : **Data**, **Testing**, **Training** et **Deployment**. Utilisez les liens fournis dans [Étapes suivantes](#next-steps) pour savoir comment utiliser chaque onglet.

> [!IMPORTANT]
> [Speech Studio](https://aka.ms/custom-speech), qui s’appelait « portail Custom Speech », a été récemment mis à jour ! Si vous avez créé antérieurement des données, modèles, tests et points de terminaison publiés sur le portail CRIS.ai ou à l’aide d’API, vous devez créer un projet sur le nouveau portail pour vous connecter à ces anciennes entités.

## <a name="model-and-endpoint-lifecycle"></a>Cycle de vie des modèles et des points de terminaison

Les modèles plus anciens deviennent généralement de moins en moins utiles au fil du temps, car le modèle le plus récent est généralement d’une plus grande exactitude. Par conséquent, les modèles de base ainsi que les modèles personnalisés et les points de terminaison créés via le portail expirent après un an pour l’adaptation et deux ans pour le décodage. Consultez une description détaillée dans l’article [Cycle de vie des modèles et des points de terminaison](./how-to-custom-speech-model-and-endpoint-lifecycle.md).

## <a name="next-steps"></a>Étapes suivantes

* [Préparer et tester les données](./how-to-custom-speech-test-and-train.md)
* [Inspecter les données](how-to-custom-speech-inspect-data.md)
* [Évaluer et améliorer la justesse du modèle](how-to-custom-speech-evaluate-data.md)
* [Entraîner et déployer un modèle](how-to-custom-speech-train-model.md)
