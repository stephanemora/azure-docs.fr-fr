---
title: Protection avancée contre les menaces - Azure SQL Database | Microsoft Docs
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
ms.date: 12/05/2018
ms.openlocfilehash: ea7b4def23f766e6cda1e1e8e1d4e8dc7be302fe
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997603"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Protection avancée contre les menaces pour Azure SQL Database

Protection avancée contre les menaces SQL est un package unifié de fonctionnalités de sécurité SQL avancées. Il inclut des fonctionnalités permettant de découvrir et de classifier les données sensibles, de présenter et de corriger les vulnérabilités de votre base de données et de détecter les activités anormales susceptibles d’indiquer une menace ciblant votre base de données. Il vous permet d’activer et de gérer ces fonctionnalités à partir d’un seul et même emplacement. 

## <a name="overview"></a>Vue d’ensemble

Protection avancée contre les menaces SQL fournit un ensemble de fonctionnalités de sécurité SQL avancées, dont Découverte et classification des données, Évaluation des vulnérabilités et Détection de menaces. 

- [Découverte et classification des données](sql-database-data-discovery-and-classification.md) (actuellement en préversion) offre des fonctionnalités intégrées à Azure SQL Database pour la découverte, la classification, l’étiquetage et la protection des données sensibles dans vos bases de données. Cette fonctionnalité peut être utilisée pour fournir de la visibilité sur l’état de classification de votre base de données et pour suivre l’accès aux données sensibles dans la base de données et en dehors de celle-ci.
- La fonctionnalité [Évaluation des vulnérabilités](sql-vulnerability-assessment.md) est un service simple à configurer, qui vous permet de découvrir, suivre et de corriger des vulnérabilités de base de données potentielles. Elle offre une visibilité sur votre état de sécurité et inclut des mesures pratiques pour résoudre les problèmes de sécurité et améliorer la protection de votre base de données.
- La fonctionnalité [Détection des menaces](sql-database-threat-detection-overview.md) détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de votre base de données. Elle surveille en permanence votre base de données pour détecter des activités suspectes et envoie immédiatement des alertes de sécurité en cas de vulnérabilités éventuelles, d’attaques par injection de code SQL et de modèles d’accès anormaux à la base de données. Les alertes générées par la fonctionnalité Détection des menaces fournissent des informations sur les activités suspectes et recommandent l’action à entreprendre pour analyser et atténuer la menace.

Il vous suffit d’activer Protection avancée contre les menaces SQL une seule fois pour activer toutes ces fonctionnalités. En un clic, vous pouvez activer Protection avancée contre les menaces sur l’ensemble de votre serveur de base de données, en l’appliquant à toutes les bases de données sur le serveur. 

La tarification Protection avancée contre les menaces reflète celle du niveau Standard d’Azure Security Center, où chaque serveur SQL Database protégé correspond à un seul nœud. Les ressources nouvellement protégées se qualifient pour un essai gratuit du niveau Standard de Security Center. Pour plus d’informations, consultez la [page de tarification d’Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="getting-started-with-atp"></a>Prise en main de Protection avancée contre les menaces 
Les étapes suivantes vous aident à prendre en main Protection avancée contre les menaces. 

## <a name="1-enable-atp"></a>1. Activer Protection avancée contre les menaces

Activez Protection avancée contre les menaces en accédant à **Protection avancée contre les menaces** sous **Sécurité** dans votre volet Azure SQL Database. Pour activer Protection avancée contre les menaces pour toutes les bases de données sur le serveur, cliquez sur **Activer la Protection avancée contre les menaces sur le serveur**.

![Activer Protection avancée contre les menaces](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> Le coût de la Protection avancée contre les menaces est aligné avec la tarification par nœud du niveau Standard d’Azure Security Center, où un nœud correspond à l’ensemble du serveur logique SQL. Vous payez donc une seule fois pour protéger toutes les bases de données sur le serveur avec Protection avancée contre les menaces. Vous pouvez bénéficier d’une évaluation gratuite de Protection avancée contre les menaces.

## <a name="2-configure-vulnerability-assessment"></a>2. Configurer la fonctionnalité Évaluation des vulnérabilités

Pour commencer à utiliser la fonctionnalité Évaluation des vulnérabilités, vous devez configurer un compte de stockage dans lequel les résultats d’analyse sont enregistrés. Pour ce faire, cliquez sur la carte de la fonctionnalité Évaluation des vulnérabilités.

![Configurer Évaluation des vulnérabilités](./media/sql-advanced-protection/configure_va.png) 

Sélectionnez ou créez un compte de stockage pour enregistrer les résultats d’analyse. Vous pouvez également activer les analyses périodiques récurrentes pour configurer la fonctionnalité Évaluation des vulnérabilités de façon qu’elle exécute des analyses automatiques une fois par semaine. Un résumé des résultats d’analyse est envoyé aux adresses e-mail que vous fournissez.

![Paramètres d’Évaluation des vulnérabilités](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. Démarrer la classification des données, le suivi des vulnérabilités et l’analyse des alertes de menaces

Cliquez sur la carte **Découverte et classification des données** pour afficher les colonnes sensibles devant être classifiées, et pour classifier vos données à l’aide d’étiquettes de sensibilité persistantes. Cliquez sur la carte **Évaluation des vulnérabilités** pour afficher et gérer des rapports et des analyses de vulnérabilités et suivre votre état de sécurité. Si des alertes de sécurité ont été reçues, cliquez sur la carte **Détection des menaces** pour afficher des détails des alertes et consulter un rapport regroupant toutes les alertes dans votre abonnement Azure via la page d’alertes de sécurité Azure Security Center.

## <a name="4-manage-atp-settings-on-your-sql-server"></a>4. Gérer les paramètres Protection avancée contre les menaces sur votre serveur SQL

Pour afficher et gérer les paramètres Protection avancée contre les menaces, accédez à **Protection avancée contre les menaces** sous **Sécurité** dans le volet de votre serveur SQL. Sur cette page, vous pouvez activer ou désactiver Protection avancée contre les menaces et modifier les paramètres associés pour l’intégralité de votre serveur SQL.

![Paramètres du serveur](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-atp-settings-for-a-sql-database"></a>5. Gérer les paramètres Protection avancée contre les menaces pour une base de données SQL

Pour écraser les paramètres Protection avancée contre les menaces d’une base de données spécifiée, cochez la case **Activer la Protection avancée contre les menaces au niveau de la base de données**. Utilisez cette option uniquement si vous avez besoin de recevoir des alertes de détection des menaces séparées pour la base de données spécifiée, au lieu ou en plus des alertes reçues pour toutes les bases de données sur le serveur. 

Une fois la case cochée, cliquez sur **Paramètres de détection des menaces pour cette base de données**, puis configurez les paramètres appropriés pour cette base de données.

![Base de données et paramètres de détection des menaces](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Vous pouvez également accéder aux paramètres Protection avancée contre les menaces de votre serveur à partir du volet de la base de données Protection avancée contre les menaces. Cliquez sur **Paramètres** dans le volet Protection avancée contre les menaces principal, puis cliquez sur **Afficher les paramètres serveur de la Protection avancée contre les menaces**. 

![Paramètres de base de données](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Étapes suivantes 

- En savoir plus sur [Découverte et classification des données](sql-database-data-discovery-and-classification.md) 
- En savoir plus sur [Évaluation des vulnérabilités](sql-vulnerability-assessment.md) 
- En savoir plus sur [Détection de menaces](sql-database-threat-detection.md)
- En savoir plus sur [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
