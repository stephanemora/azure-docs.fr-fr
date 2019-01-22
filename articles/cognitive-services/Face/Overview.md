---
title: Qu’est ce que le l’API Visage ?
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser le service Visage pour détecter et analyser les visages dans les images.
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 10/29/2018
ms.author: sbowles
ms.openlocfilehash: 1916aa94dcf7528ce898ef37eac29c1017f03f15
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54231884"
---
# <a name="what-is-the-azure-face-api"></a>Qu’est ce que le l’API Visage Azure ?

L’API Visage Azure est un service cognitif qui fournit des algorithmes de détection, de reconnaissance et d’analyse des visages humains dans les images. Pouvoir traiter les informations des visages humains est important pour de nombreux domaines logiciels, notamment la sécurité, l’interface utilisateur naturelle, l’analyse et la gestion du contenu de l’image, les applications mobiles et la robotique.

L’API Visage fournit plusieurs fonctions différentes, chacune décrite dans les sections suivantes. Poursuivez votre lecture pour en savoir plus sur chacune d’elles et déterminer si elle répond à vos besoins.

## <a name="face-detection"></a>Détection des visages

L’API Visage peut détecter les visages humains dans une image et renvoyer les coordonnées du rectangle de leur emplacement. La détection faciale peut également permettre d’extraire un ensemble d’attributs associés au visage tels que la pose, le sexe, l’âge, la position de la tête, la pilosité du visage et les lunettes.

![Une image d’une femme et d’un homme, avec des rectangles dessinés autour de leur visage, et un âge et un sexe affichés](./Images/Face.detection.jpg)

La fonctionnalité de détection de visage est également disponible via l’[API Vision par ordinateur](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), mais si vous souhaitez effectuer des opérations supplémentaires avec les données de visage, utilisez l’API Visage (le présent service). Pour plus d’informations sur la détection de visage, consultez la section [API de détection](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-verification"></a>Vérification faciale

L’API de vérification effectue une authentification en comparant deux visages détectés ou un visage détecté et un objet Personne. En pratique, il évalue si deux visages appartiennent à la même personne. Cela peut être utile dans le domaine de la sécurité. Pour plus d’informations, consultez la section [API de vérification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="find-similar-faces"></a>Rechercher des visages semblables

L’API de recherche de visages semblables prend un visage cible et un ensemble de visages de candidats et identifie un petit ensemble de visages ressemblant le plus au visage cible. Deux modes de travail, **matchPerson** et **matchFace**, sont pris en charge. Le mode **matchPerson** renvoie des visages semblables après avoir filtré les résultats pour la même personne (à l’aide de l’[API de vérification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)). Le mode **matchFace** ignore le filtre précédent et renvoie une liste de visages candidats semblables qui peuvent ou non appartenir à la même personne.

Dans l’exemple suivant, il s’agit du visage cible :

![Une femme qui sourit](./Images/FaceFindSimilar.QueryFace.jpg)

Et voici les visages candidats :

![Cinq images de personnes qui sourient. Les images (a) et (b) correspondent à la même personne](./Images/FaceFindSimilar.Candidates.jpg)

Pour rechercher quatre visages similaires, le mode **matchPerson** renvoie (a) et (b), qui représentent le même visage que la cible. Le mode **matchFace** renvoie (a), (b), (c) et (d) &mdash; soit exactement quatre candidats, même si certains n’appartiennent pas à la même personne que le visage cible ou s’ils ont une faible similarité. Pour plus d’informations, consultez la section [API de recherche de visages semblables](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

## <a name="face-grouping"></a>Regroupement de visages

L’API de regroupement divise un ensemble de visages inconnus en plusieurs groupes en fonction de leurs similarités. Chaque groupe est un sous-ensemble distinct de l’ensemble de visages d’origine. Tous les visages d’un groupe sont susceptibles d’appartenir à la même personne, mais il peut y avoir plusieurs groupes pour une même personne (différenciés par un autre facteur, telles que les expressions du visage par exemple). Pour plus d’informations, consultez la section [API de regroupement](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="person-identification"></a>Identification de personnes

L’API d’identification peut être utilisée pour identifier un visage détecté en le comparant à ceux d’une base de données de personnes. Cela peut être utile pour baliser automatiquement les images dans les logiciels de gestion de photos. Vous créez la base de données à l’avance, elle pourra ensuite être modifiée au fil du temps.

L’image suivante est un exemple de base de données nommée « myfriends ». Chaque groupe peut contenir jusqu’à 1 000 000 d’objets Personne différents, et chaque objet Personne peut avoir jusqu’à 248 visages enregistrés.

![Une grille avec 3 colonnes pour les différentes personnes, chacune avec 3 lignes d’images de visage](./Images/person.group.clare.jpg)

Après avoir créé et entraîné une base de données, il est possible d’effectuer l’identification par rapport au groupe avec un nouveau visage détecté. Si le visage est identifié en tant qu’une personne du groupe, l’objet Personne est renvoyé.

Pour plus d’informations sur l’identification des personnes, consultez la section [API d’identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="use-containers"></a>Utiliser des conteneurs

[Utilisez le conteneur Visage](face-how-to-install-containers.md) pour détecter, reconnaître et identifier des visages, en installant un conteneur Docker normalisé plus près de vos données.

## <a name="sample-apps"></a>Exemples d’application

Les exemples d’applications suivants présentent quelques-unes des méthodes d’utilisation de l’API Visage.

- [API Visage de Microsoft : bibliothèque de client Windows et exemple](https://github.com/Microsoft/Cognitive-Face-Windows) : application WPF qui présente plusieurs scénarios de détection, d’analyse et d’identification de visages.
- [Application FamilyNotes UWP](https://github.com/Microsoft/Windows-appsample-familynotes) - une application Universal Windows Platform (UWP) qui utilise l’identification des visages, ainsi que la reconnaissance vocale, Cortana, l’écriture manuscrite et la caméra dans un scénario de partage de notes en famille.

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

Comme avec tous les services Cognitive Services, les développeurs utilisant le service Visage doivent connaître les politiques de Microsoft relatives aux données client. Pour en savoir plus, consultez la [page Cognitive Services](https://www.microsoft.com/en-us/trustcenter/cloudservices/cognitiveservices) sur le Centre de gestion de la confidentialité Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Suivez un guide de démarrage rapide pour implémenter un scénario de détection des visages dans du code.
- [Démarrage rapide : Détecter les visages dans une image à l’aide du SDK .NET avec C#](quickstarts/csharp.md) (disponible dans d’autres langues)
