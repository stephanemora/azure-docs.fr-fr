---
title: Points de terminaison privés managés
description: Article qui explique ce que sont les points de terminaison privés managés dans Azure Synapse Analytics.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 08c6610541d987cddd7cf2aeb71c526cb2359598
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419553"
---
# <a name="synapse-managed-private-endpoints-preview"></a>Points de terminaison privés managés Synapse (préversion)

Cet article explique ce que sont les points de terminaison privés managés dans Azure Synapse Analytics.

## <a name="managed-private-endpoints"></a>Points de terminaison privés managés

Les points de terminaison privés managés sont des points de terminaison privés créés sur le réseau virtuel d’espace de travail managé qui établissent une liaison privée vers des ressources Azure. Azure Synapse gère ces points de terminaison privés à votre place.

Azure Synapse prend en charge les liaisons privées. Une liaison privée vous permet d’accéder à des services Azure (tels que Stockage Azure, Azure Cosmos DB et Azure SQL Data Warehouse) et à des services clients/partenaires hébergés par Azure à partir de votre réseau virtuel Azure en toute sécurité.

Quand vous utilisez une liaison privée, le trafic entre votre réseau virtuel et l’espace de travail transite intégralement par le réseau principal de Microsoft. Une liaison privée assure une protection contre les risques liés à l’exfiltration des données. Vous établissez une liaison privée vers une ressource en créant un point de terminaison privé.

Un point de terminaison privé utilise une adresse IP privée de votre réseau virtuel pour placer de fait le service sur votre réseau virtuel. Les points de terminaison privés sont mappés à une ressource spécifique dans Azure, et non à l’ensemble du service. Les clients peuvent limiter la connectivité à une ressource spécifique approuvée par leur organisation. Apprenez-en davantage sur [les liaisons privées et les points de terminaison privés](https://docs.microsoft.com/azure/private-link/).

>[!IMPORTANT]
>Les points de terminaison privés managés sont uniquement pris en charge dans les espaces de travail Azure Synapse avec un réseau virtuel d’espace de travail managé.
>[!NOTE]
>Nous vous recommandons de créer des points de terminaison privés managés pour vous connecter à toutes vos sources de données Azure. Tout le trafic sortant en provenance du réseau virtuel d’espace de travail managé sera bloqué à l’avenir.

Une connexion de point de terminaison privé est créée dans un état « en attente » quand vous créez un point de terminaison privé managé dans Azure Synapse. Un workflow d’approbation est lancé. Le propriétaire de la ressource de liaison privée est responsable de l’approbation ou du refus de la connexion.

Si le propriétaire approuve la connexion, la liaison privée est établie. S’il la refuse, la liaison privée n’est pas établie. Dans les deux cas, le point de terminaison privé managé est mis à jour avec l’état de la connexion.

Seule une instance de point de terminaison privé managé dans un état approuvé peut envoyer du trafic vers une ressource de liaison privée donnée.

## <a name="managed-private-endpoints-for-sql-pool-and-sql-on-demand"></a>Points de terminaison privés managés pour pool SQL et SQL à la demande

Le pool SQL et SQL à la demande sont des fonctionnalités d’analytique dans votre espace de travail Azure Synapse. Ces fonctionnalités utilisent une infrastructure multi-locataire qui n’est pas déployée sur le [réseau virtuel d’espace de travail managé](./synapse-workspace-managed-vnet.md).

Quand un espace de travail est créé, Azure Synapse crée deux points de terminaison privés managés vers le pool SQL et SQL à la demande dans cet espace de travail. 

Ces deux points de terminaison privés managés sont listés dans Azure Synapse Studio. Sélectionnez **Gérer** dans le volet de navigation gauche, puis sélectionnez **Réseaux virtuels managés** pour les afficher dans Studio.

Le point de terminaison privé managé qui cible le pool SQL se nomme *synapse-ws-sql--\<nom_espace_de_travail\>* , tandis que celui qui cible SQL à la demande se nomme *synapse-ws-sqlOnDemand--\<nom_espace_de_travail\>* .
![Points de terminaison privés managés pour pool SQL et SQL à la demande](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Ces deux points de terminaison privés managés sont créés automatiquement pour vous quand vous créez votre espace de travail Azure Synapse. Vous n’êtes pas facturé pour ces deux points de terminaison privés managés.

## <a name="next-steps"></a>Étapes suivantes

[Créer des points de terminaison privés managés à vos sources de données](./how-to-create-managed-private-endpoints.md)
