---
title: Présentation d’Azure Arc avec PostgreSQL Hyperscale
description: Présentation d’Azure Arc avec PostgreSQL Hyperscale
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: d43753084f79184b338b16223ed45cb00a2dfb33
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732455"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Présentation d’Azure Arc avec PostgreSQL Hyperscale

Azure Arc enabled PostgreSQL Hyperscale est un des services de base de données disponibles dans le cadre des services de données activés pour Azure Arc. Azure Arc vous permet d'exécuter des services de données Azure localement, à la périphérie et dans des clouds publics à l'aide de Kubernetes et de l'infrastructure de votre choix. La proposition de valeur des services de données activés pour Azure Arc s’articule autour des éléments suivants :
- Toujours à jour
- Mise à l’échelle élastique
- Provisionnement en libre-service
- Gestion unifiée
- Prise en charge de scénarios déconnectés

Vous pouvez approfondir le sujet en lisant :
- [Que sont les services de données avec Azure Arc ?](overview.md)
- [Modes et exigences de connectivité](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Pour en savoir plus sur ces fonctionnalités, vous pouvez également vous référer à cet épisode de Data Exposed.
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-Arc-Enabled-PostgreSQL-Hyperscale--Data-Exposed/player?format=ny]

## <a name="compare-solutions"></a>Comparer les solutions

Cette section décrit en quoi PostgreSQL Hyperscale activé par Azure Arc diffère d’Azure Database pour PostgreSQL Hyperscale (Citus).

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Hyperscale (Citus) - Azure Database pour PostgreSQL

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Azure SQL Database pour PostgreSQL Hyperscale (Citus)":::

Il s’agit du facteur de forme hyperscale du moteur de base de données Postgres disponible en tant que service dans Azure (PaaS). Il s’appuie sur l’extension Citus qui permet l’expérience hyperscale. Dans ce facteur de forme, le service s’exécute dans les centres de données Microsoft et il est exploité par Microsoft.

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL Hyperscale activé par Azure Arc

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="PostgreSQL Hyperscale activé par Azure Arc":::

Il s’agit du facteur de forme hyperscale du moteur de base de données Postgres disponible avec les services de données activés pour Azure Arc. Il s’appuie également sur l’extension Citus qui permet l’expérience hyperscale. Dans ce facteur de forme, nos clients fournissent l’infrastructure qui héberge les systèmes et les exploitent.

## <a name="next-steps"></a>Étapes suivantes
- **Faites un essai.** Démarrez rapidement avec [Démarrage rapide d’Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) sur Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou sur une machine virtuelle Azure. 

- **Créez votre propre cluster.** Pour créer sur votre propre cluster Kubernetes, procédez comme suit : 
   1. [Installer les outils clients](install-client-tools.md)
   2. [Créer le contrôleur de données Azure Arc](create-data-controller.md)
   3. [Créer un groupe de serveurs Azure Database pour PostgreSQL Hyperscale sur Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [En savoir plus sur les services de données activés par Azure Arc](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Découvrir Azure Arc](https://aka.ms/azurearc)
