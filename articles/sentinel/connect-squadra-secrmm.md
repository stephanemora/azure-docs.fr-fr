---
title: Connecter des données Squadra Technologies secRMM à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données Squadra Technologies secRMM à Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: bd599fbfc712d85ec5fac0a3aa6f9f1cd320d91c
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122534850"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Connecter vos données Squadra Technologies secRMM à Azure Sentinel 

Le connecteur Squadra Technologies secRMM vous permet de connecter facilement les journaux de la solution de sécurité Squadra Technologies secRMM à Azure Sentinel. Il vous permet de voir des tableaux de bord, de créer des alertes personnalisées et d’améliorer les enquêtes. Ce connecteur vous donne des insights sur les événements liés au stockage amovible USB. L’intégration entre Squadra Technologies secRMM et Azure Sentinel est basée sur l’utilisation de l’API REST.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Configurer et connecter Squadra Technologies secRMM 

La solution Squadra Technologies secRMM peut intégrer et exporter des journaux directement vers Azure Sentinel.
1. Dans le portail Azure Sentinel, cliquez sur Data Connectors (Connecteurs de données), sélectionnez Squadra Technologies secRMM, puis Open Connector Page (Ouvrir la page du connecteur).

2. Suivez les étapes décrites dans le [guide d’intégration de Squadra Technologies pour Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) afin de récupérer des données Squadra secRMM dans Azure Sentinel.   

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion établie, les données s’affichent dans **Journaux**, sous la section **CustomLogs**, dans la table `secRMM_CL`.

Pour interroger les journaux Squadra Technologies secRMM, entrez le nom de table en haut de la fenêtre de requête.

## <a name="validate-connectivity"></a>Valider la connectivité

Jusqu’à 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter Squadra Technologies secRMM à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
- [Utilisez des classeurs](monitor-your-data.md) pour superviser vos données.