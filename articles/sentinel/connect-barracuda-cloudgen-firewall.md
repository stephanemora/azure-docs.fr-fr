---
title: Connecter Barracuda CloudGen Firewall à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter Barracuda CloudGen Firewall à Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 1ff3d5481b02e587d993bb4a5d7abf6aecd22ee8
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122534877"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>Connecter Barracuda CloudGen Firewall

Le connecteur Barracuda CloudGen Firewall (CGFW) vous permet de connecter facilement vos journaux Barracuda CGFW à Azure Sentinel, pour consulter des tableaux de bord, créer des alertes personnalisées et améliorer les enquêtes. Cela vous donne plus d’informations sur le réseau de votre organisation et améliore vos capacités d’opération de sécurité.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>Prérequis

- Lire et écrire des autorisations dans l’espace de travail Azure Sentinel.

- Barracuda CloudGen Firewall doit être configuré pour exporter les journaux via Syslog.

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>Connecter Azure Sentinel à Barracuda CloudGen Firewall

1. Dans le Portail Azure, accédez à **Azure Sentinel** > **Connecteurs de données**, puis sélectionnez le connecteur **Barracuda CloudGen Firewall**.

1. Sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les instructions de la page **Barracuda CloudGen Firewall**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter Barracuda CloudGen Firewall à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.