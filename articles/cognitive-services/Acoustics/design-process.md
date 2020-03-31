---
title: Principes de conception avec simulation acoustique
titlesuffix: Azure Cognitive Services
description: Cette vue d’ensemble conceptuelle explique comment Project Acoustics intègre la simulation acoustique au processus de conception sonore.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 11e1e3f45b5198ddedb6c31fcd354185adef445d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854309"
---
# <a name="project-acoustics-design-process-concepts"></a>Concepts du processus de conception de Project Acoustics

Cette vue d’ensemble conceptuelle explique comment Project Acoustics intègre la simulation acoustique physique au processus de conception sonore.

## <a name="sound-design-with-audio-dsp-parameters"></a>Conception sonore avec les paramètres DSP audio

Les titres interactifs 3D tirent leur son particulier de blocs de traitement numérique du signal (DSP) hébergés dans un moteur audio, soit, du plus simple au plus complexe : mixage simple, réverbération, écho, délai, égalisation, compression et limitation, parmi d’autres effets. C’est au concepteur sonore qu’il revient de sélectionner, d’arranger et de paramétrer ces effets : il crée un graphe audio qui atteint les objectifs d’esthétique et de jouabilité de l’expérience.

Dans un titre interactif, où les sons et l’écouteur se déplacent dans l’espace 3D, comment ces paramètres s’adaptent-ils à l’évolution des conditions ? Le concepteur sonore arrange souvent des volumes dans l’espace, programmés pour déclencher des modifications de paramètres qui changent les effets de réverbération, par exemple, ou baissent les sons du mix lorsque l’écouteur du déplace d’une partie de la scène à une autre. Il existe également des systèmes acoustiques capables d’automatiser certains de ces effets.

Les titres 3D utilisent des systèmes de cinématique et d’éclairage animés par des mouvements physiques, mais ajustés par le concepteur de façon à allier immersion et jouabilité. Un concepteur visuel ne définit pas des valeurs de pixel individuelles, mais ajuste des modèles 3D, des matériaux et des systèmes de transport léger qui représentent un compromis physique entre esthétique et coûts de processeur. Quel serait le processus équivalent pour l’audio ? Project Acoustics constitue une première étape dans l’exploration de cette question. Nous allons tout d’abord étudier ce que signifie transporter de l’énergie acoustique dans un espace.

![Capture d’écran de la scène AltSpace sur laquelle sont superposées les zones de réverbération](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Réponses impulsionnelles : le lien acoustique entre deux points dans l’espace

Si vous connaissez la conception audio, les réponses impulsionnelles acoustiques vous sont peut-être familières. Une réponse impulsionnelle acoustique modélise le transport d’un son d’une source vers un écouteur. Elle peut donc capturer chacun des effets intéressants de l’acoustique d’une pièce, comme l’occlusion et la réverbération. Elle possède également de puissantes propriétés qui permettent d’utiliser des effets DSP audio à grande échelle. Il revient au même d’ajouter deux signaux audio et de les traiter avec une réponse impulsionnelle que d’appliquer la réponse impulsionnelle séparément pour chaque signal et d’ajouter les résultats. La propagation acoustique et les réponses impulsionnelles ne dépendent pas non plus de l’audio traité, mais seulement de la scène modélisée et de l’emplacement de la source et de l’écouteur. En bref, une réponse impulsionnelle condense l’effet de la scène sur la propagation audio.

Une réponse impulsionnelle capture chacun des effets intéressants de l’acoustique d’une pièce : il est alors possible de l’appliquer efficacement au son avec un filtre et d’obtenir des réponses impulsionnelles de la mesure ou de la simulation. Mais comment faire en sorte que l’acoustique ne corresponde pas exactement à la physique, mais la modèle pour répondre aux exigences émotionnelles d’une scène ? Tout comme les valeurs en pixels, une réponse impulsionnelle est une simple liste de milliers de nombres : comment, dans ce cas, peut-on l’adapter aux besoins esthétiques ? Et comment obtenir une occlusion/obstruction qui varie de manière fluide au moment de franchir des portes ou des obstacles ? Combien de réponses impulsionnelles faut-il pour que l’effet soit harmonieux ? Que se passe-t-il si la source se déplace rapidement ? Comment interpoler ?

Il semble difficile d’utiliser la simulation et les réponses impulsionnelles pour certains aspects acoustiques des titres interactifs. Cependant, nous pouvons créer un système de transport audio qui gère les ajustements du concepteur, à condition de pouvoir connecter les réponses impulsionnelles issues de la simulation aux paramètres d’effet DSP audio que nous connaissons bien.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Connecter la simulation aux traitements DSP audio avec des paramètres

Une réponse impulsionnelle contient chacun des effets acoustiques intéressants (et non intéressants). Les blocs DSP audio dont les paramètres sont définis correctement peuvent donner un effet acoustique intéressant. Utiliser la simulation acoustique pour piloter un bloc DSP audio qui automatise le transport audio dans une scène 3D revient simplement à mesurer les paramètres DSP audio d’une réponse impulsionnelle. Cette mesure est bien comprise pour certains effets acoustiques courants et importants, notamment l’occlusion, l’obstruction, le portalling et la réverbération.

Mais, si la simulation est connectée directement aux paramètres DSP audio, où est l’ajustement du concepteur ? Qu'y avons-nous gagné ? Nous avons récupéré une quantité importante de mémoire en abandonnant les réponses impulsionnelles et en conservant quelques paramètres DSP. Pour donner au concepteur une certaine maîtrise du résultat final, il suffit de trouver un moyen de l’insérer entre la simulation et le DSP.

![Graphe avec réponse impulsionnelle stylisée et paramètres superposés](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Conception sonore en transformant des paramètres DSP audio de la simulation

Prenons les effets qu’ont des lunettes de soleil sur la vision du monde. S’il y a du soleil, elles peuvent réduire son éclat à un niveau plus confortable. On risque en revanche de ne rien y voir dans une pièce sombre. Les lunettes ne définissent pas un certain niveau de luminosité dans toutes les situations ; elles rendent simplement tout plus sombre.

Si nous utilisons la simulation pour piloter notre DSP audio avec des paramètres d’occlusion et de réverbération, nous pouvons ajouter un filtre après le simulateur afin d’ajuster les paramètres que le DSP « voit ». Le filtre n’appliquerait pas un certain niveau d’occlusion ou une certaine longueur de réverbération, de même que les lunettes de soleil ne donnent pas la même luminosité à chaque pièce. Le filtre peut simplement limiter ou augmenter l’occlusion de chaque dispositif d’occlusion. En ajoutant et en ajustant un filtre de paramètre d’occlusion « assombrissant », les grandes pièces ouvertes n’auraient toujours pas (ou peu) d’effet d’occlusion, tandis que les portes passeraient d’un effet d’occlusion moyen à un effet fort tout en maintenant la fluidité des transitions d’effets de la simulation.

Dans ce paradigme, la tâche du concepteur évolue : au lieu de choisir les paramètres acoustiques de chaque situation, il sélectionne et ajuste les filtres à appliquer aux paramètres DSP les plus importants de la simulation. Les activités du concepteur s’éloignent ainsi des considérations étroites liées à la configuration de transitions fluides pour toucher à des problématiques supérieures : l’intensité des effets d’occlusion et de réverbération et la présence des sources dans le mix. Bien sûr, lorsque la situation l’impose, il existe un filtre qui reste toujours disponible : revenir en arrière pour choisir les paramètres DSP d’une source spécifique dans une situation donnée.

## <a name="sound-design-in-project-acoustics"></a>Conception sonore dans Project Acoustics

Le package Project Acoustics intègre chacun des composants décrits ci-dessus : un simulateur, un encodeur qui extrait les paramètres et génère l’élément acoustique, le DSP audio et une sélection de filtres. La conception sonore avec Project Acoustics implique de choisir des filtres qui ajustent les paramètres d’occlusion et de réverbération dérivés de la simulation et appliqués au DSP audio, avec des contrôles dynamiques exposés à l’intérieur de l’éditeur de jeu et du moteur audio.

## <a name="next-steps"></a>Étapes suivantes
* Essayez le paradigme de conception avec le [Démarrage rapide Project Acoustics pour Unity](unity-quickstart.md) ou le [Démarrage rapide Project Acoustics pour Unreal](unreal-quickstart.md)
* Explorez les [Contrôles de conception Project Acoustics pour Unity](unity-workflow.md) ou les [Contrôles de conception Project Acoustics pour Unreal](unreal-workflow.md)

