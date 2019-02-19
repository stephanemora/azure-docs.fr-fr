---
title: Notes de publication Azure SQL Data Warehouse | Microsoft Docs
description: Notes de publication pour Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
ms.openlocfilehash: 2ac60287c9d92ab6230e1dd6777504036e54492d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244672"
---
# <a name="azure-sql-data-warehouse-release-notes-and-documentation-updates"></a>Notes de publication et mises à jour de la documentation Azure SQL Data Warehouse

Azure SQL Data Warehouse (SQL DW) est un entrepôt de données d’entreprise cloud qui utilise le traitement massivement parallèle pour exécuter rapidement des requêtes complexes sur des pétaoctets de données. Utilisez SQL Data Warehouse comme composant clé d’une solution Big Data. Importez des données volumineuses dans SQL Data Warehouse avec des requêtes T-SQL PolyBase simples, puis utilisez le traitement massivement parallèle pour exécuter des analyses hautes performances. À mesure de vos intégrations et analyses, l’entrepôt de données deviendra la seule source pertinente sur laquelle votre activité peut compter pour obtenir des informations précises.

Cliquez sur les liens ci-dessous pour en savoir plus sur les nouvelles fonctionnalités et les améliorations que vous pouvez attendre dans la dernière version d’Azure SQL Data Warehouse. Vous pouvez vous attendre à recevoir ces mises à jour de service au cours de votre programme de maintenance planifié.

- [SQL Data Warehouse Version 10.0.10106.0 (janvier)](./release-notes-10-0-10106-0.md)
- [Décembre 2018](./release-notes-december-2018.md)
- [Octobre 2018](./release-notes-october-2018.md)
- [Septembre 2018](./release-notes-september-2018.md)
- [Août 2018](./release-notes-august-2018.md)
- [Juillet 2018](./release-notes-july-2018.md)
- [Juin 2018](./release-notes-june-2018.md)
- [Mai 2018](./release-notes-may-2018.md)

## <a name="checking-the-code-version-that-has-been-applied-to-your-data-warehouse"></a>Vérification de la version de code qui a été appliquée à votre entrepôt de données

Pour confirmer quelle version a été appliquée à votre entrepôt de données : Connectez-vous à votre entrepôt de données via SSMS et exécutez la syntaxe qui vous indiquera la version actuelle de SQL Data Warehouse.

```sql
SELECT @@VERSION AS 'SQL Data Warehouse';
```

Exemple de sortie : ![Version de SQL Data Warehouse](./media/release-notes/dw-version.png)

Utilisez la version identifiée pour confirmer quelle version a été appliquée à votre entrepôt de données SQL Azure. 


## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus](https://docs.microsoft.com/azure/sql-data-warehouse/viewing-maintenance-schedule) sur l’affichage d’une planification de maintenance. 
- [En savoir plus](https://docs.microsoft.com/azure/sql-data-warehouse/changing-maintenance-schedule) sur la modification d’une planification de maintenance.
- [En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/alert-metric) sur la création, l’affichage et la gestion des alertes à l’aide d’Azure Monitor.
- [En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sur les actions webhook pour les règles d’alerte de journal.
- [En savoir plus](https://docs.microsoft.com/azure/service-health/service-health-overview) sur Azure Service Health