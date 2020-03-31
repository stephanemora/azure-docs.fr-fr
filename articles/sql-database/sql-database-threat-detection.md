---
title: Configurer Advanced Threat Protection
description: Advanced Threat Protection détecte les activités de base de données anormales indiquant des menaces de sécurité potentielles pour la base de données dans une base de données unique ou un pool élastique.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 8eb8e4fccc17fe31def671cf6e8edb19d867b244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822505"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Advanced Threat Protection d’Azure SQL Database pour les bases de données uniques ou mises en pool

[Advanced Threat Protection](sql-database-threat-detection-overview.md) pour les bases de données uniques et mises en pool détecte les activités anormales indiquant des tentatives inhabituelles et potentiellement dangereuses pour accéder à des bases de données ou les exploiter. Advanced Threat Protection peut identifier une **injection SQL potentielle**, un **accès à partir d'un emplacement ou d'un centre de données inhabituel**, un **accès à partir d'un principal inconnu ou d'une application potentiellement dangereuse** et des **informations d'identification SQL par force brute**. Pour plus d'informations, consultez [Alertes de protection avancée des menaces](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Vous pouvez recevoir des notifications à propos des menaces détectées par l’intermédiaire de [notifications par e-mail](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou du [portail Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal).

[Advanced Threat Protection](sql-database-threat-detection-overview.md) fait partie de l’offre [Advanced Data Security](sql-database-advanced-data-security.md) (ADS) qui est un package unifié de fonctionnalités de sécurité SQL avancées. Advanced Threat Protection est accessible et gérable par le biais du portail SQL ADS central.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Configurer la protection avancée contre les menaces dans le portail Azure

1. Lancez le portail Azure sur [https://portal.azure.com](https://portal.azure.com).
2. Accédez à la page de configuration du serveur Azure SQL Database que vous voulez protéger. Dans les paramètres de sécurité, sélectionnez **Advanced Data Security**.
3. Sur la page de configuration **Advanced Data Security** :

   - Activez Advanced Data Security sur le serveur.
   - Dans les **paramètres de protection avancée contre les menaces**, dans la zone de texte **Envoyer les alertes à**, fournissez la liste d’adresses e-mail devant recevoir des alertes de sécurité lors de la détection d’activités de base de données anormales.
  
   ![Configurer Advanced Threat Protection](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > Les prix dans les captures d’écran ne reflètent pas toujours le prix actuel et sont donnés à titre d’exemple.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Configurer Advanced Threat Protection avec PowerShell

Pour obtenir un exemple de script, consultez [Configurer l’audit et Advanced Threat Protection avec PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Advanced Threat Protection](sql-database-threat-detection-overview.md)
- Découvrez-en plus sur [Advanced Threat Protection dans une instance gérée](sql-database-managed-instance-threat-detection.md).  
- En savoir plus sur [Advanced Data Security](sql-database-advanced-data-security.md)
- En savoir plus sur l'[audit](sql-database-auditing.md)
- En savoir plus sur [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Pour plus d'informations sur la tarification, consultez la [page de tarification SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  
