---
title: Détection de menaces - Azure SQL Database | Microsoft Docs
description: Threat Detection permet de détecter les activités de base de données anormales indiquant la présence potentielle de menaces de sécurité pour une base de données unique ou un pool élastique.
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
ms.date: 01/30/2019
ms.openlocfilehash: 032146742c1a49fc8cdbda24d5c732add3cc5ea7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465348"
---
# <a name="azure-sql-database-threat-detection-for-standalone-or-pooled-database"></a>Détection des menaces Azure SQL Database pour une base de données autonome ou regroupée

La [détection des menaces](sql-database-threat-detection-overview.md) Azure SQL pour les bases de données autonomes et regroupées détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de bases de données. La détection des menaces peut identifier une **injection SQL potentielle**, un **accès à partir d’un emplacement ou d’un centre de données inhabituel**, un **accès à partir d’un principal inconnu ou d’une application potentiellement dangereuse** et des **informations d’identification SQL par force brute**. Pour plus d’informations, consultez [Alertes de détection des menaces](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts).

Vous pouvez recevoir des notifications à propos des menaces détectées par l’intermédiaire de [notifications par e-mail](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou du [portail Azure](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal).

La [détection des menaces](sql-database-threat-detection-overview.md) fait partie de l’offre [SQL Advanced Data Security](sql-advanced-threat-protection.md), qui est un package unifié de fonctionnalités de sécurité SQL avancées. La détection des menaces peut être accessible et gérée via le portail SQL Advanced Data Security central. Le package Advanced Data Security est facturé 15 USD par mois et par serveur logique, avec les 30 premiers jours gratuits.

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

- En savoir plus sur la [détection des menaces](sql-database-threat-detection-overview.md).
- En savoir plus sur la [détection des menaces dans Managed Instance](sql-database-managed-instance-threat-detection.md).  
- En savoir plus sur [SQL Advanced Data Security](sql-advanced-threat-protection.md).
- En savoir plus sur [Audit Azure SQL Database](sql-database-auditing.md)
- En savoir plus sur [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Pour plus d’informations sur la tarification, consultez la [page de tarification SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  
