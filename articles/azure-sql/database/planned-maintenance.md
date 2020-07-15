---
title: Planification des événements de maintenance Azure
description: Apprenez à anticiper les événements de maintenance planifiée dans Azure SQL Database et Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: 5bdc3eb8c118c19f90ce1fd92ac5ee156719dacd
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85987206"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Planifier les événements de maintenance Azure dans Azure SQL Database et Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Apprenez à anticiper les événements de maintenance planifiée sur votre base de données dans Azure SQL Database et Azure SQL Managed Instance.

## <a name="what-is-a-planned-maintenance-event"></a>Qu’est-ce qu’un événement de maintenance planifiée ?

Pour chaque base de données, Azure SQL Database et Azure SQL Managed Instance gèrent un quorum de réplicas de base de données où un seul réplica est le réplica principal. À tout moment, un réplica principal doit être en service et en ligne et au moins un réplica secondaire doit être sain. Pendant les maintenances planifiées, les membres du quorum de base de données passent hors connexion l’un après l’autre. Ce comportement permet d’avoir un réplica principal chargé de répondre et au moins un réplica secondaire en ligne pour assurer l’absence de temps d’arrêt du client. Lorsque le réplica principal doit être mis hors connexion, un processus de reconfiguration/basculement se produit et un des réplicas secondaires devient le nouveau réplica principal.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Que se passe-t-il pendant un événement de maintenance planifiée ?

Les reconfigurations ou les basculements se terminent généralement dans les 30 secondes. La moyenne est de huit secondes. Si elle est déjà connectée, votre application doit se reconnecter au nouveau réplica principal sain de votre base de données. Si une nouvelle connexion est tentée alors que la base de données subit une reconfiguration avant que le nouveau réplica principal soit en ligne, vous obtenez l’erreur 40613 (base de données non disponible) : « La base de données "{databasename}" sur le serveur "{servername}" n’est pas disponible actuellement. Veuillez réessayer la connexion ultérieurement. » Si votre base de données a une requête durable, cette dernière sera interrompue pendant une reconfiguration et devra ensuite être redémarrée.

## <a name="retry-logic"></a>Logique de nouvelle tentative

Toute application de production cliente qui se connecte à un service de base de données cloud doit implémenter une [logique de nouvelle tentative](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) de connexion robuste. Cela permet d’atténuer ces situations et doit généralement rendre les erreurs transparentes pour l’utilisateur final.

## <a name="frequency"></a>Fréquence

En moyenne, 1,7 événement de maintenance planifiée se produit par mois.

## <a name="resource-health"></a>Intégrité des ressources

Si votre base de données subit des échecs de connexion, examinez la fenêtre [Resource Health](../../service-health/resource-health-overview.md#get-started) dans le [portail Azure](https://portal.azure.com) pour connaître l’état actuel. La section Health History (Historique d’intégrité) contient le motif du temps d’arrêt pour chaque événement (le cas échéant).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Resource Health](resource-health-to-troubleshoot-connectivity.md) pour Azure SQL Database et Azure SQL Managed Instance.
- Pour plus d’informations sur la logique de nouvelle tentative, consultez [Logique de nouvelle tentative pour les erreurs temporaires](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
