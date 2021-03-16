---
title: Vue d’ensemble de l’analyse spatiale
titleSuffix: Azure Cognitive Services
description: Ce document explique les concepts et fonctionnalités de base d’un conteneur d’analyse spatiale Vision par ordinateur.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ad05dd59c925242baf5c2b0e36c1f51bc4fec5d4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575367"
---
# <a name="overview-of-computer-vision-spatial-analysis"></a>Vue d’ensemble de l’analyse spatiale Vision par ordinateur

L’analyse spatiale de Vision par ordinateur est une nouvelle fonctionnalité de l’API Vision par ordinateur d’Azure Cognitive Services qui permet aux organisations de maximiser la valeur de leurs espaces physiques en comprenant les déplacements et la présence des personnes dans une zone donnée. Elle vous permet d’ingérer le flux vidéo de caméras de télésurveillance, d’exploiter des opérations d’intelligence artificielle (IA) pour extraire des insights de flux vidéo, et de générer des événements que d’autres systèmes peuvent utiliser. À partir d’un flux de caméra, une opération d’IA peut effectuer des opérations telles que dénombrer les personnes entrant dans un espace ou mesurer le respect des consignes de port du masque ou de distanciation sociale.

## <a name="the-basics-of-spatial-analysis"></a>Concepts de base de l’analyse spatiale

Aujourd’hui, les principales opérations en lien avec l’analyse spatiale s’appuient sur un pipeline qui ingère un flux vidéo, détecte des personnes dans celui-ci, suit ces personnes dans leurs déplacements au fil du temps et génère des événements à mesure que les personnes interagissent avec des zones d’intérêt.

## <a name="spatial-analysis-terms"></a>Terminologie de l’analyse spatiale

| Terme | Définition |
|------|------------|
| Détection de personnes | Ce composant répond à la question « où sont les personnes dans cette image » ? Il recherche les êtres humains dans une image et transmet un rectangle englobant indiquant l’emplacement de chaque personne au composant de suivi des personnes. |
| Suivi des personnes | Ce composant connecte les détections de personnes au fil du temps à mesure que les personnes se déplacent devant une caméra. Pour ce faire, il utilise une logique temporelle relative à la manière dont les personnes se déplacent généralement, ainsi que des informations de base sur l’apparence globale des personnes. Il ne fait pas le suivi des personnes sur plusieurs caméras. Si une personne sort du champ d’une caméra pendant plus d’une minute environ, puis entre à nouveau dans le champ de la caméra, le système la considère comme une nouvelle personne. Le suivi des personnes n’identifie pas de manière unique les individus sur plusieurs caméras. Il n’utilise pas la reconnaissance faciale ni le suivi de la démarche. |
| Détection de masque | Ce composant détecte l’emplacement du visage d’une personne dans le champ de la caméra et identifie la présence d’un masque. Pour ce faire, l’opération d’IA analyse les images de la vidéo. Quand un visage est détecté, le service dessine un cadre englobant autour du visage. À l’aide des fonctionnalités de détection d’objets, il identifie la présence d’un masque dans le cadre englobant. La détection de masque ne nécessite pas de faire la distinction entre les visages, de prédire ou de classifier les attributs faciaux ni d’utiliser la reconnaissance faciale. |
| Zone d’intérêt | Il s’agit d’une zone ou d’une ligne définie dans la vidéo d’entrée lors de la configuration. Quand une personne interagit avec la zone de la vidéo, le système génère un événement. Par exemple, pour l’opération PersonCrossingLine, une ligne est définie dans la vidéo. Quand une personne franchit cette ligne, un événement est généré. |
| Événement | Un événement est la sortie principale de l’analyse spatiale. Chaque opération émet un événement spécifique soit périodiquement (par exemple, une fois par minute), soit quand un déclencheur spécifique s’active. L’événement inclut des informations sur ce qui s’est produit dans la vidéo d’entrée, mais n’inclut aucune image ou vidéo. Par exemple, l’opération PeopleCount peut émettre un événement contenant le nombre actualisé chaque fois que le nombre de personnes change (déclencheur) ou une fois par minute (périodiquement). |

## <a name="responsible-use-of-spatial-analysis-technology"></a>Utilisation responsable de la technologie d’analyse spatiale

Pour savoir comment utiliser la technologie d’analyse spatiale de façon responsable, consultez la [note de transparence](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). Les notes de transparence de Microsoft sont destinées à vous aider à comprendre le fonctionnement de notre technologie d’IA, les choix que les propriétaires de systèmes peuvent faire et qui influencent les performances et le comportement du système, et l’importance de penser à l’ensemble du système, y compris la technologie, les personnes et l’environnement.

## <a name="spatial-analysis-gating-for-public-preview"></a>Contrôle d’accès à l’analyse spatiale pour la préversion publique

Pour nous assurer que l’analyse spatiale soit utilisée pour les scénarios pour lesquels elle a été conçue, nous mettons cette technologie à la disposition des clients par le biais d’un processus de demande. Pour accéder à l’analyse spatiale, vous devez commencer par remplir notre formulaire de demande en ligne. [Commencez votre demande ici](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

L’accès à la préversion publique de l’analyse spatiale est à l’entière discrétion de Microsoft selon nos critères d’admissibilité, notre processus de vérification et notre capacité à prendre en charge un nombre limité de clients pendant cette période de préversion contrôlée. Dans le cadre de la préversion publique, nous recherchons des clients qui ont un lien significatif avec Microsoft, sont désireux de collaborer avec nous sur les cas d’usage recommandés, ainsi que d’autres scénarios en accord avec nos engagements en matière d’IA responsable.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Bien démarrer avec le conteneur Analyse spatiale](spatial-analysis-container.md)