---
title: Configurer Advanced Threat Protection
titleSuffix: Azure SQL Managed Instance
description: Advanced Threat Protection détecte les activités de base de données anormales indiquant des menaces de sécurité potentielles pour la base de données dans Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: ceb6285448df2a5d87dfa87ab249c99bf22c9928
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84686322"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Configurer Advanced Threat Protection dans Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Advanced Threat Protection](../database/threat-detection-overview.md) pour une instance [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles ou potentiellement dangereuses de bases de données. Advanced Threat Protection peut identifier une **injection SQL potentielle**, un **accès à partir d'un emplacement ou d'un centre de données inhabituel**, un **accès à partir d'un principal inconnu ou d'une application potentiellement dangereuse** et des **informations d'identification SQL par force brute**. Pour plus d'informations, consultez [Alertes de protection avancée des menaces](../database/threat-detection-overview.md#alerts).

Vous pouvez recevoir des notifications à propos des menaces détectées par l’intermédiaire de [notifications par e-mail](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) ou du [portail Azure](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal).

[Advanced Threat Protection](../database/threat-detection-overview.md) fait partie de l’offre [Advanced Data Security](../database/advanced-data-security.md) qui est un package unifié de fonctionnalités de sécurité SQL avancées. Advanced Threat Protection est accessible et gérable par le biais du portail SQL ADS central.

##  <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
2. Accédez à la page de configuration de l’instance SQL Managed Instance que vous voulez protéger. Dans la page **Paramètres**, sélectionnez **Advanced Data Security**.
3. Sur la page de configuration Advanced Data Security
   - **ACTIVEZ** Advanced Data Security.
   - Configurez la **liste des e-mails** qui doivent recevoir des alertes de sécurité en cas de détection d’activités anormales sur la base de données.
   - Sélectionnez le **compte de stockage Azure** dans lequel sont enregistrés les enregistrements d’audit des menaces anormales.
   - Sélectionnez les **types d’Advanced Threat Protection** que vous souhaitez configurer. En savoir plus sur les [alertes Advanced Threat Protection](../database/threat-detection-overview.md)
4. Cliquez sur **Enregistrer** pour enregistrer la stratégie Advanced Data Security, nouvelle ou mise à jour.

   ![Protection avancée contre les menaces](./media/threat-detection-configure/threat-detection.png)


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Advanced Threat Protection](../database/threat-detection-overview.md)
- Pour en savoir plus sur les instances gérées, consultez [Qu’est-ce qu’une instance Azure SQL Managed Instance ?](sql-managed-instance-paas-overview.md).
- En savoir plus sur [Advanced Threat Protection pour Azure SQL Database](../database/threat-detection-configure.md).
- En savoir plus sur [l’audit SQL Managed Instance](https://go.microsoft.com/fwlink/?linkid=869430).
- Découvrez plus en détail [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
