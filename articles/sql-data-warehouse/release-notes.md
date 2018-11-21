---
title: Notes de publication Azure SQL Data Warehouse | Microsoft Docs
description: Notes de publication pour Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 11/11/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: b702d375f7a66843918a960ca3783c078eac541e
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51579285"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Notes de publication pour Azure SQL Data Warehouse

Azure SQL Data Warehouse est un entrepôt de données d’entreprise basé sur le cloud qui utilise le traitement massivement parallèle pour exécuter rapidement des requêtes complexes parmi des pétaoctets de données. Utilisez SQL Data Warehouse comme composant clé d’une solution Big Data. Importez des données volumineuses dans SQL Data Warehouse avec des requêtes T-SQL PolyBase simples, puis utilisez le traitement massivement parallèle pour exécuter des analyses hautes performances. À mesure de vos intégrations et analyses, l’entrepôt de données deviendra la seule source pertinente sur laquelle votre activité peut compter pour obtenir des informations précises.

Cliquez sur les liens ci-dessous pour en savoir plus sur les nouvelles fonctionnalités et les améliorations que vous pouvez attendre dans la dernière version d’Azure SQL Data Warehouse. Vous pouvez vous attendre à recevoir ces mises à jour de service au cours de votre programme de maintenance planifié.

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

Exemple de sortie : ![Version SQL Data Warehouse](./media/release-notes/sql_data_warehouse_version.png)

Veuillez utiliser la date prévue pour confirmer quelle version a été appliquée à votre version d’Azure SQL Data Warehouse. 


## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus](https://docs.microsoft.com/azure/sql-data-warehouse/viewing-maintenance-schedule) sur l’affichage d’une planification de maintenance. 
- [En savoir plus](https://docs.microsoft.com/azure/sql-data-warehouse/changing-maintenance-schedule) sur la modification d’une planification de maintenance.
- [En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/alert-metric) sur la création, l’affichage et la gestion des alertes à l’aide d’Azure Monitor.
- [En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sur les actions webhook pour les règles d’alerte de journal.
- [En savoir plus](https://docs.microsoft.com/azure/service-health/service-health-overview) sur Azure Service Health
