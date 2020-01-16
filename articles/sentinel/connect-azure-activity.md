---
title: Connecter des données d’activité Azure à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données d’activité Azure à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: 4c451c62a16a70d85d75ee00c3e08758e27425f6
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749978"
---
# <a name="connect-data-from-azure-activity-log"></a>Connecter des données à partir du journal d’activité Azure



Vous pouvez diffuser des journaux dans Azure Sentinel en un seul clic à partir du [journal d’activité Azure](../azure-monitor/platform/platform-logs-overview.md). Le journal d’activité est un journal d’abonnement qui fournit un aperçu de tous les événements relatifs aux abonnements qui se sont produits dans Azure. Cela inclut une plage de données, à partir de données opérationnelles d’Azure Resource Manager pour les mises à jour des événements de l’état d’intégrité du service. Avec le journal d’activité, vous pouvez déterminer « quoi, qui et quand » pour toutes les opérations d’écriture (PUT, POST, DELETE) sur les ressources de votre abonnement. Vous pouvez également comprendre l’état de l’opération et d’autres propriétés pertinentes. Le journal d’activité n’inclut pas d’opérations de lecture (GET) ni d’opérations pour les ressources qui utilisent le modèle Classic/« RDFE ». 


## <a name="prerequisites"></a>Conditions préalables requises

- Utilisateur, doté d’autorisations d’administrateur général ou d’administrateur de la sécurité


## <a name="connect-to-azure-activity-log"></a>Connexion au journal d’activité Azure

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette du **journal d’activité Azure**.

2. Dans le volet de journal d’activité Azure, sélectionnez les abonnements que vous souhaitez diffuser en continu dans Azure Sentinel. 

3. Cliquez sur **Connecter**.

4. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes d’activité Azure, recherchez **AzureActivity**.


 

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter le journal d’activité Azure à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
