---
title: Détection des menaces pour les services de données dans Azure Security Center | Microsoft Docs
description: Cet article présente les alertes des services de données disponibles dans Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 6d6e48c84f558840b3266193c4cbb9c3576f1a58
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665732"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Détection des menaces pour les services de données dans Azure Security Center

 Azure Security Center analyse les journaux des services de stockage de données, et déclenche des alertes lorsqu’il détecte une menace pour vos ressources de données. Cet article répertorie les alertes générées par Security Center pour les services suivants :

* [Azure SQL Database et Azure SQL Data Warehouse](#data-sql)
* [Stockage Azure](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database et SQL Data Warehouse <a name="data-sql"></a>

Advanced Threat Protection pour Azure SQL Database détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles ou potentiellement dangereuses de bases de données.

Des alertes s’affichent quand sont détectés des activités de base de données suspectes, des vulnérabilités potentielles ou des attaques par injection de code SQL, ainsi que des modèles de requêtes et d’accès anormaux à la base de données.

Advanced Threat Protection pour Azure SQL Database et SQL font partie du package unifié [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) pour les fonctionnalités de sécurité SQL avancées, couvrant les bases de données Azure SQL Database, les instances gérées d’Azure SQL Database, les bases de données Azure SQL Data Warehouse et les serveurs SQL Server sur les machines virtuelles Azure.

Pour plus d'informations, consultez les pages suivantes :

* [Comment activer Advanced Threat Protection pour Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Comment activer Advanced Threat Protection pour les serveurs SQL Server sur des machines virtuelles Azure](security-center-iaas-advanced-data.md)
* [Liste des alertes de protection contre les menaces pour SQL Database et SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)

## Stockage Azure <a name="azure-storage"></a>

Advanced Threat Protection pour Stockage (actuellement disponible pour Stockage Blob uniquement) détecte les tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes de stockage. Cette couche de protection vous permet de traiter efficacement les menaces sans pour autant être un expert en sécurité, et vous aide à gérer des systèmes de supervision de la sécurité.

>[!NOTE]
>Advanced Threat Protection pour le stockage n’est actuellement pas disponible dans les régions de cloud souverain et Azure Government.

Pour plus d'informations, consultez les pages suivantes :

* [Comment activer Advanced Threat Protection pour Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Liste des alertes de protection contre les menaces pour Stockage Azure](alerts-reference.md#alerts-azurestorage)

## Azure Cosmos DB<a name="cosmos-db"></a>

Les alertes Azure Cosmos DB sont générées en cas de détection de tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes Azure Cosmos DB.

Pour plus d'informations, consultez les pages suivantes :

* [Advanced Threat Protection pour Azure Cosmos DB (préversion)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Liste des alertes de protection contre les menaces pour Azure Cosmos DB (préversion)](alerts-reference.md#alerts-azurecosmos)
