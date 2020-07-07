---
title: Exemple de graphique de métrique Azure Monitor
description: En savoir plus sur la visualisation de vos données Azure Monitor.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 00935762a5e19ec47074021aff59992fd3b801bf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "83797444"
---
# <a name="metric-chart-examples"></a>Exemples de graphiques de métrique 

La plateforme Azure propose [plus d’un millier de métriques](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported), qui ont des dimensions. À l’aide de [filtres de dimension](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), de l’application de [division](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), du contrôle du type de graphique et de l’ajustement des paramètres de graphique, vous pouvez créer des tableaux de bord et des affichages de diagnostic parlants qui fournissent des insights sur l’intégrité de votre infrastructure et de vos applications. Cet article présente quelques exemples de graphiques que vous pouvez générer à l’aide de [Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), et explique les étapes nécessaires pour configurer chacun de ces graphiques.

Vous souhaitez partager vos exemples de magnifiques graphiques avec le monde ? Apportez votre contribution à cette page sur GitHub et partagez vos exemples de graphiques ici !

## <a name="website-cpu-utilization-by-server-instances"></a>Utilisation du processeur du site web par instances de serveur

Ce graphique montre si l’utilisation du processeur d’un service App Service se trouvait dans une plage raisonnable et la répartit par instance pour déterminer si la charge a été correctement distribuée. D’après le graphique, vous pouvez constater que l’application s’exécutait sur une instance de serveur unique avant 6 h 00, puis est montée en puissance avec l’ajout d’une autre instance.

![Graphique en courbes du pourcentage de processeur moyen par instance de serveur](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Comment configurer ce graphique ?

Sélectionnez votre ressource App Service et recherchez la métrique **Pourcentage UC**. Cliquez ensuite sur **Appliquer la division** et sélectionnez la dimension **Instance**.

## <a name="application-availability-by-region"></a>Disponibilité de l’application par région

Affichez la disponibilité de votre application par région pour identifier les emplacements géographiques qui rencontrent des problèmes. Ce graphique montre la métrique de disponibilité Application Insights. Vous pouvez voir que l’application analysée ne rencontre aucun problème de disponibilité au niveau du centre de données USA Est, mais il existe un problème de disponibilité partielle au niveau des régions USA Ouest et Asie Est.

![Graphique de disponibilité moyenne par emplacements](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Comment configurer ce graphique ?

Vous devez tout d’abord activer la surveillance [Disponibilité Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability) pour votre site web. Après cela, sélectionnez votre ressource Application Insights, puis la métrique Disponibilité. Appliquez la division sur la dimension **Emplacement d’exécution**.

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Volume de transactions de compte de stockage par nom d’API

Votre ressource de compte de stockage est confrontée à un volume trop élevé de transactions. Vous pouvez utiliser la métrique de transactions pour identifier les API responsables de cet excès de charge. Notez que le graphique suivant est configuré avec la même dimension (nom de l’API) pour le filtrage et la division pour restreindre l’affichage aux appels d’API qui nous intéressent uniquement :

![Graphique en barres des transactions d’API](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Comment configurer ce graphique ?

Dans le sélecteur de métriques, sélectionnez votre compte de stockage et la métrique **Transactions**. Changez le type de graphique par **Graphique à barres**. Cliquez sur **Appliquer la division** et sélectionner une dimension **Nom de l’API**. Cliquez ensuite sur **Ajouter un filtre** et choisissez une fois de plus la dimension **Nom de l’API**. Dans la boîte de dialogue de filtres, sélectionnez les API que vous souhaitez tracer sur le graphique.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur Azure Monitor [Workbooks](../../azure-monitor/platform/workbooks-overview.md)
* En savoir plus sur [Metrics Explorer](metrics-charts.md)
