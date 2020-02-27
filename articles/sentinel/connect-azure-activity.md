---
title: Connecter des données d’activité Azure à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données d’activité Azure à Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 28d5acd80deef193c7d2fea46f682f26abd756aa
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588601"
---
# <a name="connect-data-from-azure-activity-log"></a>Connecter des données à partir du journal d’activité Azure



Vous pouvez diffuser des journaux dans Azure Sentinel en un seul clic à partir du [journal d’activité Azure](../azure-monitor/platform/platform-logs-overview.md). Le journal d’activité est un journal d’abonnement qui fournit un aperçu de tous les événements relatifs aux abonnements qui se sont produits dans Azure. Cela inclut une plage de données, à partir de données opérationnelles d’Azure Resource Manager pour les mises à jour des événements de l’état d’intégrité du service. Avec le journal d’activité, vous pouvez déterminer « quoi, qui et quand » pour toutes les opérations d’écriture (PUT, POST, DELETE) sur les ressources de votre abonnement. Vous pouvez également comprendre l’état de l’opération et d’autres propriétés pertinentes. Le journal d’activité n’inclut pas d’opérations de lecture (GET) ni d’opérations pour les ressources qui utilisent le modèle Classic/« RDFE ». 


## <a name="prerequisites"></a>Prérequis

- Utilisateur disposant d’autorisations de contributeur pour l’espace de travail Log Analytics 


## <a name="connect-to-azure-activity-log"></a>Connexion au journal d’activité Azure

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette du **journal d’activité Azure**.

2. Dans le volet de journal d’activité Azure, sélectionnez les abonnements que vous souhaitez diffuser en continu dans Azure Sentinel. 

3. Cliquez sur **Connecter**.

4. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes d’activité Azure, recherchez **AzureActivity**.


 

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter le journal d’activité Azure à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
