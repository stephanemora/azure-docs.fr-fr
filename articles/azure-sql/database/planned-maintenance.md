---
title: Planifier des événements de maintenance Azure
description: Apprenez à anticiper les événements de maintenance planifiée dans Azure SQL Database et Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: mathoma
ms.date: 3/23/2021
ms.openlocfilehash: 7de0db8245908e8342abbbe6a8f7cc4f2359e7f5
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112017862"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Planifier les événements de maintenance Azure dans Azure SQL Database et Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Apprenez à anticiper les événements de maintenance planifiée sur votre base de données dans Azure SQL Database et Azure SQL Managed Instance.

## <a name="what-is-a-planned-maintenance-event"></a>Qu’est-ce qu’un événement de maintenance planifiée ?

Pour assurer la sécurité, la conformité, la stabilité et les performances des services Azure SQL Database et Azure SQL Managed Instance, des mises à jour sont effectuées presque en permanence via les composants du service. Grâce à l’architecture de service moderne et robuste, ainsi qu’à des technologies novatrices telles que de la [mise à jour corrective à chaud](https://aka.ms/azuresqlhotpatching), la majorité des mises à jour sont entièrement transparentes et sans incidence en termes de disponibilité des services. Néanmoins, quelques rares types de mises à jour entraînent des interruptions de service courtes et nécessitent un traitement spécial. 

Pendant les maintenances planifiées, les membres du quorum de base de données passent hors connexion l’un après l’autre. Ce comportement permet d’avoir un réplica principal chargé de répondre. Pour les bases de données Critique pour l’entreprise et Premium, au moins un réplica secondaire est également en ligne pour éviter tout temps d’arrêt du client. Lorsque le réplica principal doit être mis hors ligne, un processus de reconfiguration se produit. Pour les bases de données Critique pour l’entreprise et Premium, l’un des réplicas secondaires devient le nouveau réplica principal. Pour les bases de données Usage général, Standard et De base, le réplica principal est déplacé vers un autre nœud de calcul sans état avec une capacité libre suffisante.

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Que se passe-t-il pendant un événement de maintenance planifiée ?

L’événement de maintenance peut produire une ou plusieurs reconfigurations, selon la constellation des réplicas principaux et secondaires au début de l’événement de maintenance. En moyenne, il se produit 1,7 reconfiguration par événement de maintenance planifiée. Les reconfigurations se terminent généralement dans les 30 secondes. La moyenne est de huit secondes. Si elle est déjà connectée, votre application doit se reconnecter au nouveau réplica principal de votre base de données. Si une nouvelle connexion est tentée alors que la base de données subit une reconfiguration avant que le nouveau réplica principal soit en ligne, vous obtenez l’erreur 40613 (base de données non disponible) : *« La base de données "{databasename}" sur le serveur "{servername}" n'est pas disponible actuellement. Veuillez réessayer la connexion ultérieurement. »* Si votre base de données a une requête durable, cette dernière sera interrompue pendant une reconfiguration et devra ensuite être redémarrée.

## <a name="how-to-simulate-a-planned-maintenance-event"></a>Comment simuler un événement de maintenance planifiée ?

En veillant à ce que votre application cliente soit résiliente aux événements de maintenance avant son déploiement en production, vous réduirez le risque d’erreurs et contribuerez à la disponibilité de l’application pour vos utilisateurs finaux. Vous pouvez tester le comportement de votre application cliente pendant les événements de maintenance planifiée en [testant la résilience aux erreurs d’application](./high-availability-sla.md#testing-application-fault-resiliency) par le biais de PowerShell, de l’interface CLI ou de l’API REST. Consultez également [Lancement du basculement manuel](https://aka.ms/mifailover-techblog) pour Managed Instance. Il produira un comportement identique à celui d'un événement de maintenance qui met le réplica principal hors connexion.

## <a name="retry-logic"></a>Logique de nouvelle tentative

Toute application de production cliente qui se connecte à un service de base de données cloud doit implémenter une [logique de nouvelle tentative](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) de connexion robuste. Cela permettra de rendre les reconfigurations transparentes pour les utilisateurs finaux, ou du moins d’en minimiser les effets négatifs.

## <a name="resource-health"></a>Intégrité des ressources

Si votre base de données subit des échecs de connexion, examinez la fenêtre [Resource Health](../../service-health/resource-health-overview.md#get-started) dans le [portail Azure](https://portal.azure.com) pour connaître l’état actuel. La section Health History (Historique d’intégrité) contient le motif du temps d’arrêt pour chaque événement (le cas échéant).

## <a name="maintenance-window-feature"></a>Fonctionnalité de fenêtre de maintenance

La fonctionnalité de fenêtre de maintenance permet de configurer des planifications de fenêtre de maintenance prévisibles pour les bases de données Azure SQL et les instances managées SQL éligibles. Pour plus d’informations, consultez [Fenêtre de maintenance](maintenance-window.md).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Resource Health](resource-health-to-troubleshoot-connectivity.md) pour Azure SQL Database et Azure SQL Managed Instance.
- Pour plus d’informations sur la logique de nouvelle tentative, consultez [Logique de nouvelle tentative pour les erreurs temporaires](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
- Configurez les planifications de fenêtre de maintenance avec la fonctionnalité [Fenêtre de maintenance](maintenance-window.md).