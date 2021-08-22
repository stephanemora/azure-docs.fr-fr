---
title: Connecter des données de journaux DNS Windows à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur de données des journaux DNS NXLog pour extraire les événements Windows DNS dans Azure Sentinel. Affichez les données DNS Windows dans des classeurs, créez des alertes et améliorez l’investigation.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: b80141d79807aea89e328b166d419e583a646ad5
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122525966"
---
# <a name="connect-your-nxlog-windows-dns-logs-to-azure-sentinel"></a>Connecter vos journaux DNS Windows NXLog à Azure Sentinel

> [!IMPORTANT]
> Le connecteur des journaux DNS NXLog est actuellement disponible en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Le connecteur [Journaux DNS NXLog](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html) vous permet d’exporter facilement tous vos événements de serveur Windows DNS vers Azure Sentinel en temps réel, ce qui vous donne un aperçu de l’activité du serveur de noms de domaine au sein de votre organisation. Il utilise le [Suivi des événements pour Windows (ETW)](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html#dns_windows_etw) haute performance pour collecter les événements de serveur DNS d’audit et d’analyse en temps réel, et prend en charge l’enrichissement des événements avec des champs personnalisés. L’intégration entre les journaux DNS NXLog et Azure Sentinel est facilitée via l’API REST.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="configure-and-connect-nxlog-dns-logs"></a>Configurer et connecter les journaux DNS NXLog

NXLog peut être configuré pour envoyer des événements au format JSON directement à Azure Sentinel.

1. Dans le portail Azure Sentinel, cliquez sur **Connecteurs de données** et sélectionnez le connecteur **NXLog DNS Logs** (Journaux DNS NXLog).

1. Sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les instructions pas à pas de la rubrique Intégration du *Guide de l’utilisateur NXLog* [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) pour configurer le transfert via l’API REST.

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion établie, les données s’affichent dans **Journaux**, sous la section **Custom Logs** (Journaux personnalisés), dans la table *DNS_Logs_CL*.

## <a name="validate-connectivity"></a>Valider la connectivité

Jusqu’à 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à utiliser NXLog pour ingérer les journaux DNS Windows dans Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.