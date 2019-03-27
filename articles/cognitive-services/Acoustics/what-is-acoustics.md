---
title: Vue d’ensemble de Project Acoustics
titlesuffix: Azure Cognitive Services
description: Project Acoustics est un moteur acoustique pour des expériences 3D interactives, intégrant la simulation de la physique des ondes de baking acoustique avec des contrôles de conception interactifs.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 3d99ea5767c7b2e62f7228440201b4a9b6593b02
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58136598"
---
# <a name="what-is-project-acoustics"></a>Qu’est-ce que Project Acoustics ?
Project Acoustics est un moteur d’acoustique ondulatoire pour les expériences en trois dimensions interactives. Il modélise les effets ondulatoires comme la diffraction, le portage et les effets de réverbération dans des scènes complexes sans nécessiter le balisage manuel des zones. Il inclut également un moteur de jeu et l'intégration d’intergiciel audio. La philosophie de Project Acoustics est similaire à celle de l’éclairage statique : effectuer un baking physique détaillé hors connexion afin de fournir une base physique, et utiliser un runtime léger avec des contrôles de conception expressifs pour vous permettre d’atteindre vos objectifs artistiques.

![Vue de conception](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Utilisation de la physique ondulatoire pour l’acoustique interactive
Les méthodes acoustiques basées sur les rayons peuvent vérifier l’occlusion à l’aide d'un modèle de rayon source-auditeur unique ou piloter la réverbération en estimant le volume local de la scène à l’aide de quelques rayons. Cependant, ces techniques peuvent manquer de fiabilité, car l’occlusion est identique quelle que soit la taille de l’objet. Les rayons ne tiennent pas compte de la façon dont le son est dévié par les objets, un phénomène connu sous le nom de diffraction. La simulation de Project Acoustics saisit ces effets à l’aide d’une simulation basée sur les ondes sonores. Le résultat est plus fiable et prévisible.

L’innovation clé de Project Acoustics est de coupler la simulation acoustique avec les concepts traditionnels de création sonore. Elle traduit les résultats de simulation en paramètres DSP audio traditionnels pour l’occlusion, le portaling et la réverbération. Le concepteur utilise des contrôles sur ce processus de traduction. Pour plus de détails sur les technologies de base inhérents à Project Acoustics, visitez la [page du projet de recherche](https://www.microsoft.com/en-us/research/project/project-triton/).

![Vue de conception](media/wave-simulation.gif)

## <a name="setup"></a>Paramétrage
[L’intégration d’Unity dans Project Acoustics](unity-integration.md) se fait par glisser-déposer et inclut un plug-in de moteur audio Unity. Augmentez les contrôles de source audio Unity en joignant un composant de contrôles C# Project Acoustics à chaque objet audio.

[L’intégration d’Unreal dans Project Acoustics Unreal](unreal-integration.md) inclut des plug-ins d'éditeur et de jeu pour Unreal, ainsi qu’un plug-in de mélangeur Wwise. Un composant audio personnalisé étend les fonctionnalités familières de Wwise dans Unreal aux contrôles de conception acoustique en direct. Les contrôles de conception sont également exposés sur le plug-in de mélangeur dans Wwise.

## <a name="workflow"></a>Workflow
* **Pré-baking :** Commencez par préparer le baking en choisissant la géométrie qui répond le mieux à l’acoustique, par exemple en ignorant les puits de lumière. Ensuite, modifiez les affectations automatiques des matériaux et sélectionnez les zones de navigation pour guider l’échantillonnage de l’auditeur. Il n’y a pas de balisage manuel pour les zones de réverbération/portail/chambre.
* **Baking :** Une étape d'analyse exécutée localement effectue la voxélisation et d’autres analyses géométriques sur la scène en fonction des sélections ci-dessus. Les résultats sont visualisés dans l’éditeur dans le cadre de la vérification de la configuration de la scène. Lors de la soumission du baking, les données de voxel sont envoyées à Azure et vous récupérez une ressource de jeu acoustique.
* **Runtime :** Chargez la ressource dans votre niveau : vous pouvoir alors évaluer l’acoustique. Concevez l’acoustique en direct dans l'éditeur en utilisant les contrôles de code source qui permettent d’effectuer des réglages fins. Les contrôles peuvent également être commandés à partir d’un script.

## <a name="platforms"></a>Plateformes
Les plug-ins de runtime Project Acoustics peuvent actuellement être déployés sur les plateformes suivantes :
*  Windows
* Android
* Xbox One

## <a name="download"></a>Téléchargement
* [Plug-ins et exemples Project Acoustics](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
  * Pour les binaires et le support Xbox, contactez-nous par le biais du formulaire d’inscription ci-dessous
* [Forums Project Acoustics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics)
* [S’inscrire pour recevoir les mises à jour de Project Acoustics](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)

## <a name="next-steps"></a>Étapes suivantes
* Essayer un [démarrage rapide Project Acoustics pour Unity](unity-quickstart.md) ou pour [Unreal](unreal-quickstart.md)
* Explorer la [philosophie de conception sonore de Project Acoustics](design-process.md)

