---
title: Guide pratique pour mettre à niveau Azure Monitor pour l'agent des conteneurs (préversion) | Microsoft Docs
description: Cet article explique comment mettre à jour l'agent Log Analytics utilisé par Azure Monitor pour les conteneurs.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: fb45002d284bc28dfb7093f69cfac7aae0681e8d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628367"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>Guide pratique pour mettre à niveau l'agent Azure Monitor pour conteneurs (préversion) | Microsoft Docs
Azure Monitor pour conteneurs utilise une version en conteneur de l’agent Log Analytics pour Linux. Lorsqu'une nouvelle version de l’agent est disponible, celui-ci n’est pas automatiquement mis à niveau sur vos clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS).

Cet article explique le processus de mise à niveau de l’agent.

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Mise à niveau de l’agent sur un cluster Kubernetes surveillé
Le processus de mise à niveau de l’agent se compose de deux étapes simples. La première étape consiste à désactiver la surveillance avec Azure Monitor pour conteneurs à l’aide de l'interface de ligne de commande Azure.  Suivez les étapes décrites dans l'article [Désactiver la surveillance](monitoring-container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli). L'interface de ligne de commande Azure permet de supprimer l’agent des nœuds du cluster, sans incidence sur la solution et les données correspondantes stockées dans l’espace de travail. 

>[!NOTE]
>Lors de cette activité de maintenance, les nœuds du cluster ne transfèrent pas les données collectées et les vues de performances n'affichent pas les données entre le moment où vous supprimez l'agent et celui où vous installez la nouvelle version. 
>

Pour installer la nouvelle version de l’agent, suivez les étapes décrites dans l'article [Surveillance de l'intégration](monitoring-container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli) à l'aide de l'interface de ligne de commande pour terminer ce processus.  

Après avoir activé la surveillance, 15 minutes peuvent s’écouler avant que vous puissiez voir les métriques d’intégrité mis à jour du cluster. 

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes lors de la mise à niveau de l'agent, consultez le [guide de résolution des problèmes](monitoring-container-insights-troubleshoot.md).