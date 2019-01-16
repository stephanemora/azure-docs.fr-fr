---
title: Configurer la détection des menaces - Azure SQL Database Managed Instance | Microsoft Docs
description: Threat Detection permet de détecter les activités base de données anormales indiquant la présence potentielle de menaces de sécurité pour une base de données d’une instance managée.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 12/06/2018
ms.openlocfilehash: f87b7093a732639db8a53ebec68fcae8f29735f3
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158667"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Configurer la détection des menaces (préversion) dans Azure SQL Database Managed Instance

La [détection des menaces](sql-database-threat-detection-overview.md) Azure SQL pour [SQL Database Managed Instance](sql-database-managed-instance-index.yml) détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles ou potentiellement dangereuses de bases de données. La détection des menaces peut identifier une **injection SQL potentielle**, un **accès à partir d’un emplacement ou d’un centre de données inhabituel**, un **accès à partir d’un principal inconnu ou d’une application potentiellement dangereuse** et des **informations d’identification SQL par force brute**. Pour plus d’informations, consultez [Alertes de détection des menaces](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts).

Vous pouvez recevoir des notifications à propos des menaces détectées par l’intermédiaire de [notifications par e-mail](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou du [portail Azure](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal).

La [détection des menaces](sql-advanced-threat-protection.md) fait partie de l’offre [SQL Advanced Threat Protection](sql-database-threat-detection-overview.md) (ATP), qui est un package unifié de fonctionnalités de sécurité SQL avancées. Threat Detection peut être accessible et gérée via le portail SQL ATP central. Le service de détection des menaces est facturé 15 USD par mois et par instance managée, avec les 30 premiers jours gratuits.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Configurer Threat Detection pour votre Managed Instance dans le portail Azure

1. Lancez le portail Azure sur [https://portal.azure.com](https://portal.azure.com).
2. Accédez à la page de configuration de Managed Instance que vous voulez protéger. Dans la page **Paramètres**, sélectionnez **Threat Detection**.
3. Page de configuration de Threat Detection
   - **Activez** la détection des menaces.
   - Configurez la **liste des e-mails** qui doivent recevoir des alertes de sécurité en cas de détection d’activités anormales sur la base de données.
   - Sélectionnez le **compte de stockage Azure** dans lequel sont enregistrés les enregistrements d’audit des menaces anormales.
4. Cliquez sur **Enregistrer** pour enregistrer la stratégie de détection de menace, nouvelle ou mise à jour.

   ![threat detection](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus en détail la [détection des menaces](sql-database-threat-detection-overview.md).
- Pour en savoir plus sur Managed Instance, consultez [Qu’est-ce que Managed Instance ?](sql-database-managed-instance.md)
- Découvrez plus en détail la [détection des menaces pour une base de données unique](sql-database-threat-detection.md).
- Découvrez plus en détail l’[audit de Managed Instance](https://go.microsoft.com/fwlink/?linkid=869430).
- Découvrez plus en détail [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
