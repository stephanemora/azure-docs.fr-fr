---
title: Vue d’ensemble de Relais Azure Fluid
description: Vue d’ensemble de l’infrastructure Fluid et de Relais Azure Fluid.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 08/19/2021
ms.topic: how-to
ms.service: azure-fluid
ms.openlocfilehash: 30c0e289951555a5ffcd1d0776e0b273f8573da7
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661889"
---
# <a name="azure-fluid-relay-overview"></a>Vue d’ensemble de Relais Azure Fluid

> [!NOTE]
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

L’[infrastructure Fluid](https://fluidframework.com/) est une infrastructure open source indépendante de la plateforme. [Relais Azure Fluid](../overview/overview.md) est une offre gérée pour l’infrastructure Fluid, qui permet aux développeurs de créer des expériences de collaboration en temps réel et de répliquer l’état des clients JavaScript connectés en temps réel.

## <a name="what-is-the-fluid-framework"></a>Qu’est-ce que l’infrastructure Fluid ?

L’infrastructure Fluid est une collection de bibliothèques clientes permettant de distribuer et de synchroniser un état partagé. Ces bibliothèques permettent à plusieurs clients de créer et d’utiliser simultanément des structures de données partagées à l’aide de modèles de codage similaires à ceux utilisés pour travailler avec des données locales.

Vous trouvez plus de documentation sur [FluidFramework.com](https://fluidframework.com).

## <a name="why-fluid"></a>Pourquoi utiliser Fluid ?

Parce qu’il est difficile de garantir des expériences collaboratives à faible latence !

L’infrastructure Fluid offre :

- Un modèle d’application centré sur le client avec une persistance des données ne nécessitant aucun code serveur personnalisé.
- Des structures de données distribuées avec des modèles de programmation familiers.
- Une très faible latence.

Les développeurs de Microsoft ont intégré la collaboration à de nombreuses applications, mais beaucoup de ces applications nécessitent une logique côté serveur spécifique pour pouvoir gérer l’expérience collaborative. L’infrastructure Fluid est le résultat de l’investissement de Microsoft dans la réduction de la complexité liée à la création d’applications collaboratives.

Et si vous n’aviez absolument pas besoin d’investir dans du code serveur ? Imaginez que vous puissiez utiliser un serveur à usage général conçu pour être léger et économique. Imaginez que tout votre développement soit axé sur l’expérience du client et que la synchronisation des données soit gérée pour vous. C’est la promesse de Fluid.

## <a name="focused-on-the-client-developer"></a>Axée sur le développeur du client

Les applications construites avec l’infrastructure Fluid ne nécessitent aucun code personnalisé sur le serveur pour permettre des scénarios de synchronisation de données sophistiqués, comme la saisie en temps réel dans des éditeurs de texte. Les développeurs clients peuvent se concentrer sur les expériences des clients tout en laissant à Fluid le soin de synchroniser les données.

L’infrastructure Fluid fonctionne avec l’infrastructure d’application de votre choix. Que vous préfériez JavaScript ou une infrastructure comme React, Angular ou Vue, l'infrastructure Fluid rend la création d’expériences collaboratives simple et flexible.

## <a name="how-fluid-works"></a>Fonctionnement de Fluid

Fluid a été conçue pour offrir des expériences collaboratives avec des performances exceptionnelles. Pour atteindre cet objectif, l’équipe a veillé à ce que la logique du serveur soit aussi simple et légère que possible. Cette approche a permis d’assurer une synchronisation quasi instantanée entre les clients, avec des coûts de serveur réduits.

Pour que le serveur reste simple, chaque client Fluid est responsable de son propre état. Alors que les systèmes précédents conservent une source de vérité sur le serveur, le service Fluid est chargé de recevoir les opérations de données, de les séquencer et de renvoyer les opérations séquencées aux clients. Chaque client est capable d’utiliser cette séquence pour produire de manière indépendante et précise l’état actuel, quel que soit l’ordre dans lequel il reçoit les opérations.

Les étapes suivantes montrent un flux standard.

1. Le code client modifie les données localement.
1. Le runtime Fluid envoie cette modification au service Fluid.
1. Le service Fluid séquence cette opération et la diffuse à tous les clients.
1. Le runtime Fluid incorpore cette opération dans les données locales et déclenche un événement « valueChanged ».
1. Le code client gère cet événement (mise à jour de l’affichage, exécution de la logique métier).

## <a name="getting-to-version-10"></a>Passage à la version 1.0

La technologie de base de l’infrastructure Fluid est mature et stable. Cependant, les couches construites sur cette base sont toujours en cours de réalisation. Au cours des prochains mois, nous ferons évoluer les API, ajouterons de nouvelles fonctionnalités et travaillerons à simplifier davantage l’utilisation de l’infrastructure. Ces modifications sont motivées par l’utilisation de Fluid par Microsoft en interne et par les exigences que nous recueillons auprès des développeurs qui utilisent actuellement Fluid.

L’infrastructure Fluid n’est pas encore prête à alimenter des solutions de qualité de production. Mais nous sommes ravis de la proposer dès maintenant en open source pour donner aux développeurs l’occasion d’explorer, d’apprendre et de contribuer à la fois par le biais de commentaires et par une participation directe.
