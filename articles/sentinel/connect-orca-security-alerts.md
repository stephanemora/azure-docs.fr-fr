---
title: Connecter des alertes Orca Security à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données d’alerte Orca Security à Azure Sentinel pour consulter des tableaux de bord, créer des alertes personnalisées et améliorer l’investigation.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 5d90b6fb67d2cb00bb86ca10eca03d07f1135559
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122563726"
---
# <a name="connect-your-orca-security-alerts-to-azure-sentinel"></a>Connecter vos alertes Orca Security à Azure Sentinel 

> [!IMPORTANT]
> Le connecteur d’alertes Orca Security dans Azure Sentinel est actuellement disponible en préversion publique.
> Cette fonctionnalité est fournie sans contrat de niveau de service et est déconseillée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Le connecteur des alertes de sécurité Orca vous permet de placer facilement vos [alertes de sécurité Orca](https://orca.security/) dans Azure Sentinel, pour pouvoir les consulter dans des classeurs, les utiliser pour créer des alertes personnalisées et les incorporer pour améliorer l’investigation. L’intégration entre les alertes de sécurité Orca et Azure Sentinel utilise l’API REST.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="configure-and-connect-orca-security-alerts"></a>Configurer et connecter des alertes de sécurité Orca

Les alertes de sécurité Orca intégrer et exporter des journaux d’activité directement dans Azure Sentinel.

1. Dans le portail Azure Sentinel, cliquez sur **Connecteurs de données** et sélectionnez **Alertes de sécurité Orca**, puis **Ouvrir la page du connecteur**.

2. Reportez-vous à https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integration pour terminer l’intégration à partir de la plateforme Orca.

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion correctement établie, les données apparaissent dans l’espace de travail Log Analytics sous **CustomLogs** dans la table **OrcaAlerts_CL**.
Pour utiliser le schéma pertinent dans Log Analytics pour les alertes Orca, recherchez `OrcaAlerts_CL`.

## <a name="validate-connectivity"></a>Valider la connectivité
Jusqu’à 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics. 


## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les alertes de sécurité Orca à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.