---
title: Liaisons privées – Azure Time Series Insights Gen2 | Microsoft Docs
description: Vue d’ensemble des liaisons privées et de la restriction d’accès public dans Azure Time Series Insights Gen2.
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: 7005028eb61c53788e7283b9a0af5878b3e3961b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130214792"
---
# <a name="private-network-access-with-azure-private-link-preview"></a>Accès réseau privé avec Azure Private Link (préversion) 

[Azure Private Link](../private-link/private-link-overview.md)  est un service qui vous permet d’accéder aux ressources Azure (par exemple  [Azure Event Hubs](../event-hubs/event-hubs-about.md),  [Stockage Azure](../storage/common/storage-introduction.md) et  [Azure Cosmos DB](../cosmos-db/introduction.md)) ainsi qu’aux services de partenaire ou de client hébergés par Azure via un point de terminaison privé dans votre  [réseau virtuel (VNet) Azure](../virtual-network/virtual-networks-overview.md). 

De même, vous pouvez utiliser des points de terminaison privés pour vos Time Series Insights afin de permettre aux clients situés dans votre réseau virtuel d’accéder en toute sécurité à l’instance via une liaison privée. 

## <a name="about-the-private-endpoints"></a>À propos des points de terminaison privés

Le point de terminaison privé utilise une adresse IP de l’espace d’adressage de votre réseau virtuel Azure. Le trafic entre un client de votre réseau privé et l’instance Time Series Insights traverse le réseau virtuel et une liaison privée sur le réseau principal de Microsoft, ce qui élimine son exposition à l’Internet public. Voici une représentation visuelle de ce système : 

[![DNS de liaisons privées de séries chronologiques](media/private-links/tsi-dns.png)](media/private-links/tsi-dns.png#lightbox)

Les clients peuvent également bloquer l’accès public à un environnement TSI afin que celui-ci ne soit accessible qu’à partir du réseau virtuel. La configuration d’un point de terminaison privé pour votre instance Time Series Insights vous permet de sécuriser celle-ci et d’éliminer l’exposition publique, tout en évitant l’exfiltration de données à partir de votre réseau virtuel. 

[![Réseau de liaisons privées de séries chronologiques](media/private-links/tsi-network-access.png)](media/private-links/tsi-network-access.png#lightbox)

Une fois qu’un point de terminaison privé est activé et que l’accès public est restreint, le client doit utiliser une autre adresse pour l’Explorateur TSI pour accéder à l’environnement TSI. Cette adresse se trouve dans la section Vue d’ensemble du portail Azure. 

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations, consultez le [Guide pratique pour configurer des points de terminaison privés pour un environnement TSI](./how-to-private-links.md)