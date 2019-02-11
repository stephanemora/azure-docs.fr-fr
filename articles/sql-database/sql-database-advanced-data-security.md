---
title: Advanced Data Security - Azure SQL Database | Microsoft Docs
description: Apprenez-en plus sur les fonctionnalités permettant de découvrir et de classifier les données sensibles, de gérer les vulnérabilités de votre base de données et de détecter les activités anormales pouvant indiquer une menace ciblant votre base de données Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 388a1a2ff70f4d00e66511811bffdb76806ed713
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735212"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Advanced Data Security pour Azure SQL Database

Advanced Data Security est un package unifié de fonctionnalités de sécurité SQL avancées. Il inclut des fonctionnalités permettant de découvrir et de classifier les données sensibles, de présenter et de corriger les vulnérabilités de votre base de données et de détecter les activités anormales susceptibles d’indiquer une menace ciblant votre base de données. Il vous permet d’activer et de gérer ces fonctionnalités à partir d’un seul et même emplacement.

## <a name="overview"></a>Vue d’ensemble

Advanced Data Security (ADS) fournit un ensemble de fonctionnalités de sécurité SQL avancées, dont la découverte et la classification des données, l’évaluation des vulnérabilités et la détection de menaces.

- [Découverte et classification des données](sql-database-data-discovery-and-classification.md) (actuellement en préversion) offre des fonctionnalités intégrées à Azure SQL Database pour la découverte, la classification, l’étiquetage et la protection des données sensibles dans vos bases de données. Cette fonctionnalité peut être utilisée pour fournir de la visibilité sur l’état de classification de votre base de données et pour suivre l’accès aux données sensibles dans la base de données et en dehors de celle-ci.
- La fonctionnalité [Évaluation des vulnérabilités](sql-vulnerability-assessment.md) est un service simple à configurer, qui vous permet de découvrir, suivre et de corriger des vulnérabilités de base de données potentielles. Elle offre une visibilité sur votre état de sécurité et inclut des mesures pratiques pour résoudre les problèmes de sécurité et améliorer la protection de votre base de données.
- La fonctionnalité [Détection des menaces](sql-database-threat-detection-overview.md) détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de votre base de données. Elle surveille en permanence votre base de données pour détecter des activités suspectes et envoie immédiatement des alertes de sécurité en cas de vulnérabilités éventuelles, d’attaques par injection de code SQL et de modèles d’accès anormaux à la base de données. Les alertes générées par la fonctionnalité Détection des menaces fournissent des informations sur les activités suspectes et recommandent l’action à entreprendre pour analyser et atténuer la menace.

Il vous suffit d’activer Advanced Data Security une seule fois pour activer toutes ces fonctionnalités. D’un seul clic, vous pouvez activer Advanced Data Security pour toutes les bases de données sur votre serveur SQL Database ou instance gérée. L’activation ou la gestion des paramètres Advanced Data Security nécessite d’appartenir au rôle [Gestionnaire de sécurité SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager), au rôle d’administrateur de base de données SQL ou au rôle d’administrateur de serveur SQL. 

La tarification Advanced Data Security reflète celle du niveau Standard d’Azure Security Center, où chaque serveur SQL Database ou instance gérée protégés correspond à un seul nœud. Les ressources nouvellement protégées se qualifient pour un essai gratuit du niveau Standard de Security Center. Pour plus d’informations, consultez la [page de tarification d’Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Prise en main d’Advanced Data Security

La procédure suivante vous aide à prendre en main Advanced Data Security.

## <a name="1-enable-ads"></a>1. Activer SQL Advanced Data Security

Activez SQL Advanced Data Security en accédant à **Advanced Data Security** sous le titre **Sécurité** pour votre serveur de base de données SQL ou instance gérée. Pour activer SQL Advanced Data Security pour toutes les bases de données sur le serveur de base de données ou l’instance gérée, cliquez sur **Activer Advanced Data Security sur le serveur**.

![Activer SQL Advanced Data Security](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> Le coût d’Advanced Data Security est aligné avec la tarification par nœud du niveau Standard d’Azure Security Center, où un nœud correspond à l’ensemble du serveur SQL Database ou de l’instance gérée. Vous payez donc une seule fois pour protéger toutes les bases de données sur le serveur de base de données ou l’instance gérée avec Advanced Data Security. Vous pouvez bénéficier d’un essai gratuit d’Advanced Data Security.

## <a name="2-configure-vulnerability-assessment"></a>2. Configurer la fonctionnalité Évaluation des vulnérabilités

Pour commencer à utiliser la fonctionnalité Évaluation des vulnérabilités, vous devez configurer un compte de stockage dans lequel les résultats d’analyse sont enregistrés. Pour ce faire, cliquez sur la carte de la fonctionnalité Évaluation des vulnérabilités.

![Configurer Évaluation des vulnérabilités](./media/sql-advanced-protection/configure_va.png) 

Sélectionnez ou créez un compte de stockage pour enregistrer les résultats d’analyse. Vous pouvez également activer les analyses périodiques récurrentes pour configurer la fonctionnalité Évaluation des vulnérabilités de façon qu’elle exécute des analyses automatiques une fois par semaine. Un résumé des résultats d’analyse est envoyé aux adresses e-mail que vous fournissez.

![Paramètres d’Évaluation des vulnérabilités](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. Démarrer la classification des données, le suivi des vulnérabilités et l’analyse des alertes de menaces

Cliquez sur la carte **Découverte et classification des données** pour afficher les colonnes sensibles devant être classifiées, et pour classifier vos données à l’aide d’étiquettes de sensibilité persistantes. Cliquez sur la carte **Évaluation des vulnérabilités** pour afficher et gérer des rapports et des analyses de vulnérabilités et suivre votre état de sécurité. Si des alertes de sécurité ont été reçues, cliquez sur la carte **Détection des menaces** pour afficher des détails des alertes et consulter un rapport regroupant toutes les alertes dans votre abonnement Azure via la page d’alertes de sécurité Azure Security Center.

## <a name="4-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>4. Gérer les paramètres Advanced Data Security sur votre serveur SQL Database ou instance gérée

Pour afficher et gérer les paramètres ADS, accédez à **Advanced Data Security** sous le titre **Sécurité** de votre serveur SQL Database ou instance gérée. Sur cette page, vous pouvez activer ou désactiver ADS et modifier les paramètres de détection des menaces pour l’intégralité de votre serveur SQL Database ou instance gérée.

![Paramètres du serveur](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-ads-settings-for-a-sql-database"></a>5. Gérer les paramètres Advanced Data Security pour une base de données SQL

Pour écraser les paramètres de détection des menaces ADS d’une base de données spécifiée, cochez la case **Activer Advanced Data Security au niveau de la base de données**. Utilisez cette option uniquement si vous avez besoin de recevoir des alertes de détection des menaces séparées pour la base de données individuelle, au lieu ou en plus des alertes reçues pour toutes les bases de données sur le serveur de base de données ou l’instance gérée. 

Une fois la case cochée, cliquez sur **Paramètres de détection des menaces pour cette base de données**, puis configurez les paramètres appropriés pour cette base de données.

![Base de données et paramètres de détection des menaces](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Les paramètres Advanced Data Security de votre serveur de base de données ou instance gérée sont également accessibles depuis le volet de base de données Advanced Data Security. Cliquez sur **Paramètres** dans le volet Advanced Data Security principal, puis cliquez sur **Voir les paramètres Advanced Data Security du serveur**. 

![Paramètres de base de données](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Étapes suivantes 

- En savoir plus sur [Découverte et classification des données](sql-database-data-discovery-and-classification.md) 
- En savoir plus sur [Évaluation des vulnérabilités](sql-vulnerability-assessment.md) 
- En savoir plus sur la [détection des menaces](sql-database-threat-detection.md)
- En savoir plus sur [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
