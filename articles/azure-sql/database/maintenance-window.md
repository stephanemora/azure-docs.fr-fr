---
title: Fenêtre de maintenance
description: Découvrez comment configurer la fenêtre de maintenance Azure SQL Database et Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/02/2021
ms.openlocfilehash: 9dc4d17ea95362dd915bd1dfdfd82f4cdec611b8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692808"
---
# <a name="maintenance-window-preview"></a>Fenêtre de maintenance (préversion)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

La fonctionnalité de fenêtre de maintenance permet de configurer des planifications de fenêtre de maintenance prévisibles pour [Azure SQL Database](sql-database-paas-overview.md) et [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md). 

Pour plus d’informations sur les événements de maintenance, consultez [Planifier les événements de maintenance Azure dans Azure SQL Database et Azure SQL Managed Instance](planned-maintenance.md).

## <a name="overview"></a>Vue d’ensemble

Azure effectue régulièrement des mises à jour de maintenance planifiée sur les ressources Azure SQL Database et SQL Managed Instance qui incluent souvent des mises à jour du matériel sous-jacent, des logiciels incluant le système d’exploitation sous-jacent et du moteur SQL. Pendant une mise à jour de maintenance, les ressources sont entièrement disponibles et accessibles, mais certaines des mises à jour de maintenance nécessitent un basculement, car Azure met les instances hors connexion pendant un bref moment afin d’appliquer les mises à jour de maintenance (huit secondes en moyenne).  Les mises à jour de maintenance planifiée se produisent une fois tous les 35 jours en moyenne, ce qui signifie que le client peut s’attendre à un événement de maintenance planifiée par mois par Azure SQL Database ou SQL Managed Instance, et uniquement pendant les créneaux de fenêtres de maintenance sélectionnés par le client.   

La fenêtre de maintenance est destinée aux charges de travail métier qui ne sont pas résilientes aux problèmes de connectivité intermittents pouvant résulter d'événements de maintenance planifiée.

La fenêtre de maintenance peut être configurée à l’aide du portail Azure, de PowerShell, de l’interface CLI ou de l’API Azure. Elle peut être configurée lors de la création ou pour des bases de données SQL et des instances managées SQL existantes.

### <a name="gain-more-predictability-with-maintenance-window"></a>Obtenir davantage de prévisibilité avec une fenêtre de maintenance

Par défaut, toutes les bases de données Azure SQL et toutes les bases de données d’instance managée sont mises à jour chaque jour uniquement entre 17h00 et 8h00 (heure locale) afin d’éviter toute interruption durant les heures de pointe. L’heure locale est déterminée par la [région Azure](https://azure.microsoft.com/global-infrastructure/geographies/) qui héberge la ressource. Vous pouvez ajuster les mises à jour de maintenance à une heure adaptée à votre base de données en choisissant parmi deux créneaux de fenêtres de maintenance supplémentaires :

* Fenêtre **par défaut**, de 17h00 à 8h00 heure locale du lundi au dimanche 
* Fenêtre semaine, de 22h00 à 6h00 heure locale, du lundi au jeudi
* Fenêtre week-end, de 22h00 à 6h00 heure locale, du vendredi au dimanche

Une fois la fenêtre de maintenance sélectionnée, toutes les mises à jour de maintenance planifiée ont lieu uniquement pendant la fenêtre de votre choix.   

> [!Note]
> En plus des mises à jour de maintenance planifiée, dans de rares circonstances, des événements de maintenance non planifiés peuvent entraîner une indisponibilité. 

### <a name="cost-and-eligibility"></a>Coût et éligibilité

Le choix d’une fenêtre de maintenance est gratuit pour les [types d’offre](https://azure.microsoft.com/support/legal/offer-details/) d’abonnement suivants : Paiement à l’utilisation, Fournisseur de solutions cloud (CSP), Microsoft Enterprise ou Contrat client Microsoft.

> [!Note]
> Une offre Azure correspond au type d’abonnement Azure que vous avez. Par exemple, un abonnement avec [tarifs de paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure en licence Open](https://azure.microsoft.com/en-us/offers/ms-azr-0111p/) et [Visual Studio Enterprise](https://azure.microsoft.com/en-us/offers/ms-azr-0063p/) sont tous des offres Azure. Chaque offre ou plan présente différentes conditions et avantages. Votre offre ou plan est affiché dans la vue d’ensemble de l’abonnement. Pour plus d’informations sur la manière de changer votre abonnement et basculer vers une autre offre, consultez [Changer d’offre pour votre abonnement Azure](/azure/cost-management-billing/manage/switch-azure-offer).

## <a name="advance-notifications"></a>Notifications préalables

Des notifications de maintenance peuvent être configurées afin d’alerter les clients concernant les prochains événements de maintenance planifiée 24 heures à l’avance, au moment de la maintenance et lorsque la fenêtre de maintenance est terminée. Pour plus d’informations, consultez [Notifications préalables](advance-notifications.md).

## <a name="availability"></a>Disponibilité

### <a name="supported-service-level-objectives"></a>Objectifs de niveau de service pris en charge

Le choix d’une fenêtre de maintenance autre que celle par défaut est disponible sur tous les objectifs de niveau de service **sauf** :
* Hyperscale 
* Gen4 vCore hérité
* De base, S0 et S1 
* DC, Fsv2, série M

### <a name="azure-region-support"></a>Prise en charge des régions Azure

Le choix d’une fenêtre de maintenance autre que celle par défaut est actuellement disponible dans les régions suivantes :

- Australie Est
- Australie Sud-Est
- Brésil Sud
- Centre du Canada
- USA Centre
- USA Est
- USA Est 2
- Japon Est
- NorthCentral US
- Europe Nord
- USA Centre Sud
- Asie Sud-Est
- Sud du Royaume-Uni
- Europe Ouest
- USA Ouest
- USA Ouest 2

## <a name="gateway-maintenance-for-azure-sql-database"></a>Maintenance de la passerelle pour Azure SQL Database

Pour tirer le meilleur parti des fenêtres de maintenance, vérifiez que vos applications clientes utilisent la stratégie de connexion de redirection. La redirection est la stratégie de connexion recommandée ; les clients établissent des connexions directement au nœud qui héberge la base de données, ce qui permet de réduire la latence et d’améliorer le débit.  

* Dans Azure SQL Database, toutes les connexions utilisant la stratégie de connexion proxy peuvent être affectées par la fenêtre de maintenance choisie et par une fenêtre de maintenance de nœud de passerelle. Toutefois, les connexions clientes utilisant la stratégie de connexion par redirection recommandée ne sont pas affectées par un basculement de maintenance de nœud de passerelle. 

* Dans Azure SQL Managed Instance, les nœuds de passerelle se trouvent [dans le cluster virtuel](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) et ont la même fenêtre de maintenance que l’instance managée. Ainsi, l’utilisation de la stratégie de connexion proxy n’expose pas les connexions à une fenêtre de maintenance supplémentaire.

Pour plus d’informations sur la stratégie de connexion du client dans Azure SQL Database, consultez [Stratégie de connexion Azure SQL Database](../database/connectivity-architecture.md#connection-policy). 

Pour plus d’informations sur la stratégie de connexion du client dans Azure SQL Managed Instance, consultez [Types de connexion Azure SQL Managed Instance](../../azure-sql/managed-instance/connection-types-overview.md).


## <a name="next-steps"></a>Étapes suivantes

* [Notifications préalables](advance-notifications.md)
* [Configurer la fenêtre de maintenance](maintenance-window-configure.md)

## <a name="learn-more"></a>En savoir plus

* [FAQ sur la fenêtre de maintenance](maintenance-window-faq.yml)
* [Azure SQL Database](sql-database-paas-overview.md) 
* [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)
* [Planifier les événements de maintenance Azure dans Azure SQL Database et Azure SQL Managed Instance](planned-maintenance.md)




