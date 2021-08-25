---
title: Connecter des données CyberArk EPV à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur de données CyberArk Enterprise Password Vault (EPV) pour extraire ses journaux dans Azure Sentinel. Affichez les données CyberArk EPV dans des classeurs, créez des alertes et améliorez les enquêtes.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: 0b8db5dfa399aaef087adb0ec5c2d67b91fceab9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694042"
---
# <a name="connect-cyberark-enterprise-password-vault-epv-to-azure-sentinel"></a>Connecter CyberArk Enterprise Password Vault (EPV) à Azure Sentinel

> [!IMPORTANT]
> Le connecteur de données CyberArk Enterprise Password Vault (EPV) dans Azure Sentinel est actuellement disponible en préversion publique. Cette fonctionnalité est fournie sans contrat de niveau de service. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Le connecteur CyberArk Syslog vous permet de connecter facilement les journaux de votre solution de sécurité CyberArk à Azure Sentinel, de consulter des tableaux de bord, de créer des alertes personnalisées et d’améliorer les enquêtes. L’intégration entre CyberArk et Azure Sentinel utilise le connecteur de données CEF pour analyser et afficher correctement les messages CyberArk Syslog.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="configure-and-connect-cyberark-epv"></a>Configuration et connexion de CyberArk EPV

Les journaux CyberArk EPV sont envoyés de Vault à un serveur de transfert de journaux basés sur Linux (exécutant rsyslog ou syslog-ng) sur lequel l’agent Log Analytics est installé, qui exporte les journaux vers Azure Sentinel. Si vous n’avez pas de serveur de transfert de journaux de ce type, consultez [ces instructions](connect-cef-agent.md) pour en obtenir un.

1. Dans le portail Azure Sentinel, cliquez sur **Connecteurs de données**, sélectionnez **CyberArk Enterprise Password Vault (EPV) Events (Preview)** , puis **Ouvrir la page du connecteur**.

1. Suivez les [instructions CyberArk EPV](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm) pour configurer l’envoi de données syslog au serveur de transfert de journaux.

1. Validez votre connexion et vérifiez l’ingestion des données à l’aide de [ces instructions](troubleshooting-cef-syslog.md#validate-cef-connectivity). Environ 20 minutes peuvent être nécessaires avant que vos journaux commencent à apparaître dans Log Analytics.

## <a name="find-your-data"></a>Recherche de données

Après l’établissement d’une connexion réussie, les données s’affichent dans **Journaux**, sous la section **Azure Sentinel** dans le tableau *CommonSecurityLog*.

Pour interroger les journaux CyberArk EPV dans Log Analytics, entrez `CommonSecurityLog` en haut de la fenêtre de requête.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter des journaux CyberArk Enterprise Password Vault à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.