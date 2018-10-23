---
title: Qu’est ce que le service API Visage ?
titleSuffix: Azure Cognitive Services
description: Cette rubrique explique comment fonctionne le service API Visage et les termes associés.
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 10/11/2018
ms.author: sbowles
ms.openlocfilehash: 6c1e0d0a51bc01c581c05e7ce3215f5501b4be76
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310355"
---
# <a name="what-is-the-face-api-service"></a>Qu’est ce que le service API Visage ?

Le service API Visage est un service informatique qui fournit des algorithmes pour analyser les visages humains dans des images et des vidéos. L’API Visage a deux fonctions principales : la détection faciale avec des attributs et la reconnaissance faciale.

## <a name="face-detection"></a>Détection des visages

L’API Visage peut détecter jusqu’à 64 visages humains en identifiant avec une grande précision la localisation des visages dans une image. L’image peut être spécifiée par fichier (un flux d’octets) ou avec une URL valide.

![Vue d’ensemble - Détection de visage](./Images/Face.detection.jpg)

Le rectangle facial (gauche, haut, largeur et hauteur) qui indique l’emplacement du visage dans l’image est renvoyé avec chaque visage détecté. La détection faciale permet également d’extraire un ensemble d’attributs associés au visage tels que la pose, le sexe, l’âge, la position de la tête, la pilosité du visage et les lunettes. Pour plus d'informations, consultez la section [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-recognition"></a>Reconnaissance faciale

Pouvoir identifier les visages humains est important pour de nombreux domaines, notamment la sécurité, l’interface utilisateur naturelle, l’analyse et la gestion du contenu de l’image, les applications mobiles et la robotique. Le service API Visage propose quatre fonctions de reconnaissance faciale : la vérification faciale, la recherche de visages similaires, le regroupement de visages et l’identification des personnes.

### <a name="face-verification"></a>Vérification faciale

La vérification faciale effectue une authentification en comparant deux visages détectés ou un visage détecté et un objet Personne. Pour plus d’informations, consultez la section [Visage - Vérifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

### <a name="finding-similar-faces"></a>Recherche de visages similaires

À partir d’un visage détecté cible et d’un ensemble de visages de candidats servant de base de recherche, le service identifie un petit ensemble de visages ressemblant le plus au visage cible. Deux modes de travail, **matchFace** et **matchPerson**, sont pris en charge. Le mode **matchPerson** renvoie les visages similaires après avoir appliqué un seuil de même personne dérivé de [Visage - Vérifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). Le mode **matchFace** ignore le seuil de même personne et renvoie les visages de candidats présentant les meilleures similitudes. Prenons l’exemple suivant qui répertorie les visages de candidats.
![Vue d’ensemble - Rechercher visages similaires](./Images/FaceFindSimilar.Candidates.jpg) Voici le visage faisant l’objet de la requête.
![Vue d’ensemble - Rechercher visages similaires](./Images/FaceFindSimilar.QueryFace.jpg)

Pour rechercher quatre visages similaires, le mode **matchPerson** renvoie (a) et (b), qui représentent le même visage que celui faisant l’objet de la requête. Le mode **matchFace** renvoie (a), (b), (c) et (d), soit exactement quatre candidats même si certains ont une faible similarité. Pour plus d'informations, consultez la section [Visage - Rechercher similaires](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

### <a name="face-grouping"></a>Regroupement de visages

À partir d’un ensemble de visages inconnus, l’API de regroupement de visages les sépare automatiquement en plusieurs groupes en fonction du degré de ressemblance. Chaque groupe est un sous-ensemble approprié disjoint de l’ensemble de visages inconnus d’origine et contient des visages similaires. Tous les visages d’un même groupe peuvent être considérés comme appartenant à la même personne. Pour plus d'informations, consultez la section [Visage - Regrouper](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

### <a name="person-identification"></a>Identification de personnes

L’API Visage peut être utilisée pour identifier les personnes en se basant sur un visage détecté et une base de données de personnes. Vous créez cette base de données à l’avance, elle pourra ensuite être modifiée au fil du temps.

La figure suivante est un exemple de base de données appelé « myfriends ». Chaque groupe peut contenir jusqu’à 1 000 000/10 000 objets Personne différents. Chaque objet Personne peut avoir jusqu’à 248 visages enregistrés.

![Vue d’ensemble - LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

Après avoir créé et formé une base de données, il est possible d’effectuer l’identification par rapport au groupe avec un nouveau visage détecté. Si le visage est identifié en tant qu’une personne du groupe, l’objet Personne est renvoyé.

Pour plus d’informations sur l’identification des personnes, consultez les guides d’API suivants :

[Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)  
[PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)  
[Personne PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)  
[PersonGroup - Effectuer l’apprentissage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)  
[LargePersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)  
[Personne LargePersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)  
[LargePersonGroup - Effectuer l’apprentissage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)  

#### <a name="face-storage-and-pricing"></a>Stockage Visage et tarification

Stockage Visage permet à un abonnement Standard de stocker des visages persistants supplémentaires lors de l’utilisation d’objets Personne LargePersonGroup/PersonGroup ([Personne PersonGroup - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[Personne LargePersonGroup - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) ou LargeFaceLists/FaceLists ([FaceList - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) à des fins d’identification ou de mise en correspondance des similarités avec l’API Visage. Les images stockées sont facturées au tarif de 0,50 $ tous les 1000 visages (tarif journalier au prorata). Les abonnements de niveau gratuit sont limités à un total de 1 000 personnes.

Par exemple, si votre compte a utilisé 10 000 visages persistants chaque jour pendant la première moitié du mois et aucun visage pendant la seconde moitié, vous êtes facturé uniquement pour les 10 000 visages pendant les jours stockés. De la même façon, si chaque jour du mois, vous rendez 1000 visages persistants pendant quelques heures, puis que vous les supprimez chaque soir, vous continuez à être facturé pour les 1000 visages rendus persistants chaque jour.

## <a name="sample-apps"></a>Exemples d’application

Intéressons-nous à ces exemples d’applications qui utilisent API Visage.

- [Microsoft API Visage : bibliothèque de client Windows et exemple](https://github.com/Microsoft/Cognitive-Face-Windows)
  - Exemple d’application WPF qui présente plusieurs scénarios de détection, d’analyse et d’identification de visages.
- [Application FamilyNotes UWP](https://github.com/Microsoft/Windows-appsample-familynotes)
  - Exemple d’application Universal Windows Platform (UWP) présentant l’utilisation de la reconnaissance vocale, de Cortana, de l’encre et de la caméra au moyen d’un scénario de partage FamilyNotes.
- [Exemple d’analyse des trames vidéo](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - Exemple d’application Win32 présentant l’analyse des flux vidéo en direct quasiment en temps réel à l’aide des API Visage, Vision par ordinateur et Émotion.

## <a name="tutorials"></a>Didacticiels
Les didacticiels suivants présentent les fonctionnalités de base et les processus d’abonnement de l’API Visage :
- [Didacticiel sur la prise en main de l’API Visage dans CSharp](Tutorials/FaceAPIinCSharpTutorial.md)
- [Prise en main de API Visage dans le didacticiel Java pour Android](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [Didacticiel sur la prise en main de l’API Visage dans Python](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="next-steps"></a>Étapes suivantes

Essayez un démarrage rapide afin d’implémenter un scénario d’API Visage simple.
- [Démarrage rapide : Détecter les visages dans une image à l’aide de C#](quickstarts/csharp.md) (disponible dans d’autres langues)
