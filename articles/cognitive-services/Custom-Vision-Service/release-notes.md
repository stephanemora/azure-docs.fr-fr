---
title: Notes de publication du service Vision personnalisée
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.date: 08/28/2018
ms.author: anroth
ms.openlocfilehash: 8423051fa5169eb8acddc7297e36188e297cb9e4
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43288650"
---
# <a name="custom-vision-service-release-notes"></a>Notes de publication du service Vision personnalisée

## <a name="august-14-2018"></a>14 août 2018
- Ajout du widget « Mise en route » au site customvision.ai pour guider les utilisateurs dans l’entraînement du projet. 
- Autres améliorations apportées au pipeline d’apprentissage automatique pour bénéficier des projets à plusieurs étiquettes (nouvelle couche de perte).

## <a name="june-28-2018"></a>28 juin 2018
- Résolution des bogues et améliorations de backend.
- Activation de la classification multiclasse, pour les projets où les images ont exactement une étiquette. Dans les prédictions pour le mode multiclasse, les probabilités atteignent une somme égale à un (toutes les images sont classées parmi vos balises spécifiées).

## <a name="june-13-2018"></a>13 juin 2018
- Actualisation de l’expérience utilisateur, axée sur la facilité d’utilisation et l’accessibilité. 
- Améliorations apportées au pipeline d’apprentissage automatique pour bénéficier des projets à plusieurs étiquettes avec un grand nombre de balises.
- Correction du bogue dans l’exportation TensorFlow. Activation de la gestion des versions des modèles exportés afin que les itérations puissent être exportées plusieurs fois. 

## <a name="may-7-2018"></a>7 mai 2018
- Introduction de la fonctionnalité de détection d’objets en préversion pour les projets de la version d’essai limitée.
- Mise à niveau vers les API 2.0
- Le niveau S0 a été étendu jusqu’à 250 étiquettes et à 50 000 images. 
- Améliorations significatives de backend pour le pipeline d’apprentissage automatique pour les projets de classification d’images. Les projets entraînés après le 27 avril 2018 bénéficient de ces mises à jour.
- Ajout de l’exportation de modèles vers ONNX, pour une utilisation avec Windows ML.
- Ajout de l’exportation de modèles vers Dockerfile. Ceci vous permet de télécharger les artefacts pour générer vos propres conteneurs Windows ou Linux, notamment un fichier DockerFile, un modèle TensorFlow et un code de service. 
- Pour les modèles entraînés récemment et exportés vers TensorFlow dans les domaines Général (Compact) et Repères géographiques (Compact), [les valeurs moyennes sont maintenant (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample) pour des raisons de cohérence entre tous les projets. 

## <a name="march-1-2018"></a>1er mars 2018
- Préversion payante entrée et intégrée sur le portail Azure. Les projets peuvent désormais être attachés à des ressources Azure avec un niveau F0 (Gratuit) ou S0 (Standard). Introduction des projets de niveau S0, qui autorisent jusqu’à 100 étiquettes et 25 000 images. 
- Modification du backend concernant le paramètre de normalisation/le pipeline d’apprentissage automatique. Ceci donne aux clients un meilleur contrôle des compromis précision/rappel lors de l’ajustement du seuil de probabilité. Dans le cadre de ces modifications, le seuil de probabilité par défaut dans le portail CustomVision.ai a été défini sur 50 %.

## <a name="december-19-2017"></a>19 décembre 2017

- Ajout de l’exportation vers Android (TensorFlow), en plus de l’exportation déjà publiée vers iOS (CoreML.) Ceci permet d’exécuter hors connexion l’exportation d’un modèle compact entraîné dans une application.
- Ajout des domaines « compacts » Distribution et Repères géographiques pour permettre l’exportation de modèles pour ces domaines.
- Publication de [l’API d’apprentissage version 1.2](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) et de [l’API de prédiction version 1.1](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Les API mises à jour prennent en charge l’exportation de modèles, un nouveau fonctionnement de la prédiction qui n’enregistre pas les images dans « Prédictions » et introduisent des opérations par lots pour l’API d’apprentissage.
- Ajustements de l’expérience utilisateur, notamment la possibilité de voir le domaine qui a été utilisé pour entraîner une itération.
- Mise à jour du [SDK et de l’exemple C#](https://github.com/Microsoft/Cognitive-CustomVision-Windows).

