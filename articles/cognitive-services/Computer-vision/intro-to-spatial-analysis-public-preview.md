---
title: Qu'est-ce que l'Analyse spatiale ?
titleSuffix: Azure Cognitive Services
description: Ce document explique les concepts et fonctionnalités de base d'un conteneur Analyse spatiale Vision par ordinateur.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.openlocfilehash: 5aa34ce15d96112450a7c15debcd92312c1d9e19
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284540"
---
# <a name="what-is-spatial-analysis"></a>Qu'est-ce que l'Analyse spatiale ?

Le service Analyse spatiale aide les organisations à optimiser la valeur de leurs espaces physiques en détectant la présence de personnes et en analysant les déplacements de celles-ci au sein d'une zone donnée. Elle vous permet d’ingérer le flux vidéo de caméras de télésurveillance, d’exploiter des opérations d’intelligence artificielle (IA) pour extraire des insights de flux vidéo, et de générer des événements que d’autres systèmes peuvent utiliser. À partir d’un flux de caméra, une opération d’IA peut effectuer des opérations telles que dénombrer les personnes entrant dans un espace ou mesurer le respect des consignes de port du masque ou de distanciation sociale.

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>Résultat

Les opérations de base de l'Analyse spatiale reposent toutes sur un pipeline qui ingère un flux vidéo, détecte les personnes qui s'y trouvent, suit ces personnes dans leurs déplacements au fil du temps et génère des événements à mesure que les personnes interagissent avec des zones d'intérêt.

## <a name="spatial-analysis-features"></a>Fonctionnalités de l'Analyse spatiale

| Fonctionnalité | Définition |
|------|------------|
| **Détection de personnes** | Ce composant répond à la question « Où sont les personnes dans cette image » ? Il détecte les personnes contenues dans une image et affiche un cadre englobant qui indique l'emplacement de chacune d'entre elles au composant de suivi des personnes. |
| **Suivi des personnes** | Ce composant connecte les détections de personnes au fil du temps à mesure que les personnes se déplacent devant une caméra. Il utilise une logique temporelle relative à la manière dont les personnes ont l'habitude de se déplacer, ainsi que des informations de base sur leur apparence globale. Il ne fait pas le suivi des personnes sur plusieurs caméras. Si une personne sort du champ de la caméra pendant plus d'une minute avant d'y revenir, le système la considère comme une nouvelle personne. Le suivi des personnes n’identifie pas de manière unique les individus sur plusieurs caméras. Il n’utilise pas la reconnaissance faciale ni le suivi de la démarche. |
| **Détection de masque** | Ce composant détecte l'emplacement du visage d'une personne dans le champ de la caméra et identifie la présence d'un masque. L'opération d'IA analyse les images de la vidéo. Lorsqu'un visage est détecté, le service affiche un cadre englobant autour de celui-ci. À l’aide des fonctionnalités de détection d’objets, il identifie la présence d’un masque dans le cadre englobant. La détection de masque ne nécessite pas de faire la distinction entre les visages, de prédire ou de classifier les attributs faciaux ni d’utiliser la reconnaissance faciale. |
| **Zone d’intérêt** | Il s'agit d'une zone ou d'une ligne définie par l'utilisateur dans l'image vidéo d'entrée. Lorsqu'une personne interagit avec cette zone de la vidéo, le système génère un événement. Par exemple, pour l’opération PersonCrossingLine, une ligne est définie dans la vidéo. Quand une personne franchit cette ligne, un événement est généré. |
| **Event** | Un événement est la sortie principale de l'Analyse spatiale. Chaque opération émet un événement spécifique périodiquement (par exemple, une fois par minute) ou à chaque occurrence d'un déclencheur spécifique. L’événement inclut des informations sur ce qui s’est produit dans la vidéo d’entrée, mais n’inclut aucune image ou vidéo. Par exemple, l’opération PeopleCount peut émettre un événement contenant le nombre actualisé chaque fois que le nombre de personnes change (déclencheur) ou une fois par minute (périodiquement). |

## <a name="get-started"></a>Bien démarrer

### <a name="public-preview-gating"></a>Accès à la préversion

Pour veiller à ce que l'Analyse spatiale soit utilisée dans les scénarios pour lesquels elle a été conçue, nous mettons cette technologie à la disposition des clients par le biais d'un processus de demande d'accès. Pour accéder à l'Analyse spatiale, vous devez commencer par remplir notre formulaire de demande en ligne. [Commencez votre demande ici](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

L'accès à la préversion publique de l'Analyse spatiale est à l'entière discrétion de Microsoft conformément à ses critères d'admissibilité, à son processus de vérification et à sa capacité à prendre en charge un nombre limité de clients pendant cette période de préversion contrôlée. Dans le cadre de la préversion publique, Microsoft recherche des clients qui ont un lien privilégié avec ses équipes, et qui sont désireux de collaborer avec elles sur les cas d'usage recommandés, ainsi que d'autres scénarios en accord avec les engagements de Microsoft en matière d'IA responsable.

### <a name="follow-a-quickstart"></a>Suivre un guide de démarrage rapide

Une fois que vous avez accès à l'Analyse spatiale, suivez le [guide de démarrage rapide](spatial-analysis-container.md) pour configurer le conteneur et entamer l'analyse de la vidéo.

## <a name="responsible-use-of-spatial-analysis-technology"></a>Utilisation responsable de la technologie d’analyse spatiale

Pour savoir comment utiliser la technologie d’analyse spatiale de façon responsable, consultez la [note de transparence](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). Les notes de transparence de Microsoft sont destinées à vous aider à comprendre le fonctionnement de notre technologie d'IA, les choix que les propriétaires de systèmes peuvent faire et qui influencent les performances et le comportement du système, et l'importance d'appréhender le système dans son ensemble, en englobant la technologie, les personnes et l'environnement.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Démarrage rapide : Conteneur Analyse spatiale](spatial-analysis-container.md)''''''''''''