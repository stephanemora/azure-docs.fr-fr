---
title: Principes de conception avec simulation acoustique
titlesuffix: Azure Cognitive Services
description: Cette vue d’ensemble conceptuelle explique comment projet acoustique incorpore acoustique simulation pour le processus de conception.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 4a1a0b15da091a1c020eb132f6b14b9ee14d334c
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316066"
---
# <a name="project-acoustics-design-process-concepts"></a>Concepts de processus de conception d’acoustique de projet

Cette vue d’ensemble conceptuelle explique comment projet acoustique incorpore les simulation acoustique physique dans le processus de conception.

## <a name="sound-design-with-audio-dsp-parameters"></a>Sonorisation avec les paramètres audio DSP

Les titres 3D interactives atteindre leur son particulier à l’aide de blocs (DSP) hébergés dans un moteur audio de traitement du signal numérique audio. Plage de ces blocs en termes de complexité à partir de la simple mélange au réverbération, echo, délai, égalisation, la compression et la limitation et d’autres effets. Sélection, la réorganisation et la définition des paramètres dans ces effets sont la responsabilité du son concepteur, qui génère un graphique d’audio qui accomplit les objectifs esthétique et le jeu de l’expérience.

Dans un titre interactif, comme les sons et l’écouteur déplacent tout au long de l’espace 3D, comment ces paramètres s’adapter aux évolutions ? Le concepteur son souvent Réorganiser tout au long de l’espace des volumes qui sont programmées pour déclencher des modifications de paramètre pour obtenir des modifications dans les effets de réverbération, par exemple, ou pour les sons de canard dans la combinaison lors de l’écouteur du déplace d’une partie de la scène à un autre. Acoustique systèmes sont également disponibles qui peuvent automatiser une partie de ces effets.

Titres 3D utilisent des systèmes physique cinématique et d’éclairage qui sont dans un monde physique, mais ajustée de concepteur pour obtenir une combinaison d’objectifs immersion et le jeu. Un concepteur visuel ne définit pas les valeurs de pixel individuelles, mais plutôt ajuste les modèles 3D, des matériels et des systèmes de transport clair qui sont des physiquement compromis esthétique et les coûts de processeur. Quel est le processus équivalent pour l’audio ? Projet acoustique est une première étape dans l’exploration de cette question. Tout d’abord, je parlerai plus tard sur ce que signifie l’énergie acoustique via un espace de transport.

![Scène de capture d’écran de AltSpace superposée aux zones de réverbération](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Réponses impulsionnelles : Acoustique reliant deux points dans l’espace

Si vous êtes familiarisé avec la conception audio, vous connaissez peut-être des réponses impulsionnelles acoustique. Une réponse impulsionnelle acoustique modélise le transport d’un son à partir d’une source à un écouteur. Par conséquent, une réponse impulsionnelle peut capturer chaque effet intéressant de tels qu’occlusion et réverbération acoustique de la pièce. Réponses impulsionnelles possèdent également des propriétés puissants qui permettent des effets audio DSP à l’échelle. Additionner des deux signaux audio et de traitement avec une réponse impulsionnelle donne le même résultat que l’application de la réponse impulsionnelle séparément à chaque signal et en ajoutant les résultats. La propagation acoustique et des réponses impulsionnelles également ne dépendent pas l’audio en cours de traitement, uniquement sur la scène en cours de modélisation, ainsi que les emplacements source et l’écouteur. En bref, une réponse impulsionnelle rassemble effet de la scène sur la propagation audio ;

Une réponse impulsionnelle capture chaque salle acoustique un effet intéressant et nous pouvons l’appliquer au format audio efficacement avec un filtre, et nous pouvons obtenir des réponses impulsionnelles à partir de la mesure ou de simulation. Mais que se passe-t-il si nous ne souhaitez tout à fait l’acoustique pour correspondre exactement à la physique, mais plutôt de moule pour correspondre à des demandes d’une scène émotionnels ? Mais à l’instar valeurs en pixels, une réponse impulsionnelle est une simple liste de milliers de nombres, comment pouvons-nous nous éventuellement ajuster pour répondre aux besoins esthétiques ? Et que se passe-t-il si nous voulons occlusion/obstruction qui fait varier sans heurts durant le passage par le biais guichets ou derrière les obstacles, nombre de réponses impulsionnelle avons-nous besoin obtenir un effet sans heurts ? Que se passe-t-il si la source déplace rapidement ? Comment nous interpoler ?

Il semble difficile à utiliser la simulation et réponses impulsionnelles concernant certains aspects d’acoustiques dans les titres interactives. Mais nous pouvons créer un système de transport audio qui prend en charge des ajustements concepteurs si nous pouvons nous connecter nos réponses impulsionnelles à partir de la simulation avec nos paramètres d’effet audio DSP familiers.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Connexion de simulation à audio DSP avec des paramètres

Une réponse impulsionnelle contient chaque intéressantes (et chaque inintéressants) acoustique effet. Les blocs audio DSP, lorsque leurs paramètres sont définis correctement, peuvent rendre un effet acoustique intéressant. À l’aide de la simulation acoustique pour piloter un bloc DSP audio pour automatiser le transport audio dans une scène 3D n'est qu’une question de mesurer les paramètres DSP audio à partir d’une réponse impulsionnelle. Cette mesure est bien entendue pour certains effets acoustiques courantes et importantes, notamment occlusion, obstruction, portalling et réverbération.

Mais si la simulation est connectée directement aux paramètres DSP audio, où est l’ajustement de concepteur ? Ce qu’obtenir ? Eh bien, nous obtenons une quantité importante de mémoire en supprimant les réponses impulsionnelles et en conservant quelques paramètres DSP. Et pour donner le concepteur du courant sur le résultat final, nous devons uniquement trouver un moyen d’insérer le concepteur entre la simulation et l’audio DSP.

![L’API Graph à réponse impulsionnelle stylisés avec des paramètres à superposer](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Sonorisation en transformant des paramètres DSP audio à partir de la simulation

Prendre en compte les effets que votre lunettes de soleil sur votre vue du monde. Un jour lumineux, les lunettes peuvent réduire l’éclat à quelque chose de plus à l’aise. Dans une salle foncée, vous ne serez peut-être pas en mesure de voir rien du tout. Les lunettes ne définissez pas un certain niveau de luminosité dans toutes les situations ; celles-ci permettent simplement de tous les éléments plus sombre.

Si nous utilisons la simulation pour piloter notre DSP audio à l’aide des paramètres d’occlusion et réverbération, nous pouvons ajouter un filtre après le simulateur d’ajuster les paramètres DSP « voit ». Le filtre n’aurait pas appliquer un certain niveau d’occlusion ou réverbération beaucoup de longueur de fin, comme lunettes de soleil n’effectuez pas la même luminosité chaque salle. Le filtre peut le mettre chaque OCCLUSION occlude inférieur. Ou occlude bien plus encore. En ajoutant et en ajustant un filtre de paramètre occlusion « assombrissement », les salles de grande taille, ouvrez n’aurait toujours peu à aucun effet occlusion, tandis que les guichets augmenterait à partir d’un support à un effet d’occlusion fort, tout en conservant le lissage en vigueur transitions que la simulation fournit.

Dans ce paradigme, les tâches du concepteur change de choisir les paramètres acoustiques pour chaque situation, de sélection et l’ajustement des filtres à appliquer aux paramètres DSP plus importantes en provenance de simulation. Elle élève les activités du concepteur à partir des préoccupations étroites des transitions sans heurts pour les problèmes plus élevées de l’intensité des effets occlusion et réverbération et la présence de sources dans la combinaison de configuration. Bien sûr, lorsque la situation le requiert, un seul filtre toujours disponible est simplement revenir en arrière pour choisir les paramètres DSP pour une source spécifique dans une situation spécifique.

## <a name="sound-design-in-project-acoustics"></a>Sonorisation dans le projet acoustique

Le package de projet acoustique s’intègre à chacun des composants décrites ci-dessus : un simulateur, un encodeur qui extrait les paramètres et génère l’élément multimédia acoustiques, audio DSP et une sélection de filtres. Sonorisation avec projet acoustique implique en choisissant l’option paramètres pour les filtres qui ajustent les paramètres de l’occlusion et réverbération dérivée de simulation et appliqué à l’audio DSP, avec des contrôles dynamiques exposées à l’intérieur de l’éditeur de jeu et le moteur audio.

## <a name="next-steps"></a>Étapes suivantes
* Essayer le paradigme de conception à l’aide de la [acoustique de projet Guide de démarrage rapide pour Unity](unity-quickstart.md) ou le [acoustique de projet Guide de démarrage rapide pour Unreal](unreal-quickstart.md)
* Explorez le [projet acoustique concevoir des contrôles pour Unity](unity-workflow.md) ou le [projet acoustique concevoir des contrôles pour Unreal](unreal-workflow.md)

