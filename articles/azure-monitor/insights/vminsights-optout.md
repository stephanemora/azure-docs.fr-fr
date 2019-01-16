---
title: Guide pratique pour désactiver la surveillance avec Azure Monitor pour machines virtuelles (préversion) | Microsoft Docs
description: Cet article explique comment arrêter la surveillance de vos machines virtuelles avec Azure Monitor pour machines virtuelles.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: f0addcc0b6220bcf877c3cac20f2d1376eb19e61
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191069"
---
# <a name="how-to-disable-monitoring-of-your-virtual-machines-with-azure-monitor-for-vms-preview"></a>Guide pratique pour désactiver la surveillance de vos machines virtuelles avec Azure Monitor pour machines virtuelles (préversion)

Si après avoir activé la surveillance de vos machines virtuelles vous décidez que vous ne souhaitez plus les surveiller avec Azure Monitor pour machines virtuelles, vous pouvez désactiver cette surveillance. Cet article explique comment accomplir cette tâche pour une ou plusieurs machines virtuelles.  

Actuellement, Azure Monitor pour machines virtuelles ne permet pas de désactivant individuellement la surveillance des machines virtuelles. Si votre espace de travail Log Analytics est configuré pour prendre en charge cette solution et d’autres solutions et pour collecter d’autres données d’analyse, il est important de comprendre l’impact et les méthodes décrites ci-dessous avant de continuer.

Azure Monitor pour machines virtuelles s’appuie sur les composants suivants :

* Un espace de travail Log Analytics, qui stocke les données d’analyse collectées sur les machines virtuelles et d’autres sources.
* Collection de compteurs de performances configurés dans l’espace de travail, qui met à jour la configuration de la surveillance sur toutes les machines virtuelles connectées à l’espace de travail.
* Deux solutions de surveillance configurées dans l’espace de travail : **InfrastructureInsights** et **ServiceMap**, qui mettent à jour la configuration de la surveillance sur toutes les machines virtuelles connectées à l’espace de travail.
* Deux extensions de machine virtuelle Azure, **MicrosoftMonitoringAgent** et **DepenendencyAgent**, qui collectent et envoient les données à l’espace de travail.

Tenez compte des points suivants lorsque vous vous préparez à désactiver la surveillance de vos machines virtuelles avec Azure Monitor pour machines virtuelles (préversion) :

* Si vous évaluez avec une seule machine virtuelle et que vous avez accepté l’espace de travail Log Analytics présélectionné, vous pouvez désactiver la surveillance en désinstallant l’agent de dépendances de la machine virtuelle et en déconnectant l’agent Log Analytics de cet espace de travail. Cette approche est adaptée si vous avez l’intention d’utiliser la machine virtuelle à d’autres fins et que vous décidez ultérieurement de la reconnecter à un autre espace de travail.
* Si vous utilisez l’espace de travail Log Analytics pour prendre en charge d’autres solutions de surveillance et la collecte de données à partir d’autres sources, vous pouvez supprimer les composants de la solution Azure Monitor pour machines virtuelles de l’espace de travail sans interrompre ni affecter votre espace de travail.  

>[!NOTE]
> Après avoir supprimé les composants de la solution de votre espace de travail, vous pouvez continuer à voir l’état d’intégrité de vos machines virtuelles Azure, plus précisément les données de performances et de mappage lorsque vous accédez à une de ces vues dans le portail. Les données cesseront d’apparaître dans les vues de performances et de mappage après quelques temps. Toutefois la vue de contrôle d’intégrité continue d’afficher l’état de vos machines virtuelles. L’option **Essayer maintenant** sera disponible sur la machine virtuelle Azure sélectionnée pour pouvoir réactiver la surveillance plus tard.  

## <a name="complete-removal-of-azure-monitor-for-vms"></a>Suppression complète d’Azure Monitor pour machines virtuelles

Les étapes suivantes décrivent comment supprimer complètement Azure Monitor pour machines virtuelles, si vous avez besoin toujours l’espace de travail Log Analytics. Vous vous apprêtez à supprimer les solutions **InfastructureInsights** et **ServiceMap** de l’espace de travail.  

>[!NOTE]
>Si vous utilisiez la solution de surveillance Service Map avant d’activer Azure Monitor pour machines virtuelles et que vous l’utilisez toujours, ne supprimez pas cette solution comme décrit dans l’étape 6 ci-dessous.  
>

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).
2. Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste des ressources, tapez Log Analytics. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.
3. Dans votre liste d’espaces de travail Log Analytics, sélectionnez celui que vous avez choisi lors de l’intégration d’Azure Monitor pour machines virtuelles.
4. Dans le volet de gauche, sélectionnez **Solutions**.  
5. Dans la liste des solutions, sélectionnez **InfrastructureInsights(nom de l’espace de travail)**, puis, dans la page **Vue d’ensemble** de la solution, cliquez sur **Supprimer**.  À l’invite de confirmation, cliquez sur **Yes**.  
6. Dans la liste des solutions, sélectionnez **ServiceMap(nom de l’espace de travail)**, puis, dans la page **Vue d’ensemble** de la solution, cliquez sur **Supprimer**.  À l’invite de confirmation, cliquez sur **Yes**.  

Si avant d’intégrer Azure Monitor pour machines virtuelles, vous ne [collectiez pas les compteurs de performances activés](vminsights-onboard.md?toc=/azure/azure-monitor/toc.json#performance-counters-enabled) pour les machines virtuelles Windows ou Linux dans votre espace de travail, vous devez désactiver ces règles selon les étapes décrites [ici](../../azure-monitor/platform/data-sources-performance-counters.md?toc=/azure/azure-monitor/toc.json#configuring-performance-counters) pour Windows et Linux.

## <a name="disable-monitoring-for-an-azure-vm-and-retain-workspace"></a>Désactiver la surveillance pour une machine virtuelle Azure et conserver l’espace de travail  

Les étapes suivantes décrivent comment désactiver la surveillance d’une machine virtuelle qui a été activée pour évaluer Azure Monitor pour machines virtuelles, mais l’espace de travail Log Analytics est toujours nécessaire pour prendre en charge la surveillance d’autres sources. S’il s’agit d’une machine virtuelle Azure, vous allez supprimer l’agent de dépendances de l’extension de machine virtuelle et l’extension de machine virtuelle de l’agent Log Analytics pour Windows/Linux directement sur la machine virtuelle. 

>[!NOTE]
>Si la machine virtuelle est gérée par Azure Automation pour orchestrer les processus, gérer la configuration ou gérer les mises à jour ou bien gérée par Azure Security Center pour la gestion de la sécurité et la détection des menaces, l’agent Log Analytics ne doit pas être supprimé. Sinon, vous empêchez ces services et ces solutions de gérer de façon proactive votre machine virtuelle. 

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com). 
2. Dans le portail Azure, sélectionnez **Machines virtuelles**. 
3. Sélectionnez une machine virtuelle dans la liste. 
4. Dans le volet de gauche, sélectionnez **Extensions** puis, dans la page **Extensions**, sélectionnez **DependencyAgent**.
5. Dans la page des propriétés de l’extension, cliquez sur **Désinstaller**.
6. Dans la page **Extensions**, sélectionnez **MicrosoftMonitoringAgent** et dans la page des propriétés de l’extension, cliquez sur **Désinstaller**.  
