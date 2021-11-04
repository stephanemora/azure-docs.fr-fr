---
title: Expériences de chaos dans Azure Chaos Studio
description: Comprendre le concept d’expérience de chaos dans Azure Chaos Studio. Quels sont les éléments d’une expérience de chaos ? Comment puis-je créer une expérience de chaos ?
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 2c84b9908eb361b4dfb57bb87d2d6fe2f828982d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097055"
---
# <a name="chaos-experiments"></a>Expériences de chaos

Dans Azure Chaos Studio, vous créez et exécutez des expériences de chaos. Une expérience de chaos est une ressource Azure qui décrit les erreurs qui doivent être exécutées et les ressources sur lesquelles ces erreurs doivent être exécutées. Un expérience est divisée en deux sections :
- **Sélecteurs** : les sélecteurs sont des groupes de ressources cibles pour lesquelles des erreurs ou d’autres actions sont exécutées. Un sélecteur vous permet de regrouper logiquement des ressources pour les réutiliser sur plusieurs actions. Par exemple, vous pouvez avoir un sélecteur nommé « AllNonProdEastUSVMs » dans lequel vous avez ajouté toutes les machines virtuelles de non-production dans la région Est des États-Unis. Vous pouvez ensuite appliquer la sollicitation du processeur suivie de la sollicitation de la mémoire virtuelle à ces machines virtuelles simplement en référençant le sélecteur.
- **Logique** : le reste de l’expérience décrit comment et quand exécuter les erreurs. Une expérience est organisée en **étapes** qui s’exécutent l’une après l’autre. Chaque étape a une ou plusieurs **branches** qui s’exécutent en même temps. Les étapes et les branches vous permettent d’injecter plusieurs erreurs sur les ressources de votre environnement en parallèle. Chaque branche comporte une ou plusieurs **actions** qui sont soit les erreurs que vous souhaitez exécuter, soit des délais. Les **erreurs** sont des actions qui provoquent une interruption. La plupart des erreurs prennent un ou plusieurs **paramètres**, tels que la durée d’exécution de l’erreur ou la quantité de contrainte à appliquer.

![Diagramme montrant la disposition d’une expérience de chaos.](images/chaos-experiment.png)

## <a name="cross-subscription-and-cross-tenant-experiments"></a>Expériences inter-abonnements et inter-locataires

Une expérience de chaos est une ressource Azure déployée sur un abonnement, un groupe de ressources et une région. Vous pouvez utiliser le Portail Azure ou l’API REST de Chaos Studio pour créer, mettre à jour, démarrer, annuler et afficher l’état d’une expérience.

Les expériences de chaos peuvent cibler des ressources dans un abonnement différent de l’expérience tant que l’abonnement se trouve dans le même locataire Azure. Les expériences de chaos peuvent cibler des ressources dans une région différente de celle de l’expérience, à condition que la région soit une région prise en charge pour Chaos Studio.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous comprenez ce qu’est une expérience de chaos, vous êtes prêt à :
- [En savoir plus sur les erreurs et les actions](chaos-studio-faults-actions.md)
- [Créer et exécuter votre première expérience](chaos-studio-tutorial-service-direct.md)
