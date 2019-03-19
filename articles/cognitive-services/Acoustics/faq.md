---
title: Questions fréquentes sur projet acoustique
titlesuffix: Azure Cognitive Services
description: Cette page fournit des réponses aux questions fréquemment posées sur Project Acoustics, notamment des instructions relatives au téléchargement et au processus de baking.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: c43c81d42a39bda504b02eb6c053a16a2cf53aec
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138046"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Questions fréquentes sur projet acoustique

## <a name="what-is-project-acoustics"></a>Qu’est-ce que Project Acoustics ?

La suite de projet acoustique de plug-ins est un système d’acoustiques qui calcule le comportement de son wave avant l’exécution, à l’éclairage statique. Le cloud effectuant le plus gros des calculs physiques des ondes au moment du design, le coût d’exécution du processeur est faible.  

## <a name="where-can-i-download-the-plugin"></a>Où puis-je télécharger le plug-in ?

Vous pouvez télécharger acoustique de projet à partir de la [page du centre de téléchargement de projet acoustique](https://www.microsoft.com/en-us/download/details.aspx?id=57346).

## <a name="does-project-acoustics-support-x-platform"></a>Les prend en charge du projet acoustique <x> plateforme ?

Prise en charge de projet acoustique de la plateforme évolue en fonction des besoins des clients. Veuillez nous contacter sur le [forums du projet acoustique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics) pour obtenir des informations sur la prise en charge de plates-formes supplémentaires.

## <a name="is-azure-used-at-runtime"></a>Azure est-il utilisé au moment de l’exécution ?

Non, l’intégration cloud est utilisée uniquement lors de l’étape de précalcul au moment de la configuration de la scène.
 
## <a name="what-is-simulation-input"></a>Qu’est-ce que l’entrée de simulation ? 

L’entrée de simulation est votre scène 3D, un environnement virtuel ou un niveau de jeu. Project Acoustics effectue des simulations d’ondes volumétriques 3D qui modélisent la physique du son de manière étroite, notamment la diffusion et l’occlusion fluide.
 
## <a name="what-is-the-runtime-cost"></a>Quel est le coût de l’exécution ?

Acoustics prend environ 0,01 % du processeur par source par trame. L’utilisation de la mémoire RAM varie selon la taille de la scène et peut aller de 10 à 100 Mo.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Ai-je besoin de simplifier la géométrie de niveau ? De contrôler le nombre de triangles ? De rendre les mailles étanches ?

Non. Le système ingère la géométrie de niveau détaillée directement. Elle est voxelisée pour le traitement interne.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Que contient le tableau de recherche du runtime ?

Inclut le fichier ACE est une table de paramètres acoustiques entre source et des paires d’emplacement écouteur nombreux, ainsi que des géométrie de scène voxelized utilisée pour l’interpolation de paramètre.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Projet acoustique peut gérer des sources de déplacement ?

Oui, projet acoustique consulte la table de recherche et met à jour de l’audio DSP sur chaque graduation, afin de pouvoir traiter déplacement sources et l’écouteur.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Projet acoustique peut gérer des geometry dynamique ? Les fermetures de portes ? Les murs qui explosent ?

Non. Les paramètres acoustiques sont précalculés d’après l’état statique d’un niveau de jeu. Nous vous suggérons en laissant géométrie porte hors acoustique, puis l’application occlusion supplémentaire en fonction de l’état de destructible et des objets de jeu mobiles à l’aide d’établie techniques.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Projet acoustique utilise-t-il des matériaux acoustique ?

Oui. Les matières sont récupérées à partir des noms de matières physiques dans votre niveau, pilotant la capacité d’absorption.
 
## <a name="what-do-the-probes-represent"></a>Que représentent les « sondes » ?

Les sondes sont un échantillonnage d’emplacements de lecteur possibles. Chaque sonde représente une simulation d’ondes distincte de la scène ayant comme origine l’emplacement de la sonde. Au moment de l’exécution, les paramètres acoustiques de l’emplacement de l’auditeur sont interpolés à partir d’emplacements de sonde à proximité.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Pourquoi effectuer autant de calcul dans le cloud ? En quoi cela m’est-il bénéfique ?

Project Acoustics fournit des paramètres acoustique précis et fiables même pour les environnements virtuels très complexes, tenant compte de tous les aspects architecturaux. Il fournit occlusion smooth et obstruction et variante réverbération dynamique sans le travail manuel de dessin des volumes. Tout ceci sans trop peser sur l’UC au moment de l’exécution.

## <a name="what-exactly-happens-during-baking"></a>Que se passe-t-il exactement pendant le « baking » ?

Un bake se compose de simulations d’ondes acoustiques des régions de simulation CUBOIDE centrées à chaque sonde de l’écouteur.

## <a name="next-steps"></a>Étapes suivantes
* Essayez le [exemple de contenu de projet acoustique Unity](unity-quickstart.md) ou [Unreal exemple de contenu](unreal-quickstart.md)

