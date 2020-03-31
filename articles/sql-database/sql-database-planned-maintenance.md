---
title: Planification des événements de maintenance Azure
description: Apprenez à anticiper les événements de maintenance planifiée pour votre instance Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: ba882176fbe17f7b74c786f421dde8fadd58d9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821322"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Planification des événements de maintenance Azure dans Azure SQL Database

Apprenez à anticiper les événements de maintenance planifiée pour votre base de données Azure SQL.

## <a name="what-is-a-planned-maintenance-event"></a>Qu’est-ce qu’un événement de maintenance planifiée ?

Pour chaque base de données, Azure SQL DB maintient un quorum de réplicas de base de données où un seul réplica est le réplica principal. Un réplica principal doit toujours être en service et en ligne, et au moins un réplica secondaire doit être sain. Pendant les maintenances planifiées, les membres du quorum de base de données passent hors connexion l’un après l’autre. Ce comportement permet d’avoir un réplica principal chargé de répondre et au moins un réplica secondaire en ligne pour assurer l’absence de temps d’arrêt du client. Lorsque le réplica principal doit être mis hors connexion, un processus de reconfiguration/basculement se produit et un des réplicas secondaires devient le nouveau réplica principal.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Que se passe-t-il pendant un événement de maintenance planifiée ?

Les reconfigurations/basculements sont généralement exécutés en 30 secondes, la moyenne étant de 8 secondes. Si elle est déjà connectée, votre application doit se reconnecter au nouveau réplica principal sain de votre base de données. Si une nouvelle connexion est tentée alors que la base de données subit une reconfiguration avant que le nouveau réplica principal soit en ligne, vous obtenez l’erreur 40613 (base de données non disponible) : « La base de données '{databasename}' sur le serveur '{servername}' n’est pas disponible actuellement. Veuillez réessayer la connexion ultérieurement. » Si votre base de données a une requête durable, cette dernière est interrompue pendant une reconfiguration et doit ensuite être redémarrée.

## <a name="retry-logic"></a>Logique de nouvelle tentative

Toute application de production cliente qui se connecte à un service de base de données cloud doit implémenter une [logique de nouvelle tentative](sql-database-connectivity-issues.md#retry-logic-for-transient-errors) de connexion robuste. Cela permet d’atténuer ces situations et doit généralement rendre les erreurs transparentes pour l’utilisateur final.

## <a name="frequency"></a>Fréquence

En moyenne, 1,7 événement de maintenance planifiée se produit par mois.

## <a name="resource-health"></a>Intégrité des ressources

Si votre base de données SQL rencontre des échecs de connexion, consultez la fenêtre [Resource Health](../service-health/resource-health-overview.md#get-started) dans le [portail Azure](https://portal.azure.com) pour connaître l’état actuel. La section Health History (Historique d’intégrité) contient le motif du temps d’arrêt pour chaque événement (le cas échéant).


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Resource Health](sql-database-resource-health.md) pour SQL Database
- Pour plus d’informations sur la logique de nouvelle tentative, consultez [Logique de nouvelle tentative pour les erreurs temporaires](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
