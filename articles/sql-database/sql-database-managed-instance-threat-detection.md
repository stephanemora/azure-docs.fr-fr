---
title: Configurer Advanced Threat Protection – Managed Instance
description: Advanced Threat Protection détecte les activités de base de données anormales indiquant des menaces de sécurité potentielles pour la base de données dans une instance gérée.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 69292a934af8b8777f11ab58ed3fe306abf8b408
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822551"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Configurer la protection avancée contre les menaces dans Instance managée Azure SQL Database

[Advanced Threat Protection](sql-database-threat-detection-overview.md) pour une [instance gérée](sql-database-managed-instance-index.yml) détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles ou potentiellement dangereuses de bases de données. Advanced Threat Protection peut identifier une **injection SQL potentielle**, un **accès à partir d'un emplacement ou d'un centre de données inhabituel**, un **accès à partir d'un principal inconnu ou d'une application potentiellement dangereuse** et des **informations d'identification SQL par force brute**. Pour plus d'informations, consultez [Alertes de protection avancée des menaces](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Vous pouvez recevoir des notifications à propos des menaces détectées par l’intermédiaire de [notifications par e-mail](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou du [portail Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal).

[Advanced Threat Protection](sql-database-threat-detection-overview.md) fait partie de l’offre [Advanced Data Security](sql-database-advanced-data-security.md) (ADS) qui est un package unifié de fonctionnalités de sécurité SQL avancées. Advanced Threat Protection est accessible et gérable par le biais du portail SQL ADS central.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Configurer la protection avancée contre les menaces dans le portail Azure

1. Lancez le portail Azure sur [https://portal.azure.com](https://portal.azure.com).
2. Accédez à la page de configuration de l’instance gérée que vous voulez protéger. Dans la page **Paramètres**, sélectionnez **Advanced Data Security**.
3. Sur la page de configuration Advanced Data Security
   - **ACTIVEZ** Advanced Data Security.
   - Configurez la **liste des e-mails** qui doivent recevoir des alertes de sécurité en cas de détection d’activités anormales sur la base de données.
   - Sélectionnez le **compte de stockage Azure** dans lequel sont enregistrés les enregistrements d’audit des menaces anormales.
   - Sélectionnez les **types d’Advanced Threat Protection** que vous souhaitez configurer. En savoir plus sur les [alertes Advanced Threat Protection](sql-database-threat-detection-overview.md)
4. Cliquez sur **Enregistrer** pour enregistrer la stratégie Advanced Data Security, nouvelle ou mise à jour.

   ![Protection avancée contre les menaces](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > Les prix dans les captures d’écran ne reflètent pas toujours le prix actuel et sont donnés à titre d’exemple.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Advanced Threat Protection](sql-database-threat-detection-overview.md)
- Pour en savoir plus sur les instances gérées, consultez [Qu’est-ce qu’une instance gérée ?](sql-database-managed-instance.md).
- En savoir plus sur [Advanced Threat Protection pour une base de données unique](sql-database-threat-detection.md).
- Découvrez plus en détail l’[audit des instances gérées](https://go.microsoft.com/fwlink/?linkid=869430).
- Découvrez plus en détail [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
