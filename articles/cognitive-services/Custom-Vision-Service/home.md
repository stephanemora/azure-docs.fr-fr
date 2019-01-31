---
title: Qu’est-ce que le service Vision personnalisée d’Azure ?
titlesuffix: Azure Cognitive Services
description: Découvrez comment utiliser le service Vision personnalisée pour créer des classifieurs d’images personnalisés dans le cloud Azure.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 01/10/2019
ms.author: anroth
ms.openlocfilehash: 887c2fa09a1108b6254f4dd13b1ca211c80f8f70
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219258"
---
# <a name="what-is-azure-custom-vision"></a>Qu’est-ce que le service Vision personnalisée d’Azure ?

L’API du service Vision personnalisée d’Azure est un service cognitif qui vous permet de créer, déployer et améliorer des classifieurs d’images personnalisés. Un classifieur d’images est un service d’intelligence artificielle qui trie les images en classes (balises) en fonction de certaines caractéristiques. Contrairement au service [Vision par ordinateur](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), le service Vision personnalisée vous permet de créer vos propres classifications.

## <a name="what-it-does"></a>Résultat

Le service Vision personnalisée utilise un algorithme de Machine Learning pour classer les images. Vous, le développeur, devez envoyer des groupes d’images présentant les classifications en question, ainsi que des groupes d’images n’en disposant pas. Vous spécifiez les balises appropriées des images au moment de l’envoi. Ensuite, l’algorithme effectue l’apprentissage avec ces données et calcule sa propre précision en se testant lui-même à l’aide de ces mêmes données. Une fois que le modèle est formé, vous pouvez tester, refaire un apprentissage et enfin l’utiliser pour classer les nouvelles images en fonction des besoins de votre application. Vous pouvez également exporter le modèle en question pour l’utiliser en mode hors connexion.

### <a name="classification-and-object-detection"></a>Classification et détection d’objet

Les fonctionnalités du service Vision personnalisée peuvent être divisées en deux fonctionnalités. La **classification d’images** assigne une distribution des classifications à chaque image. Les modèles de classification multiclasse (une seule balise par image) et multiétiquette (nombre indifférent de balises par image) sont pris en charge. La **détection d’objet** est similaire à la classification multiétiquette, mais elle retourne également les coordonnées dans l’image en fonction d’où se trouvent les étiquettes appliquées.

### <a name="optimization"></a>Optimisation

De manière générale, les méthodes utilisées par le service Vision personnalisée sont peu sensibles aux petites différences, ce qui vous permet de démarrer le prototypage avec très peu de données. Il est généralement bon de commencer avec 50 images par balise. Toutefois, cela signifie que le service n’est pas optimal pour la détection des différences subtiles dans les images (par exemple, la détection de petites fissures ou de petits trous dans des scénarios d’assurance qualité).

En outre, vous pouvez choisir parmi plusieurs types d’algorithme de Vision personnalisée qui sont optimisés pour certains sujets&mdash;par exemple, des points de repère ou des articles vendus au détail. Pour plus d’informations, consultez le document [Créer un classifieur](getting-started-build-a-classifier.md).

## <a name="what-it-includes"></a>Contenu
Le service Vision personnalisée est disponible en tant qu’ensemble de kits de développement logiciel (SDK) natifs, ainsi que via une interface web sur la [page d’accueil du service Vision personnalisée](https://customvision.ai/). Vous pouvez créer, tester et former un modèle via une des interfaces, ou avec les deux.

![Page d’accueil du service Vision personnalisée dans une fenêtre du navigateur Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

Comme avec tous les services Cognitive Services, les développeurs utilisant le service Vision personnalisée doivent connaître les politiques de Microsoft relatives aux données client. Pour en savoir plus, consultez la [page Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) dans le Centre de gestion de la confidentialité Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Suivez le guide [Créer un classifieur](getting-started-build-a-classifier.md) pour découvrir comment utiliser le service Vision personnalisée sur le web. Sinon, vous pouvez terminer le [didacticiel de classification d’Image](csharp-tutorial.md) pour implémenter le scénario en code.
