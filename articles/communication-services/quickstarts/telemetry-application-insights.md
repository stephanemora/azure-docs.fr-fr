---
title: 'Démarrage rapide : Exportation des données de télémétrie du Kit de développement logiciel (SDK) vers Application Insights'
titleSuffix: An Azure Communication Services quickstart
description: Découvrez comment exporter des données de télémétrie du Kit de développement logiciel (SDK) Azure Communication Services vers Application Insights.
author: peiliu
manager: vravikumar
services: azure-communication-services
ms.author: peiliu
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: cddff62f911339e7b2ddb76535acda09e1793137
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292419"
---
# <a name="quickstart-using-azure-opentelemetry-exporter-to-export-sdk-telemetry-data-to-application-insights"></a>Démarrage rapide : Utilisation de l’exportateur Azure OpenTelemetry pour exporter des données de télémétrie du Kit de développement logiciel (SDK) vers Application Insights

L’exportateur Azure OpenTelemetry est un kit de développement logiciel (SDK) inclus dans [Azure Monitor](../../azure-monitor/index.yml). Il vous permet d’exporter des données de suivi à l’aide de l’exportateur OpenTelemetry et de les envoyer à [Application Insights](../../azure-monitor/app/app-insights-overview.md). L’exportateur OpenTelemetry offre aux applications et aux infrastructures un moyen standardisé de collecter des informations de télémétrie.

Azure Application Insights est une fonctionnalité d’Azure Monitor utilisée pour surveiller des applications dynamiques. Il affiche des données de télémétrie relatives à votre application dans une ressource Microsoft Azure. Le modèle de télémétrie est normalisé afin qu’il soit possible de créer une plateforme et une surveillance indépendante du langage.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/telemetry-app-insights-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/telemetry-app-insights-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/telemetry-app-insights-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/telemetry-app-insights-python.md)]
::: zone-end

La sortie de l’application décrit chaque action terminée :
<!---cSpell:disable --->
```console
Created an identity with ID: <identity-id>
Issued an access token with 'chat' scope that expires at <expiry-data>
```
<!---cSpell:enable --->

## <a name="view-the-telemetry-data-in-application-insights"></a>Afficher les données de télémétrie dans Application Insights
Pour analyser les données de télémétrie à partir du Kit de développement logiciel (SDK), accédez à l’onglet `Performance`, puis à `Dependencies`. Vous pourrez voir le suivi des commandes `Create User Activity` et `Get Token Activity`.

:::image type="content" source="media/application-insights-dependencies.png" alt-text="Capture d’écran montrant des entrées de données de télémétrie dans Application Insights.":::

Pour plus de détails, vous pouvez afficher une vue détaillée des exemples :

:::image type="content" source="media/application-insights-samples-drill-down.png" alt-text="Capture d’écran montrant la vue détaillée des exemples":::

La vue détaillée contient des informations supplémentaires sur l’activité, telles que l’emplacement à partir duquel elle a été appelée, son horodateur, son nom, ses performances, son type, etc. Vous pouvez également y voir le nom de rôle cloud et l’ID d’instance que nous avons définis dans l’exemple d’extrait de code ci-dessus. Notez que les propriétés personnalisées suivies apparaissent également ici :

:::image type="content" source="media/application-insights-e2e-details.png" alt-text="Affichage de bout en bout des détails de la transaction":::

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

> [!div class="checklist"]
> * Configurer l’exportateur de télémétrie
> * Canaliser les données de télémétrie vers Application Insights
> * Afficher les données exportées dans Application Insights

Vous souhaiterez peut-être également :

- [En savoir plus sur l’analyse de données dans Application Insights](/powerapps/maker/canvas-apps/application-insights)