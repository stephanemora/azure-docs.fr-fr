---
title: Désactiver la surveillance dans Azure Monitor pour machines virtuelles (préversion) | Microsoft Docs
description: Cet article explique comment arrêter la surveillance de vos machines virtuelles dans Azure Monitor pour machines virtuelles.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/05/2018
ms.openlocfilehash: fb4347e610920380792a17bb620e6d97a7d72505
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669503"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>Désactiver la surveillance de vos machines virtuelles dans Azure Monitor pour machines virtuelles (préversion)

Après avoir activé la surveillance de vos machines virtuelles, vous pouvez choisir de désactiver cette surveillance dans Azure Monitor pour machines virtuelles. Cet article explique comment désactiver la surveillance d’une ou plusieurs machines virtuelles.  

Actuellement, Azure Monitor pour machines virtuelles ne prend pas en charge la désactivation sélective de la surveillance des machines virtuelles. Votre espace de travail Log Analytics peut prendre en charge Azure Monitor pour machines virtuelles et d’autres solutions. Il peut également collecter d’autres données de surveillance. Si votre espace de travail Log Analytics fournit ces services, vous devez comprendre l’effet et les méthodes de désactivation de la surveillance avant de commencer.

Azure Monitor pour machines virtuelles s’appuie sur les composants suivants :

* Un espace de travail Log Analytics, qui stocke les données de surveillance collectées à partir des machines virtuelles et d’autres sources.
* Une collection de compteurs de performances configurés dans l’espace de travail. Cette collection met à jour la configuration de la surveillance sur toutes les machines virtuelles connectées à l’espace de travail.
* `InfrastructureInsights` et `ServiceMap`, qui sont des solutions de surveillance configurées dans l’espace de travail. Ces solutions mettent à jour la configuration de la surveillance sur toutes les machines virtuelles connectées à l’espace de travail.
* `MicrosoftMonitoringAgent` et `DependencyAgent`, qui sont des extensions de machine virtuelle Azure. Ces extensions collectent et envoient des données à l’espace de travail.

Lorsque vous vous préparez à désactiver la surveillance de vos machines virtuelles, n’oubliez pas les considérations suivantes :

* Si vous évaluiez avec une seule machine virtuelle et que vous utilisiez l’espace de travail Log Analytics présélectionné par défaut, vous pouvez désactiver la surveillance en désinstallant l’agent de dépendances de la machine virtuelle et en déconnectant l’agent Log Analytics de cet espace de travail. Cette approche est adaptée si vous avez l’intention d’utiliser la machine virtuelle à d’autres fins et que vous décidez ultérieurement de la reconnecter à un autre espace de travail.
* Si vous avez sélectionné un espace de travail Log Analytics préexistant qui prend en charge d’autres solutions de surveillance et la collecte de données à partir d’autres sources, vous pouvez supprimer les composants de la solution de l’espace de travail sans interrompre ni affecter votre espace de travail.  

>[!NOTE]
> Après avoir supprimé les composants de la solution de votre espace de travail, vous pouvez continuer à voir l’état d’intégrité de vos machines virtuelles Azure. Plus précisément, vous voyez les données de performances et de mappage lorsque vous accédez à l’une de ces vues dans le portail. Les données cesseront éventuellement d’apparaître dans les vues **Performances** et **Mappage**. Toutefois, la vue **Intégrité** continuera d’afficher l’état d’intégrité pour vos machines virtuelles. L’option **Essayer maintenant** sera disponible sur la machine virtuelle Azure sélectionnée et vous permettra de réactiver ultérieurement la surveillance.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Supprimer complètement Azure Monitor pour machines virtuelles

Si vous avez encore besoin de l’espace de travail Log Analytics, procédez comme suit pour supprimer complètement Azure Monitor pour machines virtuelles. Vous allez supprimer les solutions `InfrastructureInsights` et `ServiceMap` à partir de l’espace de travail.  

>[!NOTE]
>Si vous utilisiez la solution de surveillance Service Map avant d’activer Azure Monitor pour machines virtuelles et que vous l’utilisez toujours, ne supprimez pas cette solution comme cela est décrit dans la dernière étape de la procédure suivante.  
>

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le portail Azure, sélectionnez **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste filtre les suggestions. Sélectionnez **Log Analytics**.
3. Dans votre liste d’espaces de travail Log Analytics, sélectionnez celui que vous avez choisi lors de l’activation d’Azure Monitor pour machines virtuelles.
4. Sur la gauche, sélectionnez **Solutions**.  
5. Dans la liste des solutions, sélectionnez **InfrastructureInsights(nom d’espace de travail)** . Dans la page **Vue d’ensemble** pour la solution, sélectionnez **Supprimer**. À l’invite de confirmation, sélectionnez **Oui**.  
6. Dans la liste des solutions, sélectionnez **ServiceMap(nom d’espace de travail)** . Dans la page **Vue d’ensemble** pour la solution, sélectionnez **Supprimer**. À l’invite de confirmation, sélectionnez **Oui**.  

Avant l’activation d’Azure Monitor pour machines virtuelles, si vous n’avez pas [collecté les compteurs de performances](vminsights-enable-overview.md#performance-counters-enabled) pour les machines virtuelles Windows ou Linux dans votre espace de travail, [désactivez ces règles](../platform/data-sources-performance-counters.md#configuring-performance-counters) pour Windows et Linux.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Désactiver la surveillance et conserver l’espace de travail  

Si votre espace de travail Log Analytics a encore besoin de prendre en charge la surveillance à partir d’autres sources, procédez comme suit pour désactiver la surveillance sur la machine virtuelle que vous utilisiez pour évaluer Azure Monitor pour machines virtuelles. Pour les machines virtuelles Azure, vous allez supprimer l’extension de machine virtuelle de l’agent de dépendances et l’extension de machine virtuelle de l’agent Log Analytics pour Windows ou Linux directement sur la machine virtuelle. 

>[!NOTE]
>Ne supprimez pas l’agent Log Analytics si : 
>
> * Azure Automation gère la machine virtuelle pour orchestrer les processus ou pour gérer la configuration ou les mises à jour. 
> * Azure Security Center gère la machine virtuelle pour la sécurité et la détection des menaces. 
>
> Si vous supprimez l’agent Log Analytics, vous empêchez ces services et ces solutions de gérer de façon proactive votre machine virtuelle. 

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
2. Dans le portail Azure, sélectionnez **Machines virtuelles**. 
3. Sélectionnez une machine virtuelle dans la liste. 
4. Sur la gauche, sélectionnez **Extensions**. Dans la page **Extensions**, sélectionnez **DependencyAgent**.
5. Dans la page des propriétés de l’extension, sélectionnez **Désinstaller**.
6. Dans la page **Extensions**, sélectionnez **MicrosoftMonitoringAgent**. Dans la page des propriétés de l’extension, sélectionnez **Désinstaller**.  
