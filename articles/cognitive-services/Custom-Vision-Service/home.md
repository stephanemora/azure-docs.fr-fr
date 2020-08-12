---
title: Qu’est-ce que Custom Vision ?
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser le service Vision personnalisée pour créer des classifieurs d’images personnalisés dans le cloud Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 86e2d54cfe7559675e5e878cbc8a0df4b4eb3d0b
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87834284"
---
# <a name="what-is-custom-vision"></a>Qu’est-ce que Custom Vision ?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Custom Vision est un service cognitif qui vous permet de créer, de déployer et d’améliorer vos propres classifieurs d’images. Un classifieur d’images est un service d’intelligence artificielle qui applique des étiquettes (représentant des _classes_) à des images en fonction de leurs caractéristiques visuelles. Contrairement au service [Vision par ordinateur](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), le service Custom Vision vous permet de spécifier les étiquettes à appliquer.

## <a name="what-it-does"></a>Résultat

Le service Custom Vision utilise un algorithme de Machine Learning pour appliquer des étiquettes à des images. Vous, en tant que développeur, devez envoyer des groupes d’images présentant les caractéristiques en question, ainsi que des groupes d’images n’en disposant pas. Vous étiquetez les images vous-même au moment de la soumission. L’algorithme s’entraîne ensuite avec ces données et calcule sa propre précision en se testant lui-même à l’aide de ces mêmes images. Une fois l’algorithme entraîné, vous pouvez le tester, le réentraîner et éventuellement l’utiliser pour classer les nouvelles images en fonction des besoins de votre application. Vous pouvez également exporter le modèle en question pour l’utiliser en mode hors connexion.

### <a name="classification-and-object-detection"></a>Classification et détection d’objet

Les fonctionnalités du service Vision personnalisée peuvent être divisées en deux fonctionnalités. La **classification d’images** applique une ou plusieurs étiquettes à une image. La **détection d’objet** est similaire, mais elle retourne également les coordonnées dans l’image où se trouvent la ou les étiquettes appliquées.

### <a name="optimization"></a>Optimization

Le service Custom Vision est optimisé pour reconnaître rapidement les principales différences entre les images. Vous pouvez ainsi commencer à prototyper votre modèle avec une petite quantité de données. Il est généralement conseillé de commencer avec 50 images par étiquette. Toutefois, le service n’est pas optimal pour la détection de différences subtiles au sein des images (par exemple, la détection de petites fissures ou de petits trous dans des scénarios d’assurance qualité).

En outre, vous pouvez choisir parmi plusieurs types d’algorithme de Custom Vision qui sont optimisés pour certains sujets (par exemple, des points de repère ou des articles vendus au détail). Pour plus d’informations, consultez le guide [Créer un classifieur](getting-started-build-a-classifier.md).

## <a name="what-it-includes"></a>Contenu

Le service Vision personnalisée est disponible en tant qu’ensemble de kits de développement logiciel (SDK) natifs, ainsi que via une interface web sur la [page d’accueil du service Vision personnalisée](https://customvision.ai/). Vous pouvez créer, tester et entraîner un modèle avec une interface ou les deux.

![Page d’accueil du service Vision personnalisée dans une fenêtre du navigateur Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

Comme avec tous les services Cognitive Services, les développeurs utilisant le service Vision personnalisée doivent connaître les politiques de Microsoft relatives aux données client. Pour en savoir plus, consultez la [page Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) dans le Centre de gestion de la confidentialité Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Suivez le guide [Créer un classifieur](getting-started-build-a-classifier.md) pour découvrir comment utiliser le service Custom Vision sur le web. Sinon, vous pouvez terminer le [tutoriel de classification d’images](quickstarts/image-classification.md) pour implémenter un scénario de base dans le code.
