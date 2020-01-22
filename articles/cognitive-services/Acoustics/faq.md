---
title: Forum aux questions (FAQ) sur Project Acoustics
titlesuffix: Azure Cognitive Services
description: Cette page fournit des réponses aux questions fréquemment posées sur Project Acoustics, notamment des instructions relatives au téléchargement et au processus de baking.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fa4b6499260219b0eb8ea4df4b4ccfd5263b57bb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770201"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Forum aux questions (FAQ) sur Project Acoustics

## <a name="what-is-project-acoustics"></a>Qu’est-ce que Project Acoustics ?

La série de plug-ins Project Acoustics est un système d’acoustique qui calcule le comportement des ondes sonores avant l’exécution, un peu comme l’éclairage statique. Le cloud effectuant le plus gros des calculs physiques des ondes au moment du design, le coût d’exécution du processeur est faible.  

## <a name="where-can-i-download-the-plugin"></a>Où puis-je télécharger le plug-in ?

Vous pouvez télécharger le [plug-in Project Acoustics pour Unity](https://www.microsoft.com/download/details.aspx?id=57346) ou [le plug-in Project Acoustics pour Unreal](https://www.microsoft.com/download/details.aspx?id=58090).

## <a name="does-project-acoustics-support-ltxgt-platform"></a>Est-ce que Project Acoustics prend en charge la plateforme &lt;x&gt; ?

La prise en charge des plateformes par Project Acoustics évolue en fonction des besoins des clients. Veuillez nous contacter sur les [forums de Project Acoustics](https://github.com/microsoft/ProjectAcoustics/issues) pour obtenir des informations sur la prise en charge de plateformes supplémentaires.

## <a name="is-azure-used-at-runtime"></a>Azure est-il utilisé au moment de l’exécution ?

Non, l’intégration cloud est utilisée uniquement lors de l’étape de précalcul au moment de la configuration de la scène.
 
## <a name="what-is-simulation-input"></a>Qu’est-ce que l’entrée de simulation ? 

L’entrée de simulation est votre scène 3D, un environnement virtuel ou un niveau de jeu. Project Acoustics effectue des simulations d’ondes volumétriques 3D qui modélisent la physique du son de manière étroite, notamment la diffusion et l’occlusion fluide.
 
## <a name="what-is-the-runtime-cost"></a>Quel est le coût de l’exécution ?

Acoustics prend environ 0,01 % du processeur par source par trame. L’utilisation de la mémoire RAM varie selon la taille de la scène et peut aller de 10 à 100 Mo.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Ai-je besoin de simplifier la géométrie de niveau ? De contrôler le nombre de triangles ? De rendre les mailles étanches ?

Non. Le système ingère la géométrie de niveau détaillée directement. Elle est voxelisée pour le traitement interne.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Que contient le tableau de recherche du runtime ?

Le fichier ACE inclut une table de paramètres acoustiques entre de nombreuses paires d’emplacements source/écouteur, ainsi que la géométrie de scène voxelisée utilisée pour l’interpolation des paramètres.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Project Acoustics peut-il gérer le déplacement des sources ?

Oui, Project Acoustics consulte la table de recherche et met à jour le DSP audio lors de chaque cycle, afin de pouvoir traiter le déplacement des sources et de l’écouteur.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Project Acoustics peut-il gérer la géométrie dynamique ? Les fermetures de portes ? Les murs qui explosent ?

Non. Les paramètres acoustiques sont précalculés d’après l’état statique d’un niveau de jeu. Nous vous suggérons de laisser la géométrie de porte hors acoustique, et d’appliquer une occlusion supplémentaire en fonction de l’état des objets de jeu destructibles et mobiles à l’aide de techniques établies.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Project Acoustics utilise-t-il des matériaux acoustiques ?

Oui. Les matières sont récupérées à partir des noms de matières physiques dans votre niveau, pilotant la capacité d’absorption.
 
## <a name="what-do-the-probes-represent"></a>Que représentent les « sondes » ?

Les sondes sont un échantillonnage d’emplacements de lecteur possibles. Chaque sonde représente une simulation d’ondes distincte de la scène ayant comme origine l’emplacement de la sonde. Au moment de l’exécution, les paramètres acoustiques de l’emplacement de l’auditeur sont interpolés à partir d’emplacements de sonde à proximité.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Pourquoi effectuer autant de calcul dans le cloud ? En quoi cela m’est-il bénéfique ?

Project Acoustics fournit des paramètres acoustique précis et fiables même pour les environnements virtuels très complexes, tenant compte de tous les aspects architecturaux. Il fournit une obstruction/occlusion fluide sans tout le travail manuel et la variation de réverbération dynamique et sans dessiner de volume. Tout ceci sans trop peser sur l’UC au moment de l’exécution.

## <a name="what-exactly-happens-during-baking"></a>Que se passe-t-il exactement pendant le « baking » ?

Un bake se compose de simulations d’ondes acoustiques de régions de simulation cuboïdes centrées sur chaque sonde d’écouteur.

## <a name="is-my-source-content-secure"></a>Mon contenu source est-il sécurisé ?

Project Acoustics ne charge pas la géométrie de scène source dans le cloud. Au lieu de cela, la simulation opère sur un voxélisation de votre scène, qui est combiné avec les données d’emplacement du probe et stocké dans un format propriétaire.     

## <a name="next-steps"></a>Étapes suivantes
* Essayez [l’exemple de contenu de Project Acoustics pour Unity](unity-quickstart.md) ou [l’exemple de contenu pour Unreal](unreal-quickstart.md)

