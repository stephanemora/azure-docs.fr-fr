---
title: Notes de publication du service Vision personnalisée
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: 2b0d8b8a86c3105b1bda7fb0d72cbcb72ed82995
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785723"
---
# <a name="custom-vision-service-release-notes"></a>Notes de publication du service Vision personnalisée

## <a name="march-26-2019"></a>26 mars 2019

- Le Service Vision personnalisée a entré la disponibilité générale sur Azure !
- Fonctionnalité avancée de formation ajoutée avec un nouveau back-end pour améliorer les performances, en particulier sur les jeux de données complexes et une classification précise d’apprentissage. Avec une formation avancée, vous pouvez spécifier qu'un budget de temps de calcul pour l’apprentissage et de Vision personnalisée expérimentale identifie les meilleurs paramètres de formation et d’augmentation. Pour les itérations rapides, vous pouvez continuer à utiliser l’apprentissage rapide existant.
- Introduit 3.0 API. Annoncé prochaine dépréciation des API de pre-3.0 sur le 1 octobre 2019. Consultez les Démarrages rapides de documentation pour [.Net](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial), [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [nœud](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial), ou [accédez](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) pour obtenir des exemples sur la prise en main.
- Remplacé « Par défaut itérations » avec Publier/Annuler la publication de 3.0 API.
- Nouvelles cibles d’exportation de modèle ont été ajoutées. Exportation de fichier Dockerfile a été mis à niveau pour prendre en charge ARM pour Raspberry Pi 3. Prise en charge de l’exportation a été ajouté à la [Kit de développement IA Vision.](https://visionaidevkit.com/).
- Augmentation de la limite de balises par projet et 500 pour le niveau S0. Augmentation de la limite des Images de chaque projet à 100 000 pour le niveau S0.
- Domaine réservé aux adultes supprimés. Domaine général est recommandée à la place.
- Annoncé [tarification](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) pour la disponibilité générale.  

## <a name="february-25-2019"></a>25 février 2019

- La fin de la version d’évaluation limitée projets (non associé à une ressource Azure), annoncée comme Vision personnalisée effectuée de sa migration vers Azure en version préliminaire publique. Depuis le 25 mars 2019, le site CustomVision.ai sera uniquement prennent en charge les projets d’affichage associés à une ressource Azure, telles que la ressource de Vision personnalisée gratuite. Via le 1 octobre 2019, vous serez toujours en mesure d’accéder à vos projets d’évaluation limités existantes via les API de Vision personnalisée. Cela vous laisse le temps aux clés API de mise à jour de toutes les applications que vous avez écrit avec Vision personnalisée. Après le 1 octobre 2019, tous les projets d’évaluation limitées, vous n’avez pas déplacées vers Azure seront supprimés.

## <a name="january-22-2019"></a>22 janvier 2019

- Prise en charge ajoutée pour de nouvelles régions Azure : USA Ouest 2, USA Est, USA Est 2, Europe Ouest, Europe Nord, Asie Sud-Est, Australie Est, Inde Centre, Royaume-Uni Sud, Japon Est et USA Centre Nord. Prise en charge continue pour USA Centre Sud.

## <a name="december-12-2018"></a>12 décembre 2018

- Prise en charge de l’exportation pour les modèles de détection d’objets (introduction du domaine compact de détection d’objets).
- Correction de plusieurs problèmes d’accessibilité afin d’améliorer la prise en charge de la navigation au clavier et du lecteur d’écran.
- Mises à jour de l’expérience utilisateur de la visionneuse d’images et amélioration de l’expérience de détection des objets afin d’en accélérer l’identification.  
- Mise à jour du modèle de base pour le domaine de détection d’objets, afin d’améliorer la qualité de la détection des objets.
- Correctifs de bogues.

## <a name="november-6-2018"></a>6 novembre 2018

- Ajout de la prise en charge du domaine de logo dans la détection d’objets.

## <a name="october-9-2018"></a>9 octobre 2018

- La détection d’objets passe en préversion payante. Vous pouvez désormais créer des projets de détection d’objets avec une ressource Azure.
- Ajout de la fonctionnalité « Déplacer vers Azure » au site web pour faciliter la mise à niveau d’un projet d’évaluation limitée à lier à un projet de ressources Azure lié (F0 ou S0). Vous pouvez la trouver sur la page Paramètres de votre produit.  
- Ajout de l’exportation vers ONNX 1.2 pour prendre en charge la mise à jour Windows d’octobre 2018 de ML Windows.
Correctifs de bogues, notamment pour l’exportation ONNX avec des caractères spéciaux.

## <a name="august-14-2018"></a>14 août 2018

- Ajout du widget « Mise en route » au site customvision.ai pour guider les utilisateurs dans l’entraînement du projet.
- Autres améliorations apportées au pipeline d’apprentissage automatique pour bénéficier des projets à plusieurs étiquettes (nouvelle couche de perte).

## <a name="june-28-2018"></a>28 juin 2018

- Correctifs de bogues et améliorations du serveur principal.
- Activé la classification multiclasse, pour les projets où les images ont exactement une étiquette. Dans les prédictions pour le mode multiclasse, probabilités principales résume une (toutes les images sont classées parmi vos balises spécifiés).

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
- Pour les modèles formés récemment et exportés vers TensorFlow dans les domaines Général (Compact) et Repères géographiques (Compact), [les valeurs moyennes sont maintenant (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample) pour des raisons de cohérence entre tous les projets.

## <a name="march-1-2018"></a>1er mars 2018

- Version préliminaire payante entré et intégrées sur le portail Azure. Les projets peuvent désormais être attachés à des ressources Azure avec un niveau F0 (Gratuit) ou S0 (Standard). Introduction des projets de niveau S0, qui autorisent jusqu’à 100 étiquettes et 25 000 images.
- Modification du backend concernant le paramètre de normalisation/le pipeline d’apprentissage automatique. Ceci donne aux clients un meilleur contrôle des compromis précision/rappel lors de l’ajustement du seuil de probabilité. Dans le cadre de ces modifications, le seuil de probabilité par défaut dans le portail CustomVision.ai a été défini sur 50 %.

## <a name="december-19-2017"></a>19 décembre 2017

- Ajout de l’exportation vers Android (TensorFlow), en plus de l’exportation déjà publiée vers iOS (CoreML.) Ceci permet d’exécuter hors connexion l’exportation d’un modèle compact entraîné dans une application.
- Ajout des domaines « compacts » Distribution et Repères géographiques pour permettre l’exportation de modèles pour ces domaines.
- Publication de [l’API d’apprentissage version 1.2](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) et de [l’API de prédiction version 1.1](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Les API mises à jour prennent en charge l’exportation de modèles, un nouveau fonctionnement de la prédiction qui n’enregistre pas les images dans « Prédictions » et introduisent des opérations par lots pour l’API d’apprentissage.
- Ajustements de l’expérience utilisateur, notamment la possibilité de voir le domaine qui a été utilisé pour entraîner une itération.
- Mise à jour du [SDK et de l’exemple C#](https://github.com/Microsoft/Cognitive-CustomVision-Windows).