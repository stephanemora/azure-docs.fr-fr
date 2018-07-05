---
title: Présentation du service API Visage | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Le glossaire explique les termes que vous êtes susceptible de rencontrer lorsque vous utilisez le service API Visage.
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: fb1d14ff80bf53adc3008d79cc998739ffffde1b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048667"
---
# <a name="what-is-face-api"></a>Qu’est-ce que l’API Visage ?

Bienvenue dans l’API Visage de Microsoft, un service cloud proposant les algorithmes de visage les plus avancés. API Visage a deux fonctions principales : la détection faciale avec des attributs et la reconnaissance faciale.

## <a name="face-detection"></a>Détection de visage

API Visage détecte jusqu'à 64 visages humains en identifiant avec une grande précision la localisation des visages dans une image. L’image peut être spécifiée au moyen d’un fichier en octets ou d’une URL valide.

![Vue d’ensemble - Détection de visage](./Images/Face.detection.jpg)

Le rectangle facial (gauche, haut, largeur et hauteur) qui indique l’emplacement du visage dans l’image est renvoyé avec chaque visage détecté. La détection faciale permet également d’extraire un ensemble d’attributs associés au visage tels que la pose, le sexe, l’âge, la position de la tête, la pilosité du visage et les lunettes. Pour plus d'informations, consultez la section [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-recognition"></a>Reconnaissance faciale

La reconnaissance faciale est largement utilisée dans de nombreux domaines, et notamment la sécurité, l’interface utilisateur naturelle, l’analyse et la gestion du contenu de l’image, les applications mobiles et la robotique. Quatre fonctions de reconnaissance faciale sont proposées : la vérification faciale, la recherche de visages similaires, le regroupement de visages et l’identification des personnes.

### <a name="face-verification"></a>Vérification faciale

La vérification faciale effectue une authentification en comparant deux visages détectés ou un visage détecté et un objet Personne. Pour plus d’informations, consultez la section [Visage - Vérifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

### <a name="finding-similar-face"></a>Recherche de visages similaires

À partir d’un visage détecté cible et d’un ensemble de visages de candidats servant de base de recherche, le service identifie un petit ensemble de visages ressemblant le plus au visage cible. Deux modes de fonctionnement sont pris en charge : `matchFace` et `matchPerson`. Le mode `matchPerson` renvoie les visages similaires après avoir appliqué un seuil de même personne dérivé de [Visage - Vérifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). Le mode `matchFace` ignore le seuil de même personne et renvoie les visages de candidats présentant les meilleures similitudes. Prenons l’exemple suivant qui répertorie les visages de candidats.
![Vue d'ensemble - Rechercher visages similaires](./Images/FaceFindSimilar.Candidates.jpg) Le visage faisant l’objet de la requête est ![Vue d'ensemble - Rechercher visages similaires](./Images/FaceFindSimilar.QueryFace.jpg)

Pour rechercher quatre visages similaires, le mode `matchPerson` renvoie (a) et (b), qui appartiennent à la même personne dont le visage a fait l’objet d’une requête. Le mode `matchFace` renvoie (a), (b), (c) et (d), soit exactement quatre candidats même en cas de faible similarité. Pour plus d'informations, consultez la section [Visage - Rechercher similaires](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

### <a name="face-grouping"></a>Regroupement de visages

À partir d’un ensemble de visages inconnus, l’API de regroupement de visages les sépare automatiquement en plusieurs groupes en fonction du degré de ressemblance. Chaque groupe est un sous-ensemble approprié disjoint de l’ensemble de visages inconnus d’origine et contient des visages similaires. Tous les visages d’un même groupe peuvent être considérés comme appartenant au même objet Personne. Pour plus d'informations, consultez la section [Visage - Regrouper](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

### <a name="face-identification"></a>Identification des visages

API Visage peut être utilisée pour identifier les personnes en se basant sur un visage détecté et une base de données de personnes (définie en tant que LargePersonGroup/PersonGroup). Créez cette base de données à l’avance ; elle pourra ensuite être modifiée au fil du temps.

La figure suivante est un exemple d’un LargePersonGroup/PersonGroup nommé « myfriends ». Chaque groupe peut contenir jusqu’à 1 000 000/10 000 objets Personne. Dans le même temps, chaque objet Personne peut avoir jusqu'à 248 visages enregistrés.

![Vue d’ensemble - LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

Après avoir créé et formé un LargePersonGroup/PersonGroup, il est possible d’effectuer l’identification par rapport au groupe et un nouveau visage détecté. Si le visage est identifié en tant qu’objet Personne du groupe, l’objet Personne est renvoyé.

Pour plus d’informations sur l’identification des personnes, consultez les guides d’API suivants :

[Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)
[PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
[Personne PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
[PersonGroup - Former](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)
[LargePersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)
[Personne LargePersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)
[LargePersonGroup - Former](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)

### <a name="face-storage"></a>Stockage Visage

Stockage Visage permet à un abonnement Standard de stocker des visages persistants supplémentaires lors de l’utilisation d’objets Personne LargePersonGroup/PersonGroup ([Personne PersonGroup - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[Personne LargePersonGroup - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) ou LargeFaceLists/FaceLists ([FaceList - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) à des fins d’identification ou de mise en correspondance des similarités avec l’API Visage. Les images stockées sont facturées au tarif de 0,5 $ tous les 1 000 visages (tarif journalier au prorata). Les abonnements de niveau gratuit sont gratuits mais ils se limitent à un total de 1 000 personnes.

La tarification du service Stockage Visage est calculée au prorata du taux journalier. Par exemple, si votre compte a utilisé 10 000 visages persistants chaque jour pendant la première moitié du mois et aucun visage pendant la seconde moitié, vous êtes facturé uniquement pour les 10 000 visages pendant les jours stockés. De la même façon, si chaque jour du mois, vous rendez 1000 visages persistants pendant quelques heures, puis que vous les supprimez chaque soir, vous continuez à être facturé pour les 1000 visages rendus persistants chaque jour.

## <a name="getting-started-tutorials"></a>Didacticiels de prise en main

Les didacticiels suivants présentent les fonctionnalités de base et les processus d’abonnement de API Visage :

- [Prise en main de API Visage dans le didacticiel CSharp](Tutorials/FaceAPIinCSharpTutorial.md)
- [Prise en main de API Visage dans le didacticiel Java pour Android](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [Prise en main de API Visage dans le didacticiel Python](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="sample-apps"></a>Exemples d’applications

Intéressons-nous à ces exemples d’applications qui utilisent API Visage.

- [Microsoft API Visage : bibliothèque de client Windows et exemple](https://github.com/Microsoft/Cognitive-Face-Windows)
  - Exemple d’application WPF qui présente plusieurs scénarios de détection, d’analyse et d’identification de visages.
- [Application FamilyNotes UWP](https://github.com/Microsoft/Windows-appsample-familynotes)
  - Exemple d’application Universal Windows Platform (UWP) présentant l’utilisation de la reconnaissance vocale, de Cortana, de l’encre et de la caméra au moyen d’un scénario de partage FamilyNotes.
- [Exemple d’analyse des trames vidéo](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - Exemple d’application Universal Windows Platform (UWP) présentant l’analyse des flux vidéo quasiment en temps réel à l’aide des API Visage, Vision par ordinateur et Émotion.

## <a name="related-topics"></a>Rubriques connexes

- [Notes de mise à jour de API Visage version 1.0](ReleaseNotes.md)
- [Comment détecter des visages dans l’image](Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
- [Comment identifier les visages dans l’image](Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)
