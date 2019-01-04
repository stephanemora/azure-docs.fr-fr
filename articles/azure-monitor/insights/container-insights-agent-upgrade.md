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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 08206b9de4fca3b422c5b076d7e0c1c180f82fbd
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184081"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>Guide pratique pour mettre à niveau l'agent Azure Monitor pour conteneurs (préversion) | Microsoft Docs
Azure Monitor pour conteneurs utilise une version en conteneur de l’agent Log Analytics pour Linux. Lorsqu'une nouvelle version de l’agent est disponible, celui-ci est automatiquement mis à niveau sur vos clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS).  

Si la mise à niveau de l’agent échoue, cet article décrit le processus pour mettre à niveau l’agent manuellement. Pour suivre les versions publiées, consultez [Annonces des versions de l’agent](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Mise à niveau de l’agent sur un cluster Kubernetes surveillé
Le processus de mise à niveau de l’agent se compose de deux étapes simples. La première étape consiste à désactiver la surveillance avec Azure Monitor pour conteneurs à l’aide de l'interface de ligne de commande Azure.  Suivez les étapes décrites dans l'article [Désactiver la surveillance](container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli). L'interface de ligne de commande Azure permet de supprimer l’agent des nœuds du cluster, sans incidence sur la solution et les données correspondantes stockées dans l’espace de travail. 

>[!NOTE]
>Lors de cette activité de maintenance, les nœuds du cluster ne transfèrent pas les données collectées et les vues de performances n'affichent pas les données entre le moment où vous supprimez l'agent et celui où vous installez la nouvelle version. 
>

Pour installer la nouvelle version de l’agent, suivez les étapes décrites dans l'article [Surveillance de l'intégration](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli) à l'aide de l'interface de ligne de commande pour terminer ce processus.  

Après avoir activé la surveillance, 15 minutes peuvent s’écouler avant que vous puissiez voir les métriques d’intégrité mis à jour du cluster. Pour vérifier que l’agent a bien été mis à niveau, exécutez la commande : `kubectl logs omsagent-484hw --namespace=kube-system`

L’état doit ressembler à l’exemple suivant, où la valeur *omi* et *omsagent* doit correspondre à la dernière version spécifiée dans [l’historique des versions de l’agent](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes lors de la mise à niveau de l'agent, consultez le [guide de résolution des problèmes](container-insights-troubleshoot.md).