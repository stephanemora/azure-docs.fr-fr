---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/27/2019
ms.author: cynthn
ms.openlocfilehash: 2978da7f2e7ec27ded6b5994570fa50a9032d0d2
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55985416"
---
Vous pouvez tirer parti de nombreuses possibilités d’analyser vos machines virtuelles en collectant, affichant et analysant les données de diagnostic et de journal. Pour [analyser](../articles/azure-monitor/overview.md) simplement votre machine virtuelle, vous pouvez utiliser l’écran Vue d’ensemble de la machine virtuelle sur le portail Azure. Vous pouvez utiliser des [extensions](../articles/virtual-machines/windows/extensions-features.md) pour configurer des diagnostics sur vos machines virtuelles afin de collecter des données de mesure supplémentaires. Vous pouvez également utiliser des options d’analyse plus avancées, telles que [Application Insights](../articles/azure-monitor/app/app-insights-overview.md) et [Log Analytics](../articles/azure-monitor/log-query/log-query-overview.md).

## <a name="diagnostics-and-metrics"></a>Mesures et diagnostics 

Vous pouvez définir et analyser la collecte de [données de diagnostic](https://docs.microsoft.com/cli/azure/vm/diagnostics) à l’aide de [mesures](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) dans le portail Azure, d’Azure CLI, d’Azure PowerShell et des API de programmation. Vous pouvez par exemple :

- **Observez les mesures de base de la machine virtuelle.** Sur l’écran vue d’ensemble du portail Azure, les mesures de base affichées incluent l’utilisation du processeur, l’utilisation du réseau, le total des octets sur le disque et les opérations de disque par seconde.

- **Activer la collecte des diagnostics de démarrage et les afficher à l’aide du portail Azure.** Lorsque vous importez votre propre image dans Azure, ou même lorsque vous démarrez une des images de plateforme, il peut y avoir de nombreuses raisons pour lesquelles une Machine Virtuelle passe en état non démarrable. Vous pouvez facilement activer les diagnostics de démarrage lorsque vous créez une machine virtuelle en cliquant sur **Activé** pour les Diagnostics de démarrage sous la section Analyse de l’écran Paramètres.

    Au démarrage des machines virtuelles, l’agent Diagnostics de démarrage capture la sortie du démarrage et la stocke dans le stockage Azure. Ces données peuvent être utilisées pour résoudre les problèmes de démarrage des machines virtuelles. Les diagnostics de démarrage ne sont pas activés automatiquement quand vous créez une machine virtuelle à l’aide d’outils en ligne de commande. Avant d’activer les diagnostics de démarrage, vous devez créer un compte de stockage pour stocker les journaux d’activité de démarrage. Si vous activez les diagnostics de démarrage dans le portail Azure, un compte de stockage est créé automatiquement pour vous.

    Si vous n’avez pas activé les diagnostics de démarrage lors de la création de la machine virtuelle, vous pouvez toujours l’activer ultérieurement à l’aide d[Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostics), ou d’un [modèle Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Activer la collecte des données de diagnostics du système d’exploitation invité.** Lorsque vous créez une machine virtuelle, vous avez la possibilité d’activer les diagnostics du système d’exploitation invité sur l’écran des paramètres. Si vous activez la collecte des données de diagnostic, l’[extension IaaSDiagnostics pour Linux](../articles/virtual-machines/linux/diagnostic-extension.md) ou l’[extension IaaSDiagnostics pour Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) est ajoutée à la machine virtuelle, ce qui vous permet de collecter des données de mémoire, de processeur et de disque supplémentaires.

    Les données de diagnostic collectées permettent de configurer la mise à l’échelle automatique de vos machines virtuelles. Vous pouvez également configurer des journaux d’activité pour stocker les données et configurer des alertes pour vous avertir de performances inappropriées.

## <a name="alerts"></a>Alertes

Vous pouvez créer des [alertes](../articles/azure-monitor/platform/alerts-overview.md) en fonction de mesures de performances spécifiques. Les alertes peuvent être utilisées par exemple pour notifier que l’utilisation moyenne de l’UC dépasse un certain seuil ou que l’espace disque disponible est inférieur à une certaine quantité. Les alertes peuvent être configurées dans le [portail Azure](../articles/azure-monitor/platform/alerts-classic-portal.md), à l’aide d’[Azure PowerShell](../articles/azure-monitor/platform/alerts-classic-portal.md#with-powershell), ou d’[Azure CLI](../articles/azure-monitor/platform/alerts-classic-portal.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

[Azure Service Health](../articles/service-health/service-health-overview.md) vous offre des conseils et un support personnalisés lorsque vous rencontrez des problèmes avec les services Azure. Il vous aide également à vous préparer aux maintenances planifiées à venir. Azure Service Health vous alerte vous et votre équipe à l’aide de notifications ciblées et flexibles.

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure Resource Health](../articles/service-health/resource-health-overview.md) vous aide à diagnostiquer les problèmes et à accéder au support quand un problème Azure a une incidence sur vos ressources. Il vous informe de l’intégrité (actuelle et passée) de vos ressources et vous aide à atténuer les problèmes. Resource Health propose un support technique dès lors que vous êtes confronté à des problèmes de service Azure et que vous avez besoin d’aide.

## <a name="azure-activity-log"></a>Journaux d’activité

Le [Journal d’activité Azure](../articles/azure-monitor/platform/activity-logs-overview.md) est un journal d’abonnement qui fournit un aperçu de tous les événements relatifs aux abonnements qui se sont produits dans Azure. Cela inclut une plage de données, à partir de données opérationnelles d’Azure Resource Manager pour les mises à jour des événements de l’état d’intégrité du service. Vous pouvez cliquer sur le journal d’activité dans le portail Azure pour afficher le journal de votre machine virtuelle.

Voici ce que vous pouvez faire avec le journal d’activité :

- Créer une [alerte basée sur un événement du journal d’activité](../articles/azure-monitor/platform/activity-logs-overview.md).
- [Diffusez-le en continu vers un Event Hub](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) pour qu’un service tiers ou une solution d’analyse personnalisée (p. ex. PowerBI) l’ingère.
- Analysez-le dans PowerBI à l’aide du [pack de contenu PowerBI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Enregistrez-le dans un compte de stockage](../articles/azure-monitor/platform/archive-activity-log.md) pour l’archivage ou l’inspection manuelle. Vous pouvez spécifier la durée de rétention (en jours) à l’aide du Profil de journal.

Vous pouvez également accéder aux données de journal d’activité à l’aide d’[Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), d’[Azure CLI](https://docs.microsoft.com/cli/azure/monitor), ou des [API REST Monitor](https://docs.microsoft.com/rest/api/monitor/).

Les [journaux de diagnostic Azure](../articles/azure-monitor/platform/diagnostic-logs-overview.md) sont des journaux émis par votre machine virtuelle qui fournissent des informations riches et fréquentes relatives à son opération. Le journal d’activité diffère du journal de diagnostic dans la mesure où il fournit un éclairage sur les opérations effectuées au sein de la machine virtuelle.

Voici ce que vous pouvez faire avec les journaux de diagnostic :

- [Enregistrez-les dans un compte de stockage](../articles/azure-monitor/platform/archive-diagnostic-logs.md) pour l’audit ou l’inspection manuelle. Vous pouvez spécifier la durée de rétention (en jours) à l’aide des paramètres de diagnostic des ressources.
- [Diffusez-les en streaming sur Event Hubs](../articles/azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) pour qu’un service tiers ou une solution d’analyse personnalisée (comme PowerBI) les ingère.
- Analysez-les avec [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Surveillance avancée

- [Azure Monitor](../articles/azure-monitor/overview.md) est un service qui supervise vos environnements cloud et locaux, afin de préserver leur disponibilité et leurs performances. Il offre une solution complète pour collecter, analyser et exploiter des données de télémétrie de vos environnements cloud et locaux. Il vous aide à comprendre le fonctionnement de vos applications et identifie de façon proactive les problèmes qui les affectent et les ressources dont elles dépendent. Vous pouvez installer une extension sur une [machine virtuelle Linux](../articles/virtual-machines/linux/extensions-oms.md) ou une [machine virtuelle Windows](../articles/virtual-machines/windows/extensions-oms.md) qui installe l’agent Log Analytics pour collecter des données de journal d’activité et les stocker dans un espace de travail Log Analytics.

    Pour les machines virtuelles Windows et Linux, la méthode recommandée pour collecter des journaux d’activité consiste à installer l’agent Log Analytics. Le moyen le plus simple pour installer l’agent de Log Analytics sur une machine virtuelle consiste à utiliser l’[extension de machine virtuelle Log Analytics](../articles/log-analytics/log-analytics-azure-vm-extension.md). L’utilisation de l’extension simplifie le processus d’installation et configure automatiquement l’agent pour qu’il envoie des données à l’espace de travail Log Analytics que vous spécifiez. L’agent est également mis à niveau automatiquement, de façon à ce que vous disposiez des fonctionnalités et correctifs les plus récents.

- [Network Watcher](../articles/network-watcher/network-watcher-monitoring-overview.md) vous permet d’analyser votre machine virtuelle et ses ressources associées qui sont liées au réseau dans lequel ils se trouvent. Vous pouvez installer l’extension Network Watcher Agent sur une machine virtuelle [Linux](../articles/virtual-machines/linux/extensions-nwa.md) ou [Windows](../articles/virtual-machines/windows/extensions-nwa.md).

- [Azure Monitor pour machines virtuelles](../articles/azure-monitor/insights/vminsights-overview.md) supervise vos machines virtuelles Azure à l’échelle en analysant les performances et l’intégrité de vos machines virtuelles Windows et Linux, y compris leurs différents processus et dépendances interconnectées vis-à-vis d’autres ressources et processus externes. 

## <a name="next-steps"></a>Étapes suivantes
- Suivez les étapes de [Monitor a Windows Virtual Machine with Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) (Analyser une machine virtuelle Windows avec Azure PowerShell) ou [Monitor a Linux Virtual Machine with the Azure CLI](../articles/virtual-machines/linux/tutorial-monitoring.md) (Analyser une machine virtuelle Linux avec Azure CLI).
- En savoir plus sur les meilleures pratiques concernant l’[analyse et le diagnostic](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
