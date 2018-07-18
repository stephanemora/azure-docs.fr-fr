---
title: Vue d’ensemble des mesures dans Microsoft Azure
description: Vue d’ensemble des mesures et de leur utilisation dans Microsoft Azure
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/05/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 3501c8d35968ecf8e32c806dfb05ccfebc7f4386
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264217"
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Vue d’ensemble des mesures dans Microsoft Azure
Cet article décrit ce que sont les mesures dans Microsoft Azure ainsi que leurs avantages, et comment commencer à les utiliser.  

## <a name="what-are-metrics"></a>Quelles sont les mesures ?
Azure Monitor vous permet d’utiliser la télémétrie pour surveiller les performances et l’intégrité de vos charges de travail sur Azure. Les mesures (aussi appelées compteurs de performances) émises par la plupart des ressources Azure sont le type de données de télémétrie Azure plus important. Azure Monitor propose plusieurs façons de configurer et d’utiliser ces mesures pour l’analyse et le dépannage.

## <a name="what-are-the-characteristics-of-metrics"></a>Quelles sont les caractéristiques des mesures ?
Les mesures présentent les caractéristiques suivantes :

* Toutes les métriques ont **fréquence d’une minute** (sauf indication contraire dans leur définition). Vous recevez une valeur de mesure chaque minute à partir de votre ressource, pour une visibilité en quasi temps réel de l’intégrité de votre ressource.
* Les mesures sont **disponibles immédiatement**. Vous n’avez pas besoin de vous abonner à ou de configurer des diagnostics supplémentaires.
* Vous pouvez accéder à **93 jours d’historique** pour chaque métrique. Vous pouvez consulter rapidement les tendances récentes et mensuelles de performances ou d’intégrité de vos ressources.
* Certaines métriques peuvent avoir des paires d’attributs nom-valeur appelées **dimensions**. Elles vous permettent de segmenter encore davantage et d’explorer une métrique d’une manière plus explicite.

## <a name="what-can-you-do-with-metrics"></a>Que pouvez-vous faire avec les mesures ?
Les métriques vous permettent d’effectuer les tâches suivantes :


- Configurer une **règle d’alerte qui envoie une notification ou prend une action de façon automatique** pour une mesure lorsque celle-ci dépasse le seuil que vous avez défini. Les actions sont contrôlées via des [groupes d’actions](monitoring-action-groups.md). Exemple actions : notifications par e-mail, téléphone et SMS, appeler un webhook, démarrer un runbook et bien plus encore. La **mise à l’échelle automatique** est une action automatisée spéciale qui vous permet de mettre à l’échelle une ressource pour gérer la charge tout en préservant des coûts faibles en cas d’absence de charge. Vous pouvez configurer une règle de paramètre de mise à l’échelle automatique sur la base d’une mesure qui dépasse un seuil.
- **Acheminez** toutes les métriques vers *Application Insights* ou *Log Analytics* pour activer instantanément analyses, recherche et alertes personnalisées sur les données des métriques depuis vos ressources. Vous pouvez également diffuser des mesures vers un *Event Hub*, pour les acheminer vers Azure Stream Analytics ou des applications personnalisées pour une analyse en quasi temps réel. Vous pouvez paramétrer la diffusion vers un hub d’événements à l’aide des paramètres de diagnostic.
- **Archivez** l’historique des performances ou d’intégrité de votre ressource à des fins de conformité, d’audit ou de création de rapports hors connexion.  Vous pouvez acheminer vos mesures de stockage Azure Blob lorsque vous configurez les paramètres de diagnostic pour votre ressource.
- Utilisez le **portail Azure** pour découvrir, accéder à et afficher toutes les métriques lorsque vous sélectionnez une ressource et tracez les métriques sur un graphique. Vous pouvez suivre les performances de votre ressource (par exemple une machine virtuelle, un site web ou une application logique) en épinglant ce graphique sur votre tableau de bord.  
- **Effectuer des analyses avancées** ou créer des rapports sur les tendances de performances ou d’utilisation de vos ressources.
- **Interroger** les mesures en utilisant les applets de commande PowerShell ou l’API REST multiplateforme.
- **Utiliser** les mesures via les nouvelles API REST Azure Monitor.

  ![Acheminement des mesures dans Azure Monitor](./media/monitoring-overview-metrics/Metrics_Overview_v4.png)

## <a name="access-metrics-via-the-portal"></a>Accès aux mesures via le portail
Voici un bref aperçu de la création d’un graphique de mesures à l’aide du portail Azure.

### <a name="to-view-metrics-after-creating-a-resource"></a>Pour afficher des mesures après la création d’une ressource
1. Ouvrez le portail Azure.
2. Créez un site Azure App Service.
3. Après avoir créé un site web, accédez au panneau **Vue d’ensemble** du site web.
4. Vous pouvez afficher les nouvelles mesures sous forme de vignette **Surveillance**. Vous pouvez modifier la vignette et sélectionner d’autres mesures.

   ![Mesures sur une ressource dans Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview1.png)

### <a name="to-access-all-metrics-in-a-single-place"></a>Pour accéder à toutes les mesures en un seul emplacement
1. Ouvrez le portail Azure.
2. Accédez au nouvel onglet **Monitor**, puis sélectionnez l’option **Mesures** se trouvant dessous.
3. Sélectionnez votre abonnement, le groupe de ressources et le nom de la ressource dans la liste déroulante.
4. Affichez la liste des mesures disponibles. Sélectionnez la mesure qui vous intéresse et tracez-la.
5. Vous pouvez l’épingler au tableau de bord en cliquant sur l’épingle dans le coin supérieur droit.

   ![Accéder à toutes les mesures dans Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview2.png)

> [!NOTE]
> Vous pouvez accéder aux mesures à partir de machines virtuelles (basées sur Azure Resource Manager) et aux groupes de machines virtuelles identiques sans aucune installation de diagnostic supplémentaire. Ces nouvelles mesures au niveau hôte sont disponibles pour les instances Windows et Linux. Ces mesures ne doivent pas être confondues avec les mesures de niveau système d’exploitation invité auxquelles vous avez accès lorsque vous activez les Diagnostics Azure sur vos machines virtuelles ou groupes de machines virtuelles identiques. Pour en savoir plus sur la configuration de Diagnostics, consultez [Qu’est-ce que Microsoft Azure Diagnostics](../azure-diagnostics.md).
>
>

Azure Monitor offre également une nouvelle expérience de graphes de métriques, disponible en préversion. Cette expérience permet aux utilisateurs de superposer sur un même graphique des métriques provenant de plusieurs ressources. Les utilisateurs peuvent également tracer, segmenter et filtrer des métriques multidimensionnelles à l’aide de cette nouvelle expérience de graphes de métriques. Pour en savoir plus, [cliquez ici](https://aka.ms/azuremonitor/new-metrics-charts)

## <a name="access-metrics-via-the-rest-api"></a>Accès aux mesures via l’API REST
Les mesures Azure sont accessibles via les API Azure Monitor. Il existe deux API qui vous aident à découvrir et accéder aux mesures :

* Utilisez [l’API REST de définitions de mesures Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) pour accéder à la liste des mesures et dimensions disponibles pour un service.
* Utilisez [l’API REST Azure Monitor Metrics](https://docs.microsoft.com/rest/api/monitor/metrics) pour segmenter, filtrer et accéder aux données de mesure elles-mêmes.

> [!NOTE]
> Cet article décrit les mesures via la [nouvelle API pour les mesures](https://docs.microsoft.com/rest/api/monitor/) pour les ressources Azure. La version de l’API pour les nouvelles définitions et API de métriques est 2018-01-01. Les définitions de mesures et les mesures sont accessibles avec la version 2014-04-01 de l’API.
>
>

Pour une description plus détaillée à l’aide de l’API REST d’Azure Monitor, consultez [Procédure pas à pas d’utilisation de l’API REST d’Azure Monitor](monitoring-rest-api-walkthrough.md).

## <a name="export-metrics"></a>Exporter mesures
Vous pouvez accéder au panneau **Paramètres de diagnostic** sous l’onglet **Monitor** et afficher les options d’exportation pour les mesures. Vous pouvez sélectionner les métriques (et les journaux de diagnostic) à acheminer vers un stockage d’objets Blob, Azure Hub Events ou vers Log Analytics pour les cas d’utilisation mentionnés précédemment dans cet article.

 ![Options d’exportation pour les mesures dans Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview3.png)

Vous pouvez configurer cela au moyen de modèles Resource Manager, de [PowerShell](insights-powershell-samples.md), de [l’interface de ligne de commande Azure](insights-cli-samples.md) ou des [API REST](https://msdn.microsoft.com/library/dn931943.aspx).

> [!NOTE]
> L’envoi de métriques multidimensionnelles via les paramètres de diagnostic n’est pas pris en charge actuellement. Les métriques à plusieurs dimensions sont exportées en tant que métriques dimensionnelles uniques aplaties, puis agrégées dans les valeurs de la dimension.
>
> *Par exemple* : la métrique« Messages entrants » sur un Event Hub peut être examinée et représentée sur un niveau par file d’attente. Toutefois, lors de l’exportation via les paramètres de diagnostic, la métrique est représentée sous la forme de tous les messages entrants, dans toutes les files d’attente de l’Event Hub.
>
>

## <a name="take-action-on-metrics"></a>Effectuer une opération sur les mesures
Pour recevoir des notifications ou prendre des actions automatisées sur les données de mesure, vous pouvez configurer des règles d’alerte ou des paramètres de mise à l’échelle automatique.

### <a name="configure-alert-rules"></a>Configuration des règles d'alerte
Vous pouvez configurer des règles d’alerte sur les mesures. Ces règles d’alerte peuvent vérifier si une mesure a atteint un certain seuil. Azure Monitor propose deux fonctions d’alerte sur les mesures.

Alertes de métriques : vous pouvez ensuite recevoir une notification par e-mail ou déclencher un webhook qui peut être utilisé pour exécuter un script personnalisé. Vous pouvez également utiliser le webhook pour configurer les intégrations de produits tiers.

 ![Mesures et règles d’alerte dans Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview4.png)

Les nouvelles alertes de métriques peuvent analyser plusieurs métriques et seuils d’une ressource, et vous préviennent via un [groupe d’actions](/monitoring-action-groups.md). En savoir plus sur les [dernières alertes ici](https://aka.ms/azuremonitor/near-real-time-alerts).


### <a name="autoscale-your-azure-resources"></a>Mise à l’échelle automatique de vos ressources Azure
Certaines ressources Azure prennent en l’augmentation ou la diminution de la taille de plusieurs instances pour gérer vos charges de travail. La mise à l’échelle automatique s’applique à App Services (applications web), aux groupes de machines virtuelles identiques et aux services cloud classiques d’Azure. Vous pouvez configurer les règles de mise à l’échelle automatique pour augmenter la taille des instances lorsqu’une certaine mesure affectant votre charge de travail dépasse un seuil spécifié. Pour plus d'informations, consultez la rubrique [Présentation de la mise à l’échelle automatique](monitoring-overview-autoscale.md).

 ![Mesures et règles de mise à l’échelle automatique dans Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="learn-about-supported-services-and-metrics"></a>Découvrez les services et mesures pris en charge
Vous pouvez voir une liste détaillée de tous les services pris en charge et leurs mesures dans [Mesures d’Azure Monitor - Mesures prises en charge par type de ressource](monitoring-supported-metrics.md).

## <a name="next-steps"></a>Étapes suivantes
Consultez les liens dans cet article. De plus, consultez :  

* [Mesures courantes pour la mise à l’échelle automatique](insights-autoscale-common-metrics.md)
* [Guide de création de règles d’alertes](insights-alerts-portal.md)
* [Analyser les journaux du stockage Azure avec Log Analytics](../log-analytics/log-analytics-azure-storage.md)
