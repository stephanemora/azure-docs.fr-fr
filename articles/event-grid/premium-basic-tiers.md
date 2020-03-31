---
title: Niveaux Premium et De base d’Azure Event Grid
description: Cet article décrit la différence entre les niveaux Premium et De base d’Azure Event Grid, et quand les utiliser
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79300333"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Niveaux Premium et De base d’Azure Event Grid
Azure Event Grid a deux niveaux : **Premium** et **De base**. Le niveau De base utilise une tarification basée sur la consommation ou le paiement à l’utilisation. Il vous fournit tous les outils de publication/d’abonnement de base dont vous avez besoin pour utiliser Event Grid dans le cadre des modèles de programmation basés sur les événements. Le niveau Premium va encore plus loin avec des fonctionnalités de sécurité destinées à l’entreprise. Le niveau Premium est en phase de **préversion**. Un grand nombre de ses fonctionnalités sont encore en cours de développement.

## <a name="overview"></a>Vue d’ensemble
Toutes les rubriques et tous les domaines personnalisés d’Event Grid appartiennent au niveau De base ou Premium. À partir de la version d’API `2020-04-01-preview`, vous pouvez choisir le niveau souhaité pour la création d’une rubrique ou d’un domaine. La valeur par défaut est le niveau De base. Les rubriques et les domaines créés à l’aide d’anciennes versions d’API correspondent par défaut au niveau De base. Pour changer le niveau tarifaire de vos rubriques et domaines, consultez [Guide pratique pour mettre à jour le niveau des rubriques et des domaines](update-tier.md).

## <a name="capabilities-and-features"></a>Capacités et fonctionnalités

Le tableau suivant décrit les différences entre les niveaux :

|       &nbsp;                                           | De base           | Premium        |
| ------------------------------------------------------ | --------------- | -------------- |
| Règles de pare-feu IP en entrée                          | PRÉVERSION  | PRÉVERSION |
| Étiquettes de service en sortie                                | PRÉVERSION  | PRÉVERSION |
| Intégration au réseau virtuel de point de terminaison privé en entrée          | Non disponible   | PRÉVERSION |

## <a name="availability"></a>Disponibilité
Dans la préversion initiale, les rubriques et domaines du niveau Premium avec intégration de point de terminaison privé sont disponibles dans les régions suivantes :

- USA Est
- USA Ouest 2
- USA Centre Sud

## <a name="pricing-and-quotas"></a>Tarifs et quotas
Consultez [Tarification d’Event Grid](https://azure.microsoft.com/pricing/details/event-grid/) pour les détails des prix de l’utilisation du niveau De base. Les tarifs du niveau Premium n’ont pas encore été annoncés, donc il est gratuit tant que les prix ne sont pas disponibles.

Les quotas existants sur le nombre de rubriques et de domaines ainsi que sur le débit s’appliquent aux ressources des niveaux Premium et De base tant que les tarifs du niveau Premium ne sont pas annoncés.

## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants :

- Pour passer du niveau De base au niveau Premium, consultez l’article [Mettre à jour le niveau tarifaire](update-tier.md). 
- Vous pouvez configurer le pare-feu IP de votre ressource Event Grid pour limiter l’accès au réseau Internet public à un ensemble spécifique d’adresses IP ou de plages d’adresses IP. Pour obtenir des instructions pas à pas, consultez [Configurer le pare-feu](configure-firewall.md).
- Vous pouvez configurer des points de terminaison privés pour limiter l’accès exclusivement aux réseaux virtuels sélectionnés. Pour obtenir des instructions pas à pas, consultez [Configurer des points de terminaison privés](configure-private-endpoints.md).