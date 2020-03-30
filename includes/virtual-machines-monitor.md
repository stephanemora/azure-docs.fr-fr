---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: fbc6889507e58c4721597a1108337fcb1f8756a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75752266"
---
Compte tenu de la croissance significative des machines virtuelles hébergées dans Azure, il est important d’identifier les problèmes de performances et d’intégrité qui impactent les applications et les services d’infrastructure qu’elles prennent en charge. La supervision de base est assurée par défaut par Azure au moyen de métriques collectées par l’hyperviseur hôte : utilisation du processeur, utilisation du disque, utilisation de la mémoire et trafic réseau. Vous pouvez collecter d’autres métriques et données de journal à l’aide d’[extensions](../articles/virtual-machines/windows/extensions-features.md), ce qui vous permet de configurer des diagnostics sur vos machines virtuelles à partir du système d’exploitation invité.

Pour détecter et contribuer à diagnostiquer les problèmes de performances et d’intégrité liés au système d’exploitation invité ainsi qu’aux composants d’application web .NET ou Java s’exécutant à l’intérieur de la machine virtuelle, Azure Monitor propose une supervision centralisée avec des fonctionnalités complètes comme Azure Monitor pour machines virtuelles et Application Insights.

## <a name="diagnostics-and-metrics"></a>Mesures et diagnostics 

Vous pouvez définir et analyser la collecte de [données de diagnostic](https://docs.microsoft.com/cli/azure/vm/diagnostics) à l’aide de [mesures](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) dans le portail Azure, d’Azure CLI, d’Azure PowerShell et des API de programmation. Vous pouvez par exemple :

- **Observez les mesures de base de la machine virtuelle.** Sur l’écran vue d’ensemble du portail Azure, les mesures de base affichées incluent l’utilisation du processeur, l’utilisation du réseau, le total des octets sur le disque et les opérations de disque par seconde.

- **Activer la collecte des diagnostics de démarrage et les afficher à l’aide du portail Azure.** Lorsque vous importez votre propre image dans Azure, ou même lorsque vous démarrez une des images de plateforme, il peut y avoir de nombreuses raisons pour lesquelles une Machine Virtuelle passe en état non démarrable. Vous pouvez facilement activer les diagnostics de démarrage lorsque vous créez une machine virtuelle en cliquant sur **Activé** pour les Diagnostics de démarrage sous la section Analyse de l’écran Paramètres.

    Au démarrage des machines virtuelles, l’agent Diagnostics de démarrage capture la sortie du démarrage et la stocke dans le stockage Azure. Ces données peuvent être utilisées pour résoudre les problèmes de démarrage des machines virtuelles. Les diagnostics de démarrage ne sont pas activés automatiquement quand vous créez une machine virtuelle à l’aide d’outils en ligne de commande. Avant d’activer les diagnostics de démarrage, vous devez créer un compte de stockage pour stocker les journaux d’activité de démarrage. Si vous activez les diagnostics de démarrage dans le portail Azure, un compte de stockage est créé automatiquement pour vous.

    Si vous n’avez pas activé les diagnostics de démarrage lors de la création de la machine virtuelle, vous pouvez toujours l’activer ultérieurement à l’aide d[Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic), ou d’un [modèle Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Activer la collecte des données de diagnostics du système d’exploitation invité.** Lorsque vous créez une machine virtuelle, vous avez la possibilité d’activer les diagnostics du système d’exploitation invité sur l’écran des paramètres. Si vous activez la collecte des données de diagnostic, l’[extension IaaSDiagnostics pour Linux](../articles/virtual-machines/linux/diagnostic-extension.md) ou l’[extension IaaSDiagnostics pour Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) est ajoutée à la machine virtuelle, ce qui vous permet de collecter des données de mémoire, de processeur et de disque supplémentaires.

    Les données de diagnostic collectées permettent de configurer la mise à l’échelle automatique de vos machines virtuelles. Vous pouvez également configurer des [journaux Azure Monitor](../articles/azure-monitor/platform/data-platform-logs.md) pour stocker les données et configurer des alertes en cas de performances inappropriées.

## <a name="alerts"></a>Alertes

Vous pouvez créer des [alertes](../articles/azure-monitor/platform/alerts-overview.md) en fonction de mesures de performances spécifiques. Les alertes peuvent être utilisées par exemple pour notifier que l’utilisation moyenne de l’UC dépasse un certain seuil ou que l’espace disque disponible est inférieur à une certaine quantité. La configuration d’alertes s’effectue dans le [Portail Azure](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal) à l’aide de [modèles Azure Resource Manager](../articles/azure-monitor/platform/alerts-metric-create-templates.md) ou d’[Azure CLI](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

[Azure Service Health](../articles/service-health/service-health-overview.md) vous offre des conseils et un support personnalisés lorsque vous rencontrez des problèmes avec les services Azure. Il vous aide également à vous préparer aux maintenances planifiées à venir. Azure Service Health vous alerte vous et votre équipe à l’aide de notifications ciblées et flexibles.

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure Resource Health](../articles/service-health/resource-health-overview.md) vous aide à diagnostiquer les problèmes et à accéder au support quand un problème Azure a une incidence sur vos ressources. Il vous informe de l’intégrité (actuelle et passée) de vos ressources et vous aide à atténuer les problèmes. Resource Health propose un support technique dès lors que vous êtes confronté à des problèmes de service Azure et que vous avez besoin d’aide.

## <a name="azure-activity-log"></a>Journaux d’activité

Le [Journal d’activité Azure](../articles/azure-monitor/platform/platform-logs-overview.md) est un journal d’abonnement qui fournit un aperçu de tous les événements relatifs aux abonnements qui se sont produits dans Azure. Cela inclut une plage de données, à partir de données opérationnelles d’Azure Resource Manager pour les mises à jour des événements de l’état d’intégrité du service. Vous pouvez cliquer sur le journal d’activité dans le portail Azure pour afficher le journal de votre machine virtuelle.

Voici ce que vous pouvez faire avec le journal d’activité :

- Créer une [alerte basée sur un événement du journal d’activité](../articles/azure-monitor/platform/platform-logs-overview.md).
- [Envoyez-le à un hub d’événements](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) pour ingestion par un service tiers ou une solution d’analytique personnalisée comme Power BI.
- Analysez-le dans Power BI à l’aide du [Pack de contenu Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Enregistrez-le dans un compte de stockage](../articles/azure-monitor/platform/archive-activity-log.md) pour l’archivage ou l’inspection manuelle. Vous pouvez spécifier la durée de rétention (en jours) à l’aide du Profil de journal.

Vous pouvez également accéder aux données de journal d’activité à l’aide d’[Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), d’[Azure CLI](https://docs.microsoft.com/cli/azure/monitor), ou des [API REST Monitor](https://docs.microsoft.com/rest/api/monitor/).

Les [journaux de ressources Azure](../articles/azure-monitor/platform/platform-logs-overview.md) sont des journaux émis par votre machine virtuelle et qui fournissent des données enrichies et fréquentes sur son fonctionnement. Les journaux de ressources diffèrent des journaux d'activité dans la mesure où ils fournissent un éclairage sur les opérations effectuées au sein de la machine virtuelle.

Voici ce que vous pouvez faire avec les journaux de diagnostic :

- [Enregistrez-les dans un compte de stockage](../articles/azure-monitor/platform/archive-diagnostic-logs.md) pour l’audit ou l’inspection manuelle. Vous pouvez spécifier la durée de rétention (en jours) à l’aide des paramètres de diagnostic des ressources.
- [Envoyez-les à Event Hubs](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) pour ingestion par un service tiers ou une solution d’analytique personnalisée comme Power BI.
- Analysez-les avec [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Surveillance avancée

Pour obtenir une visibilité de l’application ou du service pris en charge par la machine virtuelle Azure et les groupes de machines virtuelles identiques, identifier les problèmes liés au système d’exploitation invité ou à la charge de travail en cours d’exécution dans la machine virtuelle pour comprendre l’impact sur la disponibilité ou les performances de l’application, ou encore déterminer l’existence d’un problème avec l’application, activez à la fois [Azure Monitor pour machines virtuelles](../articles/azure-monitor/insights/vminsights-overview.md) et [Application Insights](../articles/azure-monitor/app/app-insights-overview.md).

Azure Monitor pour machines virtuelles supervise vos machines virtuelles Azure à l’échelle en analysant les performances et l’intégrité de vos machines virtuelles Windows et Linux, y compris les différents processus et dépendances interconnectées vis-à-vis d’autres ressources et processus externes qu’il découvre. Il inclut plusieurs graphiques de performances de tendance pour faciliter l’examen des problèmes et évaluer la capacité de vos machines virtuelles. La carte des dépendances montre les machines supervisées et celles qui ne le sont pas, ainsi que les connexions réseau en échec et actives entre les processus et ces machines. Elle présente également des graphiques de tendance avec des métriques standard sur les connexions réseau. En combinant Azure Monitor pour machines virtuelles et Application Insights, vous pouvez superviser votre application et capturer des données de télémétrie (requêtes HTTP, exceptions, etc.) pour mettre en corrélation les problèmes entre les machines virtuelles et votre application. Configurez des [alertes Azure Monitor](../articles/azure-monitor/platform/alerts-overview.md) pour vous avertir si des conditions importantes sont détectées dans les données de supervision collectées par Azure Monitor pour machines virtuelles.

## <a name="next-steps"></a>Étapes suivantes

- Suivez les étapes de [Monitor a Windows Virtual Machine with Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) (Analyser une machine virtuelle Windows avec Azure PowerShell) ou [Monitor a Linux Virtual Machine with the Azure CLI](../articles/virtual-machines/linux/tutorial-monitoring.md) (Analyser une machine virtuelle Linux avec Azure CLI).

- En savoir plus sur les meilleures pratiques concernant l’[analyse et le diagnostic](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
