---
title: Connecter des données Proofpoint Targeted Attack Protection (TAP) à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données Proofpoint Targeted Attack Protection (TAP) à Azure Sentinel.
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
ms.openlocfilehash: e4185de879fa6136531e6d4c64908befa1d3bc45
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527939"
---
# <a name="connect-your-proofpoint-tap-to-azure-sentinel-with-azure-function"></a>Connecter votre solution Proofpoint TAP à l’aide d’Azure Functions

Le connecteur Proofpoint Targeted Attack Protection (TAP) vous permet de connecter facilement les journaux de votre solution de sécurité [Proofpoint TAP](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection) à Azure Sentinel, de consulter des tableaux de bord, de créer des alertes personnalisées et d’améliorer l’investigation. L’intégration entre Proofpoint TAP et Azure Sentinel utilise Azure Functions pour extraire des données de journal à l’aide de l’API REST.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="configure-and-connect-proofpoint-tap"></a>Configurer et connecter Proofpoint TAP

Azure Functions peut intégrer et extraire des événements et des journaux directement à partir de Proofpoint TAP et les transmettre à Azure Sentinel.

1. Dans le portail Azure Sentinel, cliquez sur **Connecteurs de données** et sélectionnez le connecteur **Proofpoint TAP**.

1. Sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les instructions figurant sur la page **Proofpoint TAP**.

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion correctement établie, les données apparaissent dans Log Analytics sous les tables **ProofpointTAPMessagesBlocked_CL**, **ProofpointTAPMessagesDelivered_CL**, **ProofpointTAPClicksPermitted_CL** et **ProofpointTAPClicksBlocked_CL**.

## <a name="validate-connectivity"></a>Valider la connectivité

Plus de 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter Proofpoint TAP à Azure Sentinel à l’aide d’applications de fonction Azure. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.
