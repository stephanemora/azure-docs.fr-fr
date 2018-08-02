---
title: Recommandations Azure Advisor en matière de performances | Microsoft Docs
description: Utilisez Advisor pour optimiser les performances de vos déploiements Azure.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 3331c795cbb1c45820d4c86d287ef57b54f0ae6b
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247640"
---
# <a name="advisor-performance-recommendations"></a>Recommandations Azure Advisor en matière de performances

Les recommandations d’Azure Advisor en matière de performances vous aident à optimiser la vitesse et la réactivité de vos applications stratégiques. Vous pouvez obtenir des recommandations en matière de performances à l’aide d’Advisor dans l’onglet **Performances** du tableau de bord d’Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Diminuer la durée de vie DNS dans votre profil Traffic Manager pour passer plus rapidement à des points de terminaison intègres

Les [paramètres TTL (durée de vie)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) de votre profil Traffic Manager permettent de spécifier l’intervalle de temps nécessaire avant de changer de point de terminaison lorsque celui-ci ne répond plus. Lorsque l’on réduit les valeurs TTL, les clients sont redirigés plus rapidement vers des points de terminaison opérationnels.

Azure Advisor identifie les profils Traffic Manager ayant une valeur TTL élevée et recommande de la régler à 20 secondes ou 60 secondes selon que le profil est configuré pour une [Bascule rapide](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Améliorer les performances de base de données avec SQL DB Advisor

Le conseiller vous offre une vue cohérente et consolidée des recommandations pour toutes vos ressources Azure. Il s’intègre à SQL Database Advisor pour vous proposer des recommandations en vue d’améliorer les performances de votre base de données SQL Azure. SQL Database Advisor évalue les performances de vos bases de données SQL Azure en analysant votre historique d’utilisation. Il propose alors les recommandations les plus adaptées pour exécuter la charge de travail standard de la base de données. 

> [!NOTE]
> Pour obtenir des recommandations, une base de données doit avoir été utilisée pendant environ une semaine et avoir fait l’objet d’une activité cohérente au cours de cette semaine. SQL Database Advisor peut plus facilement optimiser les modèles de requête cohérents que les pics d’activité aléatoires.

Pour plus d’informations sur SQL Database Advisor, consultez la page [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-redis-cache-performance-and-reliability"></a>Améliorer la fiabilité et les performances du Cache Redis

Advisor identifie les instances de Cache Redis où les performances peuvent être défavorablement affectées par une utilisation intensive de la mémoire, la charge du serveur, la bande passante réseau ou un grand nombre de connexions clientes. Il fournit également des recommandations quant aux meilleures pratiques pour vous éviter les problèmes potentiels. Pour plus d’informations concernant les recommandations de Cache Redis, consultez [Redis Cache Advisor](https://azure.microsoft.com/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="improve-app-service-performance-and-reliability"></a>Améliorer la fiabilité et les performances d’App Service

Le conseiller Azure intègre des recommandations quant aux meilleures pratiques pour améliorer votre expérience App Services et vous faire découvrir des fonctionnalités appropriées de la plateforme. Exemples de recommandations App Services :
* Détection des instances où les ressources de mémoire ou de processeur sont épuisées par des exécutions d’application avec options d’atténuation.
* Détection des instances où la colocalisation de ressources telles que les applications web et les bases de données peut améliorer les performances et réduire les coûts. 

Pour plus d’informations sur les recommandations App Services, consultez [Meilleures pratiques pour Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Comment accéder aux recommandations en matière de performances dans le conseiller

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis ouvrez [Advisor](https://aka.ms/azureadvisordashboard).

2.  Dans le tableau de bord Advisor, cliquez sur l’onglet **Performances**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations d’Advisor, consultez les ressources suivantes :

* [Présentation du conseiller](advisor-overview.md)
* [Prise en main d’Advisor](advisor-get-started.md)
* [Recommandations du conseiller en matière de coûts](advisor-performance-recommendations.md)
* [Recommandations du conseiller en matière de haute disponibilité](advisor-high-availability-recommendations.md)
* [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md)

