---
title: Questions fréquentes (FAQ) sur Acoustics - Cognitive Services
description: Cette page fournit des réponses aux questions fréquemment posées sur Project Acoustics, notamment des instructions relatives au téléchargement et au processus de baking.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: a71e867bd23cf64b2ac7fc8cd1c54c55d92ce924
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431786"
---
# <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

## <a name="what-is-project-acoustics"></a>Qu’est-ce que Project Acoustics ?

Le plug-in Project Acoustics Unity est un système d’acoustique qui calcule le comportement des ondes sonores avant l’exécution, un peu comme l’éclairage statique. Le cloud effectuant le plus gros des calculs physiques des ondes au moment du design, le coût d’exécution du processeur est faible.  

## <a name="where-can-i-download-the-plugin"></a>Où puis-je télécharger le plug-in ?

Si vous êtes intéressé par l’évaluation du plug-in Acoustics, inscrivez-vous [ici](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) pour rejoindre la préversion Designer Preview.

## <a name="is-azure-used-at-runtime"></a>Azure est-il utilisé au moment de l’exécution ?

Non, l’intégration cloud est utilisée uniquement lors de l’étape de précalcul au moment de la configuration de la scène.
 
## <a name="what-is-simulation-input"></a>Qu’est-ce que l’entrée de simulation ? 

L’entrée de simulation est votre scène 3D, un environnement virtuel ou un niveau de jeu. Project Acoustics effectue des simulations d’ondes volumétriques 3D qui modélisent la physique du son de manière étroite, notamment la diffusion et l’occlusion fluide.
 
## <a name="what-is-the-runtime-cost"></a>Quel est le coût de l’exécution ?

Acoustics prend environ 0,01 % du processeur par source par trame. L’utilisation de la mémoire RAM varie selon la taille de la scène et peut aller de 10 à 100 Mo.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Ai-je besoin de simplifier la géométrie de niveau ? De contrôler le nombre de triangles ? De rendre les mailles étanches ?

Non. Le système ingère la géométrie de niveau détaillée directement. Elle est voxelisée pour le traitement interne.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Que contient le tableau de recherche du runtime ?

Le fichier ACE est un tableau de paramètres acoustiques entre de nombreuses paires d’emplacements de source et d’auditeur.
 
## <a name="can-it-handle-moving-sources"></a>Peut-il gérer les sources en déplacement ?

Oui, le plug-in spatialiseur **Microsoft Acoustics** Unity consulte la table de recherche lors de chaque cycle de traitement audio avec les emplacements de source et d’auditeur actuels. Le DSP du spatialiseur met à jour les paramètres de traitement acoustique sans discontinuité à chaque cycle.
 
## <a name="can-it-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Peut-il gérer la géométrie dynamique ? Les fermetures de portes ? Les murs qui explosent ?

Non. Les paramètres acoustiques sont précalculés d’après l’état statique d’un niveau de jeu. Nous vous suggérons de laisser la géométrie de porte hors acoustique, et d’appliquer une occlusion supplémentaire en fonction de l’état des objets de jeu destructibles et mobiles à l’aide de techniques établies.
 
## <a name="does-it-handle-materials"></a>Gère-t-il les matières ?

Oui. Les matières sont récupérées à partir des noms de matières physiques dans votre niveau, pilotant la capacité d’absorption.
 
## <a name="what-do-the-probes-represent"></a>Que représentent les « sondes » ?

Les sondes sont un échantillonnage d’emplacements de lecteur possibles. Chaque sonde représente une simulation d’ondes distincte de la scène ayant comme origine l’emplacement de la sonde. Au moment de l’exécution, les paramètres acoustiques de l’emplacement de l’auditeur sont interpolés à partir d’emplacements de sonde à proximité.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Pourquoi effectuer autant de calcul dans le cloud ? En quoi cela m’est-il bénéfique ?

Project Acoustics fournit des paramètres acoustique précis et fiables même pour les environnements virtuels très complexes, tenant compte de tous les aspects architecturaux. Il fournit une obstruction/occlusion fluide sans tout le travail manuel et la variation de réverbération dynamique et sans dessiner de volume. Tout ceci sans trop peser sur l’UC au moment de l’exécution.

## <a name="what-exactly-happens-during-baking"></a>Que se passe-t-il exactement pendant le « baking » ?

Le système prend en compte les emplacements de lecteur potentiels afin de générer un ensemble de positions d’échantillons de « sonde » espacées de façon uniforme. Le baking d’un niveau se compose de tâches indépendantes pour chaque sonde : le système considère une « région de simulation » cuboïde centrée sur la sonde et effectue une simulation d’ondes détaillée dans cette région à une résolution allant jusqu’à 25 cm.

## <a name="next-steps"></a>Étapes suivantes
* Explorer l’[exemple de scène](sample-walkthrough.md)

