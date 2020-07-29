---
title: Connecter les données du système d’exploitation des identités réseau Infoblox à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter les données du système d’exploitation des identités réseau Infoblox à Azure Sentinel.
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
ms.openlocfilehash: ed4f2d769dbda3dec7b353fddfd1e5e0f3d00f9b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527980"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>Connexion de votre système d’exploitation des identités réseau Infoblox à Azure Sentinel

Cet article explique comment connecter votre appliance [du système d’exploitation des identités réseau Infoblox](https://www.infoblox.com/glossary/network-identity-operating-system-nios/) à Azure Sentinel. Le connecteur de données du système d’exploitation des identités réseau Infoblox vous permet de connecter facilement vos journaux Infoblox, de consulter des tableaux de bord, de créer des alertes personnalisées et d’améliorer les enquêtes. L'intégration entre le système d’exploitation des identités réseau Infoblox et Azure Sentinel utilise Syslog.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>Transférer des journaux Infoblox à l’agent Syslog  

Configurez Infoblox pour transférer les messages Syslog à votre espace de travail Azure par le biais de l’agent Syslog.

1. Dans le portail Azure Sentinel, cliquez sur **Data Connectors** (Connecteurs de données) et sélectionnez le connecteur **Infoblox NIOS**.

1. Sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les instructions sur la page **Infoblox NIOS**.

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion correctement établie, les données apparaissent dans l’espace de travail Log Analytics sous Syslog.

## <a name="validate-connectivity"></a>Valider la connectivité

Plus de 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter les appliances CEF à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.