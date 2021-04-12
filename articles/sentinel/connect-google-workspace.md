---
title: Connecter des données Google Workspace (G Suite) à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur de données Google Workspace (G Suite) pour ingérer des événements d’activité Google Workspace dans Azure Sentinel. Affichez des données Google Workspace dans des classeurs, créez des alertes et améliorez l’investigation.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 4ada570502d913283ba9ee4cc4c65b7bdd853935
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101743089"
---
# <a name="connect-your-google-workspace-to-azure-sentinel"></a>Connecter votre Google Workspace à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Google Workspace est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Le connecteur de données [Google Workspace (anciennement G Suite)](https://workspace.google.com/) offre la possibilité d’ingérer des événements d’activité Google Workspace dans Azure Sentinel via l’API REST. Le connecteur apporte à ces [événements](https://developers.google.com/admin-sdk/reports/reference/rest/v1/activities) la visibilité de votre SOC, ce qui vous permet d’examiner les risques de sécurité potentiels, d’analyser la collaboration de votre équipe, de diagnostiquer des problèmes de configuration, de savoir qui se connecte et quand, d’analyser l’activité de l’administrateur, de comprendre comment les utilisateurs créent et partagent du contenu, et de passer en revue d’autres événements au sein de votre organisation.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

Pour collecter les données de Google Workspace, vous devez disposer des autorisations et informations d’identification suivantes :

- Autorisations d’accès en lecture et en écriture sur l’espace de travail Azure Sentinel.

- Autorisations d’accès en lecture aux clés partagées de l’espace de travail. [En savoir plus sur les clés d’espace de travail](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Autorisations d’accès en lecture et en écriture à Azure Functions pour créer une application de fonction. [En savoir plus sur Azure Functions](../azure-functions/index.yml).

- Les informations d’identification **GooglePickleString** sont requises pour l’API REST. [Apprenez-en davantage sur l’API REST Google](https://developers.google.com/admin-sdk/reports/v1/reference/activities). [Découvrez comment obtenir des informations d’identification](https://developers.google.com/admin-sdk/reports/v1/quickstart/python).

## <a name="configure-and-connect-google-workspace"></a>Configurer et connecter Google Workspace

Google Workspace peut intégrer et exporter des journaux directement dans Azure Sentinel à l’aide d’une application de fonction Azure.

> [!NOTE]
> Ce connecteur utilise Azure Functions pour se connecter à l’API des rapports Google afin d’extraire des événements d’activité dans Azure Sentinel. Il peut en résulter des coûts supplémentaires d’ingestion des données. Pour plus d’informations, consultez la page [Tarifs d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

1. Dans le portail Azure Sentinel, cliquez sur **Connecteurs de données**. 

1. Sélectionnez **Google Workspace (G suite) (préversion)** dans la galerie de connecteurs, puis sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les étapes décrites dans la section **Configuration** de la page du connecteur.

## <a name="validate-connectivity-and-find-your-data"></a>Valider la connectivité et rechercher vos données

L’opération peut prendre jusqu’à 20 minutes jusqu’à être en mesure de voir les données ingérées dans Azure Sentinel.

Une fois la connexion établie, les données s’affichent dans **Journaux**, sous la section **Journaux personnalisés**, dans les tables suivantes :
- `GWorkspace_ReportsAPI_admin_CL`
- `GWorkspace_ReportsAPI_calendar_CL`
- `GWorkspace_ReportsAPI_drive_CL`
- `GWorkspace_ReportsAPI_login_CL`
- `GWorkspace_ReportsAPI_mobile_CL`
- `GWorkspace_ReportsAPI_token_CL`
- `GWorkspace_ReportsAPI_user_accounts_CL`

Ce connecteur de données dépend d’un analyseur basé sur une fonction Kusto pour pouvoir fonctionner normalement. [Procédez comme suit](https://aka.ms/sentinel-GWorkspaceReportsAPI-parser) pour créer l’alias de fonctions Kusto **GWorkspaceActivityReports**.

Consultez l’onglet **Étapes suivantes** de la page du connecteur pour obtenir des exemples de requêtes utiles.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment connecter Google Workspace à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.
