---
title: Advanced Data Security
description: Apprenez-en plus sur les fonctionnalités permettant de découvrir et de classifier les données sensibles, de gérer les vulnérabilités de votre base de données et de détecter les activités anormales pouvant indiquer une menace ciblant votre base de données Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
ms.author: memildin
author: memildin
manager: rkarlin
ms.reviewer: vanto
ms.date: 03/31/2019
ms.openlocfilehash: aed0bcb79dedf057c5943cea9f4b4399b2f630cb
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677466"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Advanced Data Security pour Azure SQL Database

Advanced Data Security est un package unifié de fonctionnalités de sécurité SQL avancées. Il inclut des fonctionnalités permettant de découvrir et de classifier les données sensibles, de présenter et de corriger les vulnérabilités de votre base de données et de détecter les activités anormales susceptibles d’indiquer une menace ciblant votre base de données. Il vous permet d’activer et de gérer ces fonctionnalités à partir d’un seul et même emplacement.

## <a name="overview"></a>Vue d’ensemble

Advanced Data Security (ADS) fournit un ensemble de fonctionnalités de sécurité SQL avancées, dont la découverte et la classification des données, l’évaluation des vulnérabilités et Advanced Threat Protection.

- [Découverte et classification des données](sql-database-data-discovery-and-classification.md) offre des fonctionnalités avancées intégrées à Azure SQL Database pour la découverte, la classification, l’étiquetage et le signalement des données sensibles dans vos bases de données. Cette fonctionnalité peut être utilisée pour fournir de la visibilité sur l’état de classification de votre base de données et pour suivre l’accès aux données sensibles dans la base de données et en dehors de celle-ci.
- La fonctionnalité [Évaluation des vulnérabilités](sql-vulnerability-assessment.md) est un service simple à configurer, qui vous permet de découvrir, suivre et de corriger des vulnérabilités de base de données potentielles. Elle offre une visibilité sur votre état de sécurité et inclut des mesures pratiques pour résoudre les problèmes de sécurité et améliorer la protection de votre base de données.
- [Advanced Threat Protection](sql-database-threat-detection-overview.md) détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de votre base de données. Elle surveille en permanence votre base de données pour détecter des activités suspectes et envoie immédiatement des alertes de sécurité en cas de vulnérabilités éventuelles, d’attaques par injection de code SQL et de modèles d’accès anormaux à la base de données. Les alertes Advanced Threat Protection fournissent des informations sur les activités suspectes et recommandent l’action à entreprendre pour analyser et atténuer la menace.

Il vous suffit d’activer Advanced Data Security une seule fois pour activer toutes ces fonctionnalités. D’un seul clic, vous pouvez activer Advanced Data Security pour toutes les bases de données sur votre serveur SQL Database ou instance gérée. L’activation ou la gestion des paramètres Advanced Data Security nécessite d’appartenir au rôle [Gestionnaire de sécurité SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager), au rôle d’administrateur de base de données SQL ou au rôle d’administrateur de serveur SQL. 

La tarification Advanced Data Security reflète celle du niveau Standard d’Azure Security Center, où chaque serveur SQL Database ou instance gérée protégés correspond à un seul nœud. Les ressources nouvellement protégées se qualifient pour un essai gratuit du niveau Standard de Security Center. Pour plus d’informations, consultez la [page de tarification d’Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Prise en main d’Advanced Data Security

La procédure suivante vous aide à prendre en main Advanced Data Security.

## <a name="1-enable-ads"></a>1. Activer SQL Advanced Data Security

Activer ADS en vous rendant sur**Sécurité avancée des données** sous **Sécurité** de votre serveur SQL Database ou de votre instance gérée. Pour activer SQL Advanced Data Security pour toutes les bases de données sur le serveur de base de données ou l’instance gérée, cliquez sur **Activer Advanced Data Security sur le serveur**.

> [!NOTE]
> Un compte de stockage est automatiquement créé et configuré pour stocker les résultats d’analyse de votre **évaluation des vulnérabilités**. Si vous avez déjà activé ADS pour un autre serveur dans le même groupe de ressources et la même région, alors le compte de stockage existant est utilisé.

![Activer SQL Advanced Data Security](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> Le coût d’Advanced Data Security est aligné avec la tarification par nœud du niveau Standard d’Azure Security Center, où un nœud correspond à l’ensemble du serveur SQL Database ou de l’instance gérée. Vous payez donc une seule fois pour protéger toutes les bases de données sur le serveur de base de données ou l’instance gérée avec Advanced Data Security. Vous pouvez bénéficier d’un essai gratuit d’Advanced Data Security.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Démarrer la classification des données, le suivi des vulnérabilités et l’analyse des alertes de menaces

Cliquez sur la carte **Découverte et classification des données** pour afficher les colonnes sensibles devant être classifiées, et pour classifier vos données à l’aide d’étiquettes de sensibilité persistantes. Cliquez sur la carte **Évaluation des vulnérabilités** pour afficher et gérer des rapports et des analyses de vulnérabilités et suivre votre état de sécurité. Si des alertes de sécurité ont été reçues, cliquez sur la carte **Advanced Threat Protection** pour afficher des détails des alertes et consulter un rapport regroupant toutes les alertes dans votre abonnement Azure par le biais de la page d’alertes de sécurité Azure Security Center.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. Gérer les paramètres Advanced Data Security sur votre serveur SQL Database ou instance gérée

Pour afficher et gérer les paramètres ADS, accédez à **Advanced Data Security** sous le titre **Sécurité** de votre serveur SQL Database ou instance gérée. Dans cette page, vous pouvez activer ou désactiver ADS et modifier les paramètres d’évaluation des vulnérabilités et Advanced Threat Protection pour l’intégralité de votre serveur SQL Database ou instance gérée.

![Paramètres du serveur](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. Gérer les paramètres Advanced Data Security pour une base de données SQL

Pour écraser les paramètres de détection des menaces ADS d’une base de données spécifiée, cochez la case **Activer Advanced Data Security au niveau de la base de données**. Utilisez cette option uniquement si vous avez besoin de recevoir des alertes Advanced Threat Protection ou des résultats d’évaluation des vulnérabilités distincts pour la base de données individuelle, au lieu ou en plus des alertes et résultats reçus pour toutes les bases de données sur le serveur de base de données ou l’instance gérée.

Une fois que la case est cochée, vous pouvez configurer les paramètres appropriés pour cette base de données.
 
![Paramètres de base de données et Advanced Threat Protection](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Les paramètres Advanced Data Security de votre serveur de base de données ou instance gérée sont également accessibles depuis le volet de base de données Advanced Data Security. Cliquez sur **Paramètres** dans le volet Advanced Data Security principal, puis cliquez sur **Voir les paramètres Advanced Data Security du serveur**. 

![Paramètres de base de données](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Étapes suivantes 

- En savoir plus sur [Découverte et classification des données](sql-database-data-discovery-and-classification.md) 
- En savoir plus sur [Évaluation des vulnérabilités](sql-vulnerability-assessment.md) 
- En savoir plus sur [Advanced Threat Protection](sql-database-threat-detection.md)
- En savoir plus sur [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
