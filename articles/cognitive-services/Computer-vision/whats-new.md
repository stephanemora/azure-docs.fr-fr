---
title: Nouveautés de Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Cet article présente les nouveautés de Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 05/24/2021
ms.author: pafarley
ms.openlocfilehash: 395b9d2670a488ab0743a987b1e8b857f6f2fb2f
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708321"
---
# <a name="whats-new-in-computer-vision"></a>Nouveautés de Vision par ordinateur

Découvrir les nouveautés du service. Ces éléments peuvent être des notes de publication, des vidéos, des billets de blog et d’autres types d’informations. Marquez cette page pour rester informé des nouveautés du service.

## <a name="september-2021"></a>Septembre 2021

### <a name="ocr-read-api-public-preview-supports-122-languages"></a>La préversion publique de l’API OCR (Read) prend en charge 122 langues
L’[API OCR (Read)](overview-ocr.md) de Vision par ordinateur étend les [langues prises en charge](language-support.md) à 122 dans sa dernière préversion :
* Prise en charge par OCR du texte imprimé dans 49 nouvelles langues, dont le russe, le bulgare et d’autres langues cyrilliques et latines.
* Prise en charge par OCR du texte manuscrit dans 6 nouvelles langues, dont l’anglais, le chinois simplifié, le français, l’allemand, l’italien, le portugais et l’espagnol.
* Améliorations du traitement des fichiers PDF numériques et du texte Machine Readable Zone (MRZ) dans les documents d’identité.
* Améliorations de la qualité générale des performances et de l’intelligence artificielle

Consultez le [Guide de procédures OCR](Vision-API-How-to-Topics/call-read-api.md#determine-how-to-process-the-data-optional) pour apprendre à utiliser les nouvelles fonctionnalités en préversion.

> [!div class="nextstepaction"]
> [Bien démarrer avec l’API Read](./quickstarts-sdk/client-library.md)


## <a name="august-2021"></a>Août 2021

### <a name="image-tagging-language-expansion"></a>Extension des langues de marquage d’image

La [dernière version (v3.2)](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f200) du créateur d’étiquettes d’image prend désormais en charge les étiquettes dans les 50 langues. Pour plus d’informations, consultez la page [Prise en charge linguistique](language-support.md).

## <a name="may-2021"></a>Mai 2021

### <a name="spatial-analysis-container-update"></a>Mise à jour du conteneur Analyse spatiale

Une nouvelle version du [conteneur Analyse spatiale](spatial-analysis-container.md) a été publiée avec un nouvel ensemble de fonctionnalités. Ce conteneur Docker vous permet d’analyser des vidéos en streaming en temps réel afin de comprendre les relations spatiales entre les personnes et leurs déplacements dans des environnements physiques. 

* Les [opérations d’analyse spatiale](spatial-analysis-operations.md) peuvent désormais être configurées pour détecter l’orientation à laquelle une personne fait face. 
    * Un classifieur d’orientation peut être activé pour les opérations `personcrossingline` et `personcrossingpolygon` en configurant le paramètre `enable_orientation`. Il est défini par défaut sur désactivé.

* Les [opérations d’analyse spatiale](spatial-analysis-operations.md) proposent également maintenant une configuration pour détecter la vitesse d’une personne lorsqu’elle marche ou qu’elle court.
     * La vitesse peut être détectée pour les opérations `personcrossingline` et `personcrossingpolygon` en activant le classifieur `enable_speed`, qui est désactivé par défaut. Le résultat est reflété dans les sorties `speed`, `avgSpeed` et `minSpeed`.


## <a name="april-2021"></a>Avril 2021

### <a name="computer-vision-v32-ga"></a>Vision par ordinateur v3.2 en disponibilité générale

L’API Vision par ordinateur v3.2 est maintenant en disponibilité générale avec les mises à jour suivantes :
* Modèle d’étiquetage d’image amélioré : analyse le contenu visuel et génère des étiquettes pertinentes en fonction des objets, des actions et du contenu affichés dans l’image. Ce modèle est disponible par le biais de l’[API Étiqueter l’image](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f200). Pour en savoir plus, consultez le [guide pratique](./vision-api-how-to-topics/howtocallvisionapi.md) et la [vue d’ensemble](./overview-image-analysis.md) de l’Analyse d’images.
* Modèle de modération du contenu mis à jour : détecte la présence de contenu pour adultes et fournit des indicateurs permettant de filtrer les images qui contiennent du contenu visuel pour adultes, osé et choquant. Ce modèle est disponible par le biais de l’[API Analyser](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b). Pour en savoir plus, consultez le [guide pratique](./vision-api-how-to-topics/howtocallvisionapi.md) et la [vue d’ensemble](./overview-image-analysis.md) de l’Analyse d’images.
* [OCR (lecture) disponible pour 73 langues](./language-support.md#optical-character-recognition-ocr), dont le chinois simplifié, le chinois traditionnel, le japonais, le coréen et le latin.
* [OCR (lecture)](./overview-ocr.md) également disponible en tant que [conteneur Distroless](./computer-vision-how-to-install-containers.md?tabs=version-3-2) pour un déploiement local.

> [!div class="nextstepaction"]
> [Consulter Vision par ordinateur v3.2 en disponibilité générale](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)

## <a name="march-2021"></a>Mars 2021

### <a name="computer-vision-32-public-preview-update"></a>Mise à jour de la préversion publique de Vision par ordinateur 3.2

La préversion publique de l’API Vision par ordinateur v3.2 a été mise à jour. La préversion offre toutes les fonctionnalités de Vision par ordinateur, ainsi que les versions mises à jour des API Lire et Analyser.

> [!div class="nextstepaction"]
> [Consulter la préversion publique 3 de Vision par ordinateur v3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)

## <a name="february-2021"></a>Février 2021

### <a name="read-api-v32-public-preview-with-ocr-support-for-73-languages"></a>Préversion publique de l’API Lire v3.2 avec prise en charge de la reconnaissance optique de caractères (OCR) pour 73 langues
La préversion publique de l’API Lire v3.2 de Vision par ordinateur, disponible en tant que service cloud et conteneur Docker, comprend les mises à jour suivantes :
* [OCR pour 73 langues](./language-support.md#optical-character-recognition-ocr) y compris le chinois simplifié, le chinois traditionnel, le japonais, le coréen et le latin.
* Ordre de lecture naturel pour la sortie des lignes de texte (langues latines uniquement)
* Classification du style d’écriture manuscrite pour les lignes de texte avec un score de confiance (langues latines uniquement).
* Extrayez uniquement le texte des pages sélectionnées pour un document multipage.
* Disponible en tant que [conteneur Distroless](./computer-vision-how-to-install-containers.md?tabs=version-3-2) pour un déploiement local.

Pour en savoir plus, consultez le [guide pratique consacré à l'API Read](Vision-API-How-to-Topics/call-read-api.md).

> [!div class="nextstepaction"]
> [Utiliser la préversion publique de l’API Lire v 3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)


## <a name="january-2021"></a>Janvier 2021

### <a name="spatial-analysis-container-update"></a>Mise à jour du conteneur Analyse spatiale

Une nouvelle version du [conteneur Analyse spatiale](spatial-analysis-container.md) a été publiée avec un nouvel ensemble de fonctionnalités. Ce conteneur Docker vous permet d’analyser des vidéos en streaming en temps réel afin de comprendre les relations spatiales entre les personnes et leurs déplacements dans des environnements physiques. 

* Des [opérations d'analyse spatiale](spatial-analysis-operations.md) peuvent maintenant être configurées pour détecter si une personne porte un appareil de protection respiratoire comme un masque. 
    * Un classifieur de masque peut être activé pour les opérations `personcount`, `personcrossingline` et `personcrossingpolygon` en configurant le paramètre `ENABLE_FACE_MASK_CLASSIFIER`.
    * Les attributs `face_mask` et `face_noMask` sont retournés en tant que métadonnées avec un score de confiance pour chaque personne détectée dans le flux vidéo.
* L’opération *personcrossingpolygon* a été étendue pour permettre le calcul du temps de séjour d’une personne dans une zone. Vous pouvez définir le paramètre `type` dans la configuration de zone de l’opération sur `zonedwelltime`. Un nouvel événement de type *personZoneDwellTimeEvent* inclut alors le champ `durationMs` complété avec le nombre de millisecondes que la personne a passé dans la zone.
* **Changement cassant** : l’événement *personZoneEvent* a été renommé *personZoneEnterExitEvent*. Cet événement est déclenché par l’opération *personcrossingpolygon* quand une personne entre dans la zone ou la quitte et fournit des informations directionnelles avec le côté numéroté de la zone qui a été franchie.
* L’URL de la vidéo peut être fournie en tant que « Paramètre privé/obscurci » dans toutes les opérations. L’obscurcissement est actuellement facultatif et ne fonctionne que si `KEY` et `IV` sont fournis en tant que variables d’environnement.
* L’étalonnage est activé par défaut pour toutes les opérations. Définissez `do_calibration: false` pour le désactiver.
* Ajout de la prise en charge du réétalonnage automatique (désactivé par défaut) via le paramètre `enable_recalibration`. Pour plus d'informations, consultez [Opérations d'analyse spatiale](./spatial-analysis-operations.md).
* Paramètres d’étalonnage de caméra sur `DETECTOR_NODE_CONFIG`. Pour plus d'informations, consultez [Opérations d'analyse spatiale](./spatial-analysis-operations.md).


## <a name="october-2020"></a>Octobre 2020

### <a name="computer-vision-api-v31-ga"></a>API Vision par ordinateur v3.1 en disponibilité générale

L’API Vision par ordinateur en disponibilité générale a été mise à niveau vers la version 3.1.

## <a name="september-2020"></a>Septembre 2020

### <a name="spatial-analysis-container-preview"></a>Conteneur Analyse spatiale en préversion

Le [conteneur Analyse spatiale](spatial-analysis-container.md) est désormais disponible en préversion. La fonctionnalité Analyse spatiale de Vision par ordinateur vous permet d’analyser les vidéos en streaming en temps réel, afin d’analyser les relations spatiales entre les personnes et leurs déplacements dans des environnements physiques. Analyse spatiale est un conteneur Docker que vous pouvez utiliser localement. 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>La préversion publique de l’API Lire v3.1 ajoute la reconnaissance optique de caractères (OCR) pour le japonais
La préversion publique de l’API Lire v3.1 de Vision par ordinateur ajoute les fonctionnalités suivantes :
* Reconnaissance optique de caractères (OCR) pour le japonais
* Pour chaque ligne de texte, indiquez si l’apparence correspond à un style d’écriture manuscrite ou d’impression, ainsi qu’un score de confiance (langues latines uniquement).
* Pour un document multipage, extrayez uniquement le texte des pages sélectionnées ou de la plage de pages.

* Cette préversion de l’API Lire prend en charge le français, l’allemand, l’anglais, le chinois simplifié, l’espagnol, l’italien, le japonais, le néerlandais et le portugais.

Pour en savoir plus, consultez le [guide pratique consacré à l'API Read](Vision-API-How-to-Topics/call-read-api.md).

> [!div class="nextstepaction"]
> [En savoir plus sur la préversion publique 2 de l’API Lire v3.1](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>Juillet 2020

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Préversion publique de l’API Lire v3.1 avec reconnaissance optique de caractères (OCR) pour le chinois simplifié
La prise en charge du chinois simplifié est ajoutée à la préversion publique de l’API de lecture v3.1 de Vision par ordinateur.

* Cette préversion de l’API de lecture prend en charge l’anglais, le néerlandais, le français, l’allemand, l’italien, le portugais, le chinois simplifié et l’espagnol.

Pour en savoir plus, consultez le [guide pratique consacré à l'API Read](Vision-API-How-to-Topics/call-read-api.md).

> [!div class="nextstepaction"]
> [En savoir plus sur la préversion publique 1 de l’API Lire v3.1](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>Mai 2020
L'API Vision par ordinateur v3.0 est en phase de disponibilité générale, avec des mises à jour de l'API Read :

* Prise en charge de l’anglais, du néerlandais, du français, de l’allemand, de l’italien, du portugais et de l’espagnol
* Précision accrue
* Score de confiance pour chaque mot extrait
* Nouveau format de sortie

Pour en savoir plus, consultez la [Présentation de la Reconnaissance optique de caractères (OCR)](overview-ocr.md).

## <a name="march-2020"></a>Mars 2020

* La sécurité TLS 1.2 est maintenant appliquée pour toutes les requêtes HTTP adressées à ce service. Pour plus d'informations, consultez [Sécurité Azure Cognitive Services](../cognitive-services-security.md).

## <a name="january-2020"></a>Janvier 2020

### <a name="read-api-30-public-preview"></a>Préversion publique de l’API de lecture 3.0

Vous avez maintenant utiliser la version 3.0 de l’API Lire pour extraire du texte imprimé ou manuscrit à partir d’images. Par rapport aux versions antérieures, 3.0 fournit :
* Précision accrue
* Nouveau format de sortie
* Score de confiance pour chaque mot extrait
* Prise en charge de l’espagnol et de l’anglais avec le paramètre de langue

Suivez [Démarrage rapide : Extraire du texte](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-hand-text.md?tabs=version-3) pour commencer à utiliser l’API 3.0.

## <a name="cognitive-service-updates"></a>Mises à jour du service cognitif

[Annonces de mise à jour Azure pour Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
