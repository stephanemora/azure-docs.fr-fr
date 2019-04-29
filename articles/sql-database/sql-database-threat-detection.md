---
title: Détection des menaces - Azure SQL Database | Microsoft Docs
description: La Détection des menaces permet de détecter les activités de base de données anormales indiquant la présence potentielle de menaces pour la sécurité dans une base de données unique ou un pool élastique.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 60839fa06d8436d3d2202d1d97735c42c5768e9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60331472"
---
# <a name="azure-sql-database-threat-detection-for-single-or-pooled-databases"></a>Détection des menaces Azure SQL Database pour bases de données uniques et mises en pool

La [Détection des menaces](sql-database-threat-detection-overview.md) pour bases de données uniques et regroupées détecte les activités anormales indiquant des tentatives inhabituelles et potentiellement dangereuses d'accès aux bases de données ou d'attaque de celles-ci. La Détection des menaces peut identifier une **injection SQL potentielle**, un **accès à partir d'un emplacement ou d'un centre de données inhabituel**, un **accès à partir d'un principal inconnu ou d'une application potentiellement dangereuse** et des **informations d'identification SQL par force brute**. Pour plus d'informations, consultez [Alertes de détection des menaces](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Vous pouvez recevoir des notifications à propos des menaces détectées par l’intermédiaire de [notifications par e-mail](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou du [portail Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal).

La [Détection des menaces](sql-database-threat-detection-overview.md) fait partie de l'offre [Advanced Data Security](sql-database-advanced-data-security.md) (ADS), qui est un package unifié de fonctionnalités de sécurité SQL avancées. La Détection des menaces est accessible et gérable via le portail SQL ADS central. Le package Advanced Data Security est facturé 15 USD par mois et par serveur logique, les 30 premiers jours étant gratuits.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Configurer la détection des menaces pour votre base de données dans le portail Azure

1. Lancez le portail Azure sur [https://portal.azure.com](https://portal.azure.com).
2. Accédez à la page de configuration du serveur Azure SQL Database que vous voulez protéger. Dans les paramètres de sécurité, sélectionnez **Advanced Data Security**.
3. Sur la page de configuration **Advanced Data Security** :

   - Activez Advanced Data Security sur le serveur.
   - Dans **Threat Detection Settings** (Paramètres de détection des menaces), dans la zone de texte **Envoyer les alertes à**, fournissez la liste d’adresses e-mail devant recevoir des alertes de sécurité lors de la détection d’activités de base de données anormales.
  
   ![Configurer la détection des menaces](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Configurer la détection des menaces avec PowerShell

Pour obtenir un exemple de script, consultez [Configurer l’audit et la détection des menaces avec PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [détection des menaces](sql-database-threat-detection-overview.md)
- En savoir plus sur la [détection des menaces dans une instance gérée](sql-database-managed-instance-threat-detection.md)  
- En savoir plus sur [Advanced Data Security](sql-database-advanced-data-security.md)
- En savoir plus sur l'[audit](sql-database-auditing.md)
- En savoir plus sur [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Pour plus d'informations sur la tarification, consultez la [page de tarification SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  
