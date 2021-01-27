---
title: Connecter des données Proofpoint On Demand Email Security à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur de données Proofpoint On Demand Email Security pour tirer des journaux POD Email Security dans Azure Sentinel. Affichez les données POD Email Security dans des classeurs, créez des alertes et améliorez l’investigation.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: bdc9cbf942b88af93befa34f73eb3a90469cfcef
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98873499"
---
# <a name="connect-your-proofpoint-on-demand-email-security-pod-solution-to-azure-sentinel"></a>Connecter votre solution Proofpoint On Demand Email Security (POD) à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Proofpoint On Demand Email Security est actuellement disponible en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Cet article explique comment connecter votre appliance Proofpoint On Demand Email Security à Azure Sentinel. Le connecteur de données POD vous permet de connecter facilement vos journaux POD à Azure Sentinel, ce qui vous permet ensuite de consulter les données dans des classeurs, de les utiliser pour créer des alertes personnalisées et de les incorporer pour améliorer l’investigation.  L’intégration entre Proofpoint On Demand Email Security et Azure Sentinel utilise l’API WebSocket.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture sur l’espace de travail Azure Sentinel.

- Vous devez disposer d’autorisations de lecture sur les clés partagées pour accéder à l’espace de travail. [En savoir plus sur les clés d’espace de travail](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

- Vous devez disposer d’autorisations en lecture et en écriture sur Azure Functions pour créer une application de fonction. [En savoir plus sur Azure Functions](../azure-functions/index.yml).

- Vous devez disposer des informations d’identification de l’API WebSocket suivantes : ProofpointClusterID, ProofpointToken. [En savoir plus sur l’API WebSocket](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API).

## <a name="configure-and-connect-proofpoint-on-demand-email-security"></a>Configurer et connecter Proofpoint On Demand Email Security

Proofpoint On Demand Email Security peut intégrer et exporter les journaux directement dans Azure Sentinel.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez **Proofpoint On Demand Email Security (préversion)** , puis sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les étapes décrites dans la section **Configuration** de la page du connecteur.

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion établie, les données s’affichent dans **Journaux**, sous *CustomLogs*, dans les tables suivantes :
- `ProofpointPOD_message_CL`
- `ProofpointPOD_maillog_CL`

Consultez l’onglet **Étapes suivantes** de la page du connecteur pour obtenir des exemples de requêtes utiles.

## <a name="validate-connectivity"></a>Valider la connectivité

Jusqu’à 60 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter Proofpoint On Demand Email Security à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.