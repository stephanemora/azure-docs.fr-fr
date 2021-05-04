---
title: Surveillance d’Azure Static Web Apps en préversion
description: Surveillez les requêtes, les échecs et les informations de suivi dans Azure Static Web Apps en préversion
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 4/23/2021
ms.author: cshoe
ms.openlocfilehash: 474b612d835ab415f9607f737ef219acc2e99152
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108077652"
---
# <a name="monitor-azure-static-web-apps"></a>Surveiller des applications Azure Static Web Apps

Activez [Application Insights](../azure-monitor/app/app-insights-overview.md) pour surveiller les requêtes d’API, les échecs et les informations de suivi.

> [!NOTE]
> L’utilisation d’Application Insights avec Azure Static Web Apps requiert une application avec une [API](./add-api.md).

## <a name="add-monitoring"></a>Ajouter une fonctionnalité de supervision

Utilisez les étapes suivantes pour ajouter la surveillance Application Insights à votre application web statique.

1. Ouvrez l’instance Static Web Apps dans le portail Azure.

1. Sélectionnez **Application Insights** dans le menu.

1. Sélectionnez **Oui** en regard d’_Activer Application Insights_.

1. Sélectionnez **Enregistrer**.

:::image type="content" source="media/monitoring/azure-static-web-apps-application-insights-add.png" alt-text="Ajouter Application Insights à Azure Static Web Apps":::

> [!NOTE]
> Une fois que vous avez créé l’instance Application Insights, un paramètre d’application associé est créé dans l’instance Azure Static Web Apps utilisée pour lier les services.

## <a name="access-data"></a>Accéder aux données

1. Dans la fenêtre _Vue d’ensemble_ de votre application web statique, sélectionnez le lien en regard de _Groupe de ressources_.

1. Dans la liste, sélectionnez l’instance Application Insights précédée du même nom que votre application web statique.

L’exemple suivant met en évidence quelques emplacements dans le portail utilisés pour inspecter les aspects des points de terminaison de l’API de votre application.

> [!NOTE]
> Pour plus d’informations sur l’utilisation d’Application Insights, consultez [Où se trouvent mes données de télémétrie ?](../azure-monitor/app/app-insights-overview.md#where-do-i-see-my-telemetry).

| Type | Emplacement du menu | Description |
|--- | --- | --- |
| [Échecs](../azure-monitor/app/asp-net-exceptions.md) | _Examiner > Échecs_ | Vérifiez les demandes qui ont échoué. |
| [Requêtes serveur](../azure-monitor/app/tutorial-performance.md) | _Examiner > Performances_ | Passez en revue les requêtes d’API individuelles.  |
| [Journaux d’activité](../azure-monitor/app/diagnostic-search.md) | _Supervision > Journaux_ | Interagissez avec un éditeur pour interroger les journaux de transactions. |
| [Métriques](../azure-monitor/essentials/app-insights-metrics.md) | _Supervision > Métriques_ | Interagissez avec un concepteur pour créer des graphiques personnalisés à l’aide de différentes métriques. |

### <a name="traces"></a>Traces

Pour afficher les traces dans votre application, procédez comme suit.

1. Sous _Supervision_, sélectionnez **Journaux d’activité**.

1. Pointez votre souris sur une carte dans la fenêtre _Requêtes_.

1. Sélectionnez le bouton **Charger l’éditeur**.

1. Remplacez la requête générée par le mot `traces`.

1. Sélectionnez le bouton **Run**.

:::image type="content" source="media/monitoring/azure-static-web-apps-application-insights-traces.png" alt-text="Voir les traces Application Insights":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configuration de l’authentification et de l’autorisation](authentication-authorization.md)
