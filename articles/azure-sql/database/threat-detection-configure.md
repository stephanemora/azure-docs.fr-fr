---
title: Configurer Advanced Threat Protection
description: Advanced Threat Protection détecte les activités de base de données anormales indiquant des menaces de sécurité potentielles pour la base de données dans Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 2f4e9841a44252829fae283a12ba804219204022
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84321544"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Configurer Advanced Threat Protection pour Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Advanced Threat Protection](threat-detection-overview.md) pour Azure SQL Database détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles ou potentiellement dangereuses de bases de données. Advanced Threat Protection peut identifier une **injection SQL potentielle**, un **accès à partir d'un emplacement ou d'un centre de données inhabituel**, un **accès à partir d'un principal inconnu ou d'une application potentiellement dangereuse** et des **informations d'identification SQL par force brute**. Pour plus d'informations, consultez [Alertes de protection avancée des menaces](threat-detection-overview.md#alerts).

Vous pouvez recevoir des notifications à propos des menaces détectées par l’intermédiaire de [notifications par e-mail](threat-detection-overview.md#explore-detection-of-a-suspicious-event) ou du [portail Azure](threat-detection-overview.md#explore-alerts-in-the-azure-portal).

[Advanced Threat Protection](threat-detection-overview.md) fait partie de l’offre [Advanced Data Security](advanced-data-security.md) qui réunit les fonctionnalités de sécurité SQL avancées. Advanced Threat Protection est accessible et gérable par le biais du portail SQL Advanced Data Security central.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Configurer la protection avancée contre les menaces dans le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Accédez à la page de configuration du serveur que vous voulez protéger. Dans les paramètres de sécurité, sélectionnez **Advanced Data Security**.
3. Sur la page de configuration **Advanced Data Security** :

   - Activez Advanced Data Security sur le serveur.
   - Dans les **paramètres de protection avancée contre les menaces**, dans la zone de texte **Envoyer les alertes à**, fournissez la liste d’adresses e-mail devant recevoir des alertes de sécurité lors de la détection d’activités de base de données anormales.
  
   ![Configurer Advanced Threat Protection](./media/threat-detection/set_up_threat_detection.png)

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Configurer Advanced Threat Protection avec PowerShell

Pour obtenir un exemple de script, consultez [Configurer l’audit et Advanced Threat Protection avec PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Advanced Threat Protection](threat-detection-overview.md)
- Découvrez-en plus sur [Advanced Threat Protection dans SQL Managed Instance](../managed-instance/threat-detection-configure.md).  
- En savoir plus sur [Advanced Data Security](advanced-data-security.md)
- En savoir plus sur l'[audit](../../azure-sql/database/auditing-overview.md)
- En savoir plus sur [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Pour plus d'informations sur la tarification, consultez la [page de tarification SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  
