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
ms.openlocfilehash: 58532e07b80e0084666e6847d5bd62313a51ee76
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207770"
---
# <a name="private-network-access-with-azure-private-link-preview"></a>Accès réseau privé avec Azure Private Link (préversion) 

[Azure Private Link](/azure/private-link/private-link-overview)  est un service qui vous permet d’accéder aux ressources Azure (par exemple  [Azure Event Hubs](/azure/event-hubs/event-hubs-about),  [Stockage Azure](/azure/storage/common/storage-introduction) et  [Azure Cosmos DB](/azure/cosmos-db/introduction)) ainsi qu’aux services de partenaire ou de client hébergés par Azure via un point de terminaison privé dans votre  [réseau virtuel (VNet) Azure](/azure/virtual-network/virtual-networks-overview). 

De même, vous pouvez utiliser des points de terminaison privés pour vos Time Series Insights afin de permettre aux clients situés dans votre réseau virtuel d’accéder en toute sécurité à l’instance via une liaison privée. 

## <a name="about-the-private-endpoints"></a>À propos des points de terminaison privés

Le point de terminaison privé utilise une adresse IP de l’espace d’adressage de votre réseau virtuel Azure. Le trafic entre un client de votre réseau privé et l’instance Time Series Insights traverse le réseau virtuel et une liaison privée sur le réseau principal de Microsoft, ce qui élimine son exposition à l’Internet public. Voici une représentation visuelle de ce système : 

[![DNS de liaisons privées de séries chronologiques](media/private-links/tsi-dns.png)](media/private-links/tsi-dns.png#lightbox)

Les clients peuvent également bloquer l’accès public à un environnement TSI afin que celui-ci ne soit accessible qu’à partir du réseau virtuel. La configuration d’un point de terminaison privé pour votre instance Time Series Insights vous permet de sécuriser celle-ci et d’éliminer l’exposition publique, tout en évitant l’exfiltration de données à partir de votre réseau virtuel. 

[![Réseau de liaisons privées de séries chronologiques](media/private-links/tsi-network-access.png)](media/private-links/tsi-network-access.png#lightbox)

Une fois qu’un point de terminaison privé est activé et que l’accès public est restreint, le client doit utiliser une autre adresse pour l’Explorateur TSI pour accéder à l’environnement TSI. Cette adresse se trouve dans la section Vue d’ensemble du portail Azure. 

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations, consultez le [Guide pratique pour configurer des points de terminaison privés pour un environnement TSI](./how-to-private-links.md) 