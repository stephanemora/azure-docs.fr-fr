---
title: Azure Defender pour SQL
description: Apprenez-en plus sur les fonctionnalités permettant de gérer les vulnérabilités de votre base de données et de détecter les activités anormales pouvant indiquer une menace ciblant votre base de données dans Azure SQL Database, Azure SQL Managed Instance ou Azure Synapse.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 02/02/2021
ms.openlocfilehash: 48df96373554f6e474c3835bf81e38a9aea5450c
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508807"
---
# <a name="azure-defender-for-sql"></a>Azure Defender pour SQL
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure Defender pour SQL est un package unifié de fonctionnalités de sécurité SQL avancées. Azure Defender est disponible pour Azure SQL Database, Azure SQL Managed Instance et Azure Synapse Analytics. Il inclut des fonctionnalités permettant de découvrir et de classifier les données sensibles, de présenter et de corriger les vulnérabilités de votre base de données et de détecter les activités anormales susceptibles d’indiquer une menace ciblant votre base de données. Il vous permet d’activer et de gérer ces fonctionnalités à partir d’un seul et même emplacement.

## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Quels sont les avantages d’Azure Defender pour SQL ?

Azure Defender fournit un ensemble de fonctionnalités avancées de sécurité SQL, dont l’évaluation des vulnérabilités SQL et Advanced Threat Protection.
- La fonctionnalité [Évaluation des vulnérabilités](sql-vulnerability-assessment.md) est un service simple à configurer, qui vous permet de découvrir, suivre et corriger de potentielles vulnérabilités de base de données. Elle offre une visibilité sur votre état de sécurité et inclut des mesures pratiques pour résoudre les problèmes de sécurité et améliorer la protection de votre base de données.
- [Advanced Threat Protection](threat-detection-overview.md) détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de votre base de données. Elle surveille en permanence votre base de données pour détecter des activités suspectes et envoie immédiatement des alertes de sécurité en cas de vulnérabilités éventuelles, d’attaques par injection de code SQL Azure et de modèles d’accès anormaux à la base de données. Les alertes Advanced Threat Protection fournissent des informations sur les activités suspectes et recommandent l’action à entreprendre pour analyser et atténuer la menace.

Activez Azure Defender pour SQL une seule fois pour activer toutes ces fonctionnalités incluses. D’un seul clic, vous pouvez activer Azure Defender pour toutes les bases de données sur votre [serveur](logical-servers.md) dans Azure ou dans votre instance gérée SQL. L’activation ou la gestion des paramètres Azure Defender nécessite d’appartenir au rôle [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) ou à l’un des rôles d’administrateur de base de données ou de serveur.

Pour plus d’informations sur la tarification d’Azure Defender pour SQL, consultez la [page de tarification Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-azure-defender"></a>Activer Azure Defender

Azure Defender est accessible via le [portail Azure](https://portal.azure.com). Activez Azure Defender en accédant à **Security Center** sous l’en-tête **Sécurité** de votre serveur ou instance gérée.

> [!NOTE]
> Un compte de stockage est automatiquement créé et configuré pour stocker les résultats d’analyse de votre **évaluation des vulnérabilités**. Si vous avez déjà activé Azure Defender pour un autre serveur dans le même groupe de ressources et la même région, alors le compte de stockage existant est utilisé.
>
> Le coût d’Azure Defender reflète la tarification par nœud du niveau Standard d’Azure Security Center, où un nœud correspond à l’intégralité du serveur ou de l’instance gérée. Vous payez donc une seule fois pour protéger toutes les bases de données sur le serveur ou l’instance gérée avec Azure Defender. Vous pouvez bénéficier d’un essai gratuit à Azure Defender.

:::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Activer Azure Defender pour SQL à partir des bases de données Azure SQL":::

## <a name="track-vulnerabilities-and-investigate-threat-alerts"></a>Suivre les vulnérabilités et examiner les alertes de menace

Cliquez sur la carte **Évaluation des vulnérabilités** pour afficher et gérer des rapports et des analyses de vulnérabilités et suivre votre état de sécurité. Si des alertes de sécurité ont été reçues, cliquez sur la carte **Advanced Threat Protection** pour afficher des détails des alertes et consulter un rapport regroupant toutes les alertes dans votre abonnement Azure par le biais de la page d’alertes de sécurité Azure Security Center.

## <a name="manage-azure-defender-settings"></a>Gérer les paramètres d’Azure Defender

Pour afficher et gérer les paramètres d’Azure Defender :

1. Dans la zone **Sécurité** de votre serveur ou de votre instance gérée, sélectionnez **Security Center**.

    Sur cette page, vous verrez l’état d’Azure Defender pour SQL :

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Vérification de l’état d’Azure Defender pour SQL dans les bases de données Azure SQL":::

1. Si Azure Defender pour SQL est activé, vous verrez un lien **Configurer** comme illustré dans le graphique précédent. Pour modifier les paramètres d’Azure Defender pour SQL, sélectionnez **Configurer**.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="Paramètres du serveur d’administration de la sécurité":::

1. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Évaluation des vulnérabilités](sql-vulnerability-assessment.md)
- En savoir plus sur [Advanced Threat Protection](threat-detection-configure.md)
- En savoir plus sur [Azure Security Center](../../security-center/security-center-introduction.md)