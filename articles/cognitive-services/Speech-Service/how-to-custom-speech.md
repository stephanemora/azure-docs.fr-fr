---
title: Bien démarrer avec la reconnaissance vocale personnalisée - Services de reconnaissance vocale
titlesuffix: Azure Cognitive Services
description: Reconnaissance vocale personnalisé est un ensemble d’outils en ligne qui vous permettent d’évaluer et améliorer la précision de reconnaissance vocale de Microsoft pour vos applications, les outils et les produits. Il vous suffit pour bien démarrer sont un certain nombre de fichiers audio de test. Suivez les liens ci-dessous pour commencer à créer une expérience de reconnaissance vocale personnalisée.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: ab33feb1ffdbced193afaba8f52719b3c215652f
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511097"
---
# <a name="what-is-custom-speech"></a>Quelle est la reconnaissance vocale personnalisé ?

[Reconnaissance vocale personnalisé](https://aka.ms/custom-speech) est un ensemble d’outils en ligne qui vous permettent d’évaluer et améliorer la précision de reconnaissance vocale de Microsoft pour vos applications, les outils et les produits. Il vous suffit pour bien démarrer sont un certain nombre de fichiers audio de test. Suivez les liens ci-dessous pour commencer à créer une expérience de reconnaissance vocale personnalisée.

## <a name="whats-in-custom-speech"></a>Nouveautés de reconnaissance vocal personnalisé ?

Avant de pouvoir effectuer quoi que ce soit avec reconnaissance vocale personnalisé, vous devez un compte Azure et un abonnement Services de reconnaissance vocale. Une fois que vous avez un compte, vous pouvez préparer vos données, former et tester vos modèles, inspecter la qualité de la reconnaissance, évaluer la précision et finalement déployer et utiliser le modèle personnalisé de la parole-texte.

Ce diagramme met en évidence les éléments qui composent le portail de reconnaissance vocale personnalisé. Utilisez les liens ci-dessous pour en savoir plus sur chaque étape.

![Met en évidence les différents composants qui composent le portail de reconnaissance vocale personnalisé.](./media/custom-speech/custom-speech-overview.png)

1. [S’abonner et créez un projet](#set-up-your-azure-account) : créer un compte Azure et s’abonner les Services de reconnaissance vocale. Cet abonnement unifié vous donne accès à la reconnaissance vocale, synthèse vocale, traduction vocale et le portail custom speech. Ensuite, à l’aide de votre abonnement aux Services de reconnaissance vocale, créez votre premier projet de reconnaissance vocale personnalisé.

2. [Charger des données de test](how-to-custom-speech-test-data.md) -données de test de chargement (fichiers audio) à évaluer speech-reconnaissance de Microsoft offre pour vos applications, les outils et les produits.

3. [Inspecter la qualité de la reconnaissance](how-to-custom-speech-inspect-data.md) -utiliser le portail de reconnaissance vocale personnalisé pour lire son chargé et inspecter la qualité de la reconnaissance vocale de vos données de test. Pour les mesures quantitatives, consultez [Inspecter les données](how-to-custom-speech-inspect-data.md).

4. [Évaluer la précision](how-to-custom-speech-evaluate-data.md) -évaluer la précision du modèle de reconnaissance vocale. Le portail de reconnaissance vocale personnalisé fournira un *taux d’erreur Word*, qui peut être utilisé pour déterminer si une formation supplémentaire est requise. Si vous êtes satisfait de la précision, vous pouvez utiliser directement les API de Service de reconnaissance vocale. Si vous souhaitez améliorer la précision par une moyenne relative de 5 à 20 %, utilisez la **formation** onglet dans le portail pour télécharger les données d’apprentissage supplémentaires, telles que des relevés de notes étiquetés humaines et le texte associé.

5. [Apprentissage du modèle](how-to-custom-speech-train-model.md) - améliorer la précision de votre modèle de reconnaissance vocale en fournissant des transcriptions écrites (10-1 000 heures) et le texte connexe (< 200 Mo), ainsi que le contenu audio des données de test. Ces données aident à former le modèle de reconnaissance vocale. Après une formation, testez à nouveau, et si vous êtes satisfait du résultat, vous pouvez déployer votre modèle.

6. [Déployer le modèle](how-to-custom-speech-deploy-model.md) - créer un point de terminaison personnalisé pour votre modèle de reconnaissance vocale et l’utiliser dans vos applications, des outils ou produits.

## <a name="set-up-your-azure-account"></a>Configurer votre compte Azure

Un abonnement de Services de reconnaissance vocale est requis avant de pouvoir utiliser le portail de reconnaissance vocale personnalisé pour créer un modèle personnalisé. Suivez ces instructions pour créer un abonnement aux Services de reconnaissance vocale standard : [Créer un abonnement vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account).

> [!NOTE]
> Veillez à créer des abonnements (S0) standard, les abonnements d’essai gratuit (F0) ne sont pas pris en charge.

Une fois que vous avez créé un compte Azure et un abonnement Services de reconnaissance vocale, vous devez vous connecter au portail de reconnaissance vocale personnalisé et de se connecter à votre abonnement.

1. Obtenir votre clé d’abonnement Services de reconnaissance vocale à partir du portail Azure.
2. Connectez-vous à la [portal de reconnaissance vocale personnalisé](https://aka.ms/custom-speech).
3. Sélectionnez l’abonnement dont vous avez besoin pour travailler sur et créer un projet de reconnaissance vocale.
4. Si vous souhaitez modifier votre abonnement, utilisez le **représentant une roue dentée** icône située dans le volet de navigation supérieur.

## <a name="how-to-create-a-project"></a>Comment créer un projet

Contenu comme points de terminaison, les modèles, les tests et les données sont organisées en **projets** dans le portail de reconnaissance vocale personnalisé. Chaque projet est spécifique à un domaine et un pays/langage. Par exemple, vous pouvez créer un projet pour les centres d’appel qui utilise l’anglais des États-Unis.

Pour créer votre premier projet, sélectionnez le **vocale de la parole-texte/Custom**, puis cliquez sur **nouveau projet**. Suivez les instructions fournies par l’Assistant pour créer votre projet. Une fois que vous avez créé un projet, vous devez voir quatre onglets : **Données**, **test**, **formation**, et **déploiement**. Utilisez les liens fournis dans [étapes suivantes](#next-steps) pour apprendre à utiliser chaque onglet.

## <a name="next-steps"></a>Étapes suivantes

* [Préparer et tester vos données](how-to-custom-speech-test-data.md)
* [Inspectez vos données](how-to-custom-speech-inspect-data.md)
* [Évaluer vos données](how-to-custom-speech-evaluate-data.md)
* [Formation d’un modèle](how-to-custom-speech-train-model.md)
* [Déployer votre modèle](how-to-custom-speech-deploy-model.md)
