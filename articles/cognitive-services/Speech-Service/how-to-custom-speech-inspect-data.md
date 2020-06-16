---
title: Inspecter la qualité des données pour Custom Speech - Service Speech
titleSuffix: Azure Cognitive Services
description: Custom Speech propose des outils qui vous permettent d’inspecter visuellement la qualité de la reconnaissance d’un modèle en comparant les données audio au résultat de la reconnaissance correspondante. Vous pouvez lire le contenu audio chargé pour déterminer si le résultat proposé de la reconnaissance est correct.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: e871d2c8e0fe00fa7db3144a787447163c82e62d
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629034"
---
# <a name="inspect-custom-speech-data"></a>Inspecter des données Custom Speech

> [!NOTE]
> Cette page suppose que vous avez lu [Préparer des données de test pour Custom Speech](how-to-custom-speech-test-data.md) et que vous avez chargé un jeu de données pour l’inspecter.

Custom Speech propose des outils qui vous permettent d’inspecter visuellement la qualité de la reconnaissance d’un modèle en comparant les données audio au résultat de la reconnaissance correspondante. À partir du [portail Custom Speech](https://speech.microsoft.com/customspeech), vous pouvez lire le contenu audio chargé et déterminer si le résultat proposé de la reconnaissance est correct. Cet outil vous permet d’inspecter la qualité du modèle de reconnaissance vocale de base de Microsoft, d’inspecter un modèle personnalisé entraîné ou de comparer la transcription selon deux modèles.

Dans ce document, vous allez apprendre à inspecter visuellement la qualité d’un modèle en utilisant les données d’entraînement que vous avez chargées précédemment.

Dans cette page, vous allez apprendre à inspecter visuellement la qualité du modèle de reconnaissance vocale de référence de Microsoft et/ou d’un modèle personnalisé que vous avez entraîné. Vous allez utiliser les données que vous avez chargées sous l’onglet **Data** à des fins de test.

## <a name="create-a-test"></a>Créer un test

Pour créer un test, suivez ces instructions :

1. Connectez-vous au [portail Custom Speech](https://speech.microsoft.com/customspeech).
2. Accédez à **Synthèse vocale > Custom Speech > [nom du projet] > Test en cours**.
3. Cliquez sur **Add Test** (Ajouter un test).
4. Sélectionnez **Inspect quality (Audio-only data)** [Inspecter la qualité (données audio uniquement)]. Nommez et décrivez le test et sélectionnez votre jeu de données audio.
5. Sélectionnez les modèles que vous voulez tester (deux au maximum).
6. Cliquez sur **Créer**.

Une fois qu’un test a été créé avec succès, vous pouvez voir comment un modèle transcrit le jeu de données audio que vous avez spécifié ou comparer les résultats de deux modèles côte à côte.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Comparaisons côte à côte de modèles

Quand l’état du test est _Succeeded_ (Opération réussie), cliquez sur le nom du test pour en voir les détails. Cette page de détails liste tous les énoncés de votre jeu de données et affiche les résultats de reconnaissance des deux modèles que vous comparez.

Pour inspecter plus facilement la comparaison côte à côte, vous pouvez voir les différents types d’erreurs (insertion, suppression et substitution). En écoutant le contenu audio et en comparant les résultats de la reconnaissance dans chaque colonne (transcription étiquetée à la main et résultats des deux modèles de reconnaissance vocale), vous pouvez identifier le modèle qui répond à vos besoins et à quel niveau il est nécessaire d’apporter des améliorations.

Le test de modèles côte à côte est utile pour valider le modèle de reconnaissance vocale le mieux adapté à une application. Pour obtenir une mesure objective de la précision, ce qui nécessite que le contenu audio soit transcrit, suivez les instructions fournies dans [Évaluer la précision](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Étapes suivantes

- [Évaluer les données](how-to-custom-speech-evaluate-data.md)
- [Entraîner un modèle](how-to-custom-speech-train-model.md)
- [Améliorer un modèle](how-to-custom-speech-improve-accuracy.md)
- [Déployer un modèle](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ressources supplémentaires

- [Préparer les données de test pour Custom Speech](how-to-custom-speech-test-data.md)
