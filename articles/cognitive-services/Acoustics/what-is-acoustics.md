---
title: Qu’est-ce que Project Acoustics ?
titlesuffix: Azure Cognitive Services
description: Le plug-in Project Acoustics Unity fournit une occlusion, une réverbération et une spatialisation pour les projets ciblant la réalité virtuelle et les écrans traditionnels.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 8305eca478854eeff29268a86e4e49b697261ca2
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868257"
---
# <a name="what-is-project-acoustics"></a>Qu’est-ce que Project Acoustics ?
Le plug-in Project Acoustics Unity fournit une occlusion, une réverbération et une spatialisation pour les projets ciblant la réalité virtuelle et les écrans traditionnels. Il fournit un moyen de concevoir des acoustiques de jeux qui calquent des intentions de concepteur de couches via une simulation d’ondes basée sur le moteur physique.

## <a name="why-use-acoustics-in-virtual-environments"></a>Pourquoi utiliser Acoustics dans des environnements virtuels ?
Les humains utilisent des signaux audiovisuels pour comprendre le monde qui les entourent. Dans les mondes virtuels, l’association d’un audio spatial avec des acoustiques augmente l’immersion de l’utilisateur. L’outil Acoustics décrit ici analyse les mondes virtuels pour créer une simulation acoustique réaliste et il prend en charge un processus de conception post-simulation. L’analyse inclut la géométrie et les matériaux pour chaque surface dans le monde. La simulation inclut des paramètres tels que la direction d’arrivée (portage), la puissance de réverbération, la durée de dégradation atténuation et les effets d’occlusion et d’obstruction.

## <a name="how-does-this-approach-to-acoustics-work"></a>Comment fonctionne cette approche acoustique ?
Le système s’appuie sur un calcul hors connexion du monde virtuel, ce qui permet une simulation plus complexe qu’avec une analyse effectuée lors de l’exécution. Le calcul hors connexion génère une table de recherche des paramètres acoustiques. Un concepteur spécifie les règles appliquées aux paramètres lors de l’exécution. L’ajustement de ces règles permet d’obtenir des effets hyper réalistes pour une haute intensité émotionnelle ou des scènes hypo-réalistes pour plus de sons audios en arrière-plan.

## <a name="design-process-comparison"></a>Comparaison des processus de conception
Le plug-in Project Acoustics prend en charge un nouveau processus de conception pour les acoustiques dans les scènes Unity. Pour expliquer ce nouveau processus de conception, comparerons-le à l’une des approches acoustiques populaires d’aujourd'hui.

### <a name="typical-approach-to-acoustics-today"></a>Approche courante actuelle pour les acoustiques
Dans une approche courante actuelle pour les acoustiques, vous dessinez des volumes de réverbération :

![Vue de conception](media/reverbZonesAltSPace2.png)

Réglez ensuite les paramètres pour chaque zone :

![Vue de conception](media/TooManyReverbParameters.png)

Enfin, ajoutez une logique de lancer de rayon pour obtenir le filtrage correct de l’occlusion et de l’obstruction tout au long de la scène, et une logique de recherche de chemin d’accès pour le portage. Ce code peut ajouter un coût d’exécution. Il a également des problèmes avec la fluidité autour des coins et a des cas extrêmes dans les scènes formées irrégulièrement.

### <a name="an-alternative-approach-with-physics-based-design"></a>Une autre approche avec la conception physique
Avec l’approche fournie par le plug-in Project Acoustics Unity, vous fournissez la forme et les matériaux d’une scène statique. Étant donné que la scène est voxelisée et que le processus n’utilise pas le lancer de rayon, vous n’avez pas besoin de fournir un maillage acoustique simplifié ou étanche. De même, il n’est pas nécessaire de marquer la scène avec des volumes de réverbération. Le plug-in charge la scène sur le cloud, où il utilise une simulation d’ondes basée sur le moteur physique. Le résultat est intégré à votre projet sous la forme d’une table de recherche et il peut être modifié pour les effets esthétiques ou de l’expérience de jeu.

![Vue de conception](media/GearsWithVoxels.jpg)

## <a name="requirements"></a>Configuration requise
* Unity 2018.2+ pour les créations acoustiques, et Unity 5.2+ pour la conception sonore et le déploiement
* Éditeur Unity Windows 64 bits
* Abonnement Azure Batch pour les créations acoustiques
* L’exécution du script de Unity doit être définie sur « Équivalent à .NET 4.x »

## <a name="platform-support"></a>Prise en charge de plateformes
* Bureau Windows (x 86 et AMD64)
* Windows UWP (x86, AMD64 et ARM)
* Android (x 86 et ARM64)

## <a name="download"></a>Téléchargement
Si vous êtes intéressé par l’évaluation du plug-in Acoustics, inscrivez-vous [ici](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) pour rejoindre la préversion Designer Preview.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur le [processus de conception](design-process.md)
* Bien démarrer avec [l’intégration acoustique dans votre projet Unity](getting-started.md)

