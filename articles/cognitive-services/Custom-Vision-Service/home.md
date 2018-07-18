---
title: Vue d’ensemble de l’apprentissage automatique du service Vision personnalisée - Azure Cognitive Services | Microsoft Docs
description: Le service Vision personnalisée est un service Microsoft Cognitive Services qui vous permet de créer des classifieurs d’images personnalisés sur la plateforme Azure.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: de45fc399470a806fb7456cbbe936cecf659ee7c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37087685"
---
# <a name="what-is-the-custom-vision-service"></a>Qu’est-ce que le service Vision personnalisée ?

Le service Vision personnalisée est un service Microsoft Cognitive Services qui vous permet de créer des classifieurs d’images personnalisés. Il permet de créer, déployer et améliorer facilement et rapidement un classifieur d’images. Le service Vision personnalisée fournit une API REST et une interface web pour charger vos images et entraîner le classifieur.

## <a name="what-does-custom-vision-service-do-well"></a>Pour quelles tâches le service Vision personnalisée est-il performant ?

Le service Vision personnalisée fonctionne mieux quand l’élément que vous essayez de classifier est bien visible dans votre image. 

Quelques images seulement sont nécessaires pour créer un classifieur ou un détecteur. 50 images par classe suffisent pour démarrer votre prototype. Les méthodes utilisées par le service Vision personnalisée sont peu sensibles aux petites différences, ce qui vous permet de démarrer le prototypage avec très peu de données. Cela signifie que le service Vision personnalisée n’est pas adapté aux scénarios où vous voulez détecter des différences subtiles, par exemple dans le cas de petites fissures ou de petits trous dans des scénarios d’assurance qualité.

## <a name="release-notes"></a>Notes de publication

### <a name="may-7-2018"></a>7 mai 2018
- Introduction de la fonctionnalité de détection d’objets en préversion pour les projets de la version d’essai limitée.
- Mise à niveau vers les API 2.0
- Le niveau S0 a été étendu jusqu’à 250 étiquettes et à 50 000 images. 
- Améliorations significatives de backend pour le pipeline d’apprentissage automatique pour les projets de classification d’images. Les projets entraînés après le 27 avril 2018 bénéficient de ces mises à jour.
- Ajout de l’exportation de modèles vers ONNX, pour une utilisation avec Windows ML.
- Ajout de l’exportation de modèles vers Dockerfile. Ceci vous permet de télécharger les artefacts pour générer vos propres conteneurs Windows ou Linux, notamment un fichier DockerFile, un modèle TensorFlow et un code de service. 
- Pour les modèles entraînés récemment et exportés vers TensorFlow dans les domaines Général (Compact) et Repères géographiques (Compact), [les valeurs moyennes sont maintenant (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample) pour des raisons de cohérence entre tous les projets. 

### <a name="march-1-2018"></a>1er mars 2018
- Préversion payante entrée et intégrée sur le portail Azure. Les projets peuvent désormais être attachés à des ressources Azure avec un niveau F0 (Gratuit) ou S0 (Standard). Introduction des projets de niveau S0, qui autorisent jusqu’à 100 étiquettes et 25 000 images. 
- Modification du backend concernant le paramètre de normalisation/le pipeline d’apprentissage automatique. Ceci donne aux clients un meilleur contrôle des compromis précision/rappel lors de l’ajustement du seuil de probabilité. Dans le cadre de ces modifications, le seuil de probabilité par défaut dans le portail CustomVision.ai a été défini sur 50 %.

### <a name="december-19-2017"></a>19 décembre 2017

- Ajout de l’exportation vers Android (TensorFlow), en plus de l’exportation déjà publiée vers iOS (CoreML.) Ceci permet d’exécuter hors connexion l’exportation d’un modèle compact entraîné dans une application.
- Ajout des domaines « compacts » Distribution et Repères géographiques pour permettre l’exportation de modèles pour ces domaines.
- Publication de [l’API d’apprentissage version 1.2](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) et de [l’API de prédiction version 1.1](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Les API mises à jour prennent en charge l’exportation de modèles, un nouveau fonctionnement de la prédiction qui n’enregistre pas les images dans « Prédictions » et introduisent des opérations par lots pour l’API d’apprentissage.
- Ajustements de l’expérience utilisateur, notamment la possibilité de voir le domaine qui a été utilisé pour entraîner une itération.
- Mise à jour du [SDK et de l’exemple C#](https://github.com/Microsoft/Cognitive-CustomVision-Windows).

## <a name="next-steps"></a>Étapes suivantes

[Découvrir comment créer un classifieur](getting-started-build-a-classifier.md)
