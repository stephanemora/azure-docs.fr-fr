---
title: Configurer la détection des menaces - Instance managée Azure SQL Database | Microsoft Docs
description: La détection des menaces permet de détecter les activités de base de données anormales indiquant la présence potentielle de menaces de sécurité pour une base de données d’une instance gérée.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 02/04/2019
ms.openlocfilehash: 6d1f2a9547f01de91a8e7739d827a91154842d7b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567307"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Configurer la détection des menaces (préversion) dans une instance managée Azure SQL Database

La [détection des menaces](sql-database-threat-detection-overview.md) pour une [instance gérée](sql-database-managed-instance-index.yml) détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles ou potentiellement dangereuses de bases de données. La détection des menaces peut identifier une **injection SQL potentielle**, un **accès à partir d’un emplacement ou d’un centre de données inhabituel**, un **accès à partir d’un principal inconnu ou d’une application potentiellement dangereuse** et des **informations d’identification SQL par force brute**. Pour plus d’informations, consultez [Alertes de détection des menaces](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Vous pouvez recevoir des notifications à propos des menaces détectées par l’intermédiaire de [notifications par e-mail](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou du [portail Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal).

La [Détection des menaces](sql-database-threat-detection-overview.md) fait partie de l'offre [Advanced Data Security](sql-database-advanced-data-security.md) (ADS), qui est un package unifié de fonctionnalités de sécurité SQL avancées. La détection des menaces est accessible et gérée via le portail SQL Advanced Data Security central. Le service de détection des menaces est facturé 15 USD par mois et par instance gérée, avec les 30 premiers jours gratuits.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Configurer la détection des menaces pour votre instance gérée dans le portail Azure

1. Lancez le portail Azure sur [https://portal.azure.com](https://portal.azure.com).
2. Accédez à la page de configuration de l’instance gérée que vous voulez protéger. Dans la page **Paramètres**, sélectionnez **Threat Detection**.
3. Page de configuration de Threat Detection
   - **Activez** la détection des menaces.
   - Configurez la **liste des e-mails** qui doivent recevoir des alertes de sécurité en cas de détection d’activités anormales sur la base de données.
   - Sélectionnez le **compte de stockage Azure** dans lequel sont enregistrés les enregistrements d’audit des menaces anormales.
4. Cliquez sur **Enregistrer** pour enregistrer la stratégie de détection de menace, nouvelle ou mise à jour.

   ![threat detection](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [détection des menaces](sql-database-threat-detection-overview.md).
- Pour en savoir plus sur les instances gérées, consultez [Qu’est-ce qu’une instance gérée ?](sql-database-managed-instance.md).
- Découvrez plus en détail la [détection des menaces pour une base de données unique](sql-database-threat-detection.md).
- Découvrez plus en détail l’[audit des instances gérées](https://go.microsoft.com/fwlink/?linkid=869430).
- Découvrez plus en détail [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
