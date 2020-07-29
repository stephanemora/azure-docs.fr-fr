---
title: Connecter des données Pulse Connect Secure à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données Pulse Connect Secure à Azure Sentinel.
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
ms.openlocfilehash: 01d41b20c543262280e215cf4e31fd0cccce877f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527938"
---
# <a name="connect-your-pulse-connect-secure-to-azure-sentinel"></a>Connecter votre solution Pulse Connect Secure à Azure Sentinel

Cet article explique comment connecter votre appliance [Pulse Connect Secure](https://www.pulsesecure.net/products/pulse-connect-secure/) à Azure Sentinel. Le connecteur de données Pulse Connect Secure vous permet de connecter facilement vos journaux Pulse Connect Secure à Azure Sentinel, de consulter des tableaux de bord, de créer des alertes personnalisées et d’améliorer l’investigation. L'intégration entre Pulse Connect Secure et Azure Sentinel utilise Syslog.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="forward-pulse-connect-secure-logs-to-the-syslog-agent"></a>Transférer les journaux Pulse Connect Secure à l’agent Syslog  

Configurez Pulse Connect Secure pour transférer les messages Syslog à votre espace de travail Azure par le biais de l’agent Syslog.

1. Dans le portail Azure Sentinel, cliquez sur **Connecteurs de données** et sélectionnez le connecteur **Pulse Connect Secure**.

1. Sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les instructions figurant sur la page **Pulse Connect Secure**.

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion correctement établie, les données apparaissent dans l’espace de travail Log Analytics sous Syslog.

## <a name="validate-connectivity"></a>Valider la connectivité

Plus de 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter Pulse Connect Secure à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.