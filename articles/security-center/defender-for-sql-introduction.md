---
title: 'Azure Defender pour SQL : avantages et fonctionnalités'
description: Découvrez les avantages et les fonctionnalités d’Azure Defender pour SQL.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 220f9fffd6c0f9b959230964a9ec79f4e209bd54
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372545"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Présentation d’Azure Defender pour SQL

Azure Defender pour SQL comprend deux plans Azure Defender qui étendent le [package de sécurité des données](../azure-sql/database/azure-defender-for-sql.md) d’Azure Security Center pour sécuriser les bases de données et leurs données où qu’elles se trouvent. 

## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|**Azure Defender pour les serveurs de base de données Azure SQL** – Disponibilité générale (GA)<br>**Azure Defender pour les serveurs SQL Server sur les machines** – Préversion<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Prix :|Les deux plans qui forment **Azure Defender pour SQL** sont facturés conformément aux indications de la [page des tarifs](security-center-pricing.md).|
|Versions de SQL protégées :|Azure SQL Database <br>Azure SQL Managed Instance<br>Azure Synapse Analytics (anciennement SQL DW)<br>SQL Server (toutes les versions prises en charge)|
|Clouds :|![Oui](./media/icons/yes-icon.png) Clouds commerciaux<br>![Oui](./media/icons/yes-icon.png) Gouvernement des États-Unis<br>![Non](./media/icons/no-icon.png) Chine Gov, autres Gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Quels sont les éléments protégés par Azure Defender pour SQL ?

**Azure Defender pour SQL** comprend deux plans Azure Defender distincts :

- **Azure Defender pour les serveurs de base de données Azure SQL** protège :
  - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
  - [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **Azure Defender pour les serveurs SQL Server sur les machines (préversion)** étend les protections de vos serveurs SQL Azure natifs pour prendre en charge les environnements hybrides et protéger les serveurs SQL (toutes les versions prises en charge) hébergés dans Azure, d’autres environnements cloud et même les machines locales.


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Quels sont les avantages d’Azure Defender pour SQL ?

Ces deux plans comprennent des fonctionnalités qui permettent d’identifier et d’atténuer les vulnérabilités potentielles des bases de données, et de détecter les activités anormales susceptibles d’indiquer des menaces pour les bases de données :

- [Évaluation des vulnérabilités](../azure-sql/database/sql-vulnerability-assessment.md) : service d’analyse qui vous permet de découvrir, de suivre les vulnérabilités de base de données potentielles et vous aide à les corriger. Les analyses d’évaluation fournissent une vue d’ensemble de l’état de sécurité de vos machines SQL, ainsi que des détails sur les résultats de la sécurité.

- [Protection avancée contre les menaces](../azure-sql/database/threat-detection-overview.md) : service de détection qui permet d’effectuer un monitoring continu des serveurs SQL pour repérer les menaces telles que l’injection de code SQL, les attaques par force brute et les abus de privilèges. Ce service fournit des alertes de sécurité orientées sur l’action dans Azure Security Center avec des détails sur l’activité suspecte, des conseils sur la façon de limiter les menaces et des options pour poursuivre vos investigations avec Azure Sentinel.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Quels sont les types d’alertes fournis par Azure Defender pour SQL ?

Les alertes de sécurité sont déclenchées dans les cas suivants :

- **Attaques potentielles par injection de code SQL** , y compris les vulnérabilités détectées lorsque les applications génèrent une instruction SQL défectueuse dans la base de données
- **Modèles de requête et d’accès à la base de données anormaux** , par exemple, un nombre anormalement élevé d’échecs de tentatives de connexion avec des informations d’identification différentes (tentative d’attaque par force brute)
- **Activité suspecte sur la base de données** , par exemple, une modification apportée à la destination de stockage de l’exportation pour une opération d’importation et d’exportation SQL.

Les alertes fournissent des détails sur l’incident qui les a déclenchées, ainsi que des suggestions pour enquêter et contrer les menaces.



## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert Azure Defender pour SQL.

Pour des informations connexes, consultez les articles suivants : 

- [Guide pratique pour activer Azure Defender pour les serveurs SQL Server sur les machines](defender-for-sql-usage.md)
- [Guide pratique pour activer Azure Defender pour les serveurs de base de données SQL](../azure-sql/database/azure-defender-for-sql.md)
- [Liste des alertes Azure Defender pour SQL](alerts-reference.md#alerts-sql-db-and-warehouse)