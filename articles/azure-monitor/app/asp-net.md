---
title: Configurer des analyses d’application web pour ASP.NET avec Azure Application Insights | Microsoft Docs
description: Configurez les outils d’analytique des performances, de la disponibilité et du comportement des utilisateurs de votre site web ASP.NET, hébergé en local ou dans Azure.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: d3181c3d43f07c7cb920b9fe265a8420c1417a56
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82145278"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Configurer Application Insights pour votre site web ASP.NET

Cette procédure configure votre application web ASP.NET pour l’envoi de données de télémétrie au service [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Elle fonctionne pour les applications ASP.NET hébergées sur votre propre serveur IIS local ou dans le cloud. Vous obtenez des graphiques et un langage de requête puissant, qui vous aident à comprendre les performances de votre application et son utilisation, ainsi que des alertes automatiques sur les défaillances ou les problèmes de performances. De nombreux développeurs trouvent ces fonctionnalités exceptionnelles telles quelles, mais vous pouvez également étendre et personnaliser les données de télémétrie si vous en avez besoin.

L’installation se fait en seulement quelques clics dans Visual Studio. Vous avez la possibilité d’éviter des frais en limitant le volume de données de télémétrie. Cette fonctionnalité vous permet de tester et de déboguer, ou de surveiller un site avec peu d’utilisateurs. Si vous décidez plus tard de vous lancer et de surveiller votre site de production, il sera facile d’augmenter la limite.

## <a name="prerequisites"></a>Prérequis
Pour Application Insights à votre site web ASP.NET, vous devez :

- Installez [Visual Studio 2019 pour Windows](https://www.visualstudio.com/downloads/) avec les charges de travail suivantes :
    - Développement ASP.NET et web (ne décochez pas les composants optionnels)
    - Développement Azure

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="step-1-add-the-application-insights-sdk"></a><a name="ide"></a> Étape 1 : Ajouter le Kit SDK Application Insights

> [!IMPORTANT]
> Les captures d’écran de cet exemple sont basées sur Visual Studio 2017 version 15.9.9 ou ultérieure. L’expérience d’ajout d’Application Insights varie selon les versions de Visual Studio et le type de modèle ASP.NET. Les versions plus anciennes peuvent comporter du texte de remplacement tel que « Configurer Application Insights ».

Faites un clic droit sur le nom de votre application web dans l’Explorateur de solutions, puis choisissez **Ajouter** > **Application Insights Telemetry**

![Capture d’écran de l’Explorateur de solutions, avec l’option Configurer Application Insights en surbrillance](./media/asp-net/add-telemetry-new.png)

(Selon la version de votre SDK d’Application Insights, vous pouvez être invité à passer à la dernière version du Kit de développement logiciel (SDK). Si vous y êtes invité, sélectionnez **Mettre à jour le SDK**.)

![Capture d’écran : Une nouvelle version du SDK Microsoft Application Insights est disponible. Mettre à jour le SDK en surbrillance](./media/asp-net/0002-update-sdk.png)

Écran de configuration d’Application Insights :

Sélectionnez **Prise en main**.

![Capture d’écran de la page Inscrire votre application auprès d’Application Insights](./media/asp-net/00004-start-free.png)

Si vous souhaitez définir le groupe de ressources ou l’emplacement où vos données sont stockées, cliquez sur **Configurer les paramètres**. Les groupes de ressources sont utilisés pour contrôler l’accès aux données. Par exemple, si vous possédez plusieurs applications qui font partie du même système, vous pourrez placer leurs données Application Insights dans le même groupe de ressources.

 Sélectionnez **Inscription**.

![Capture d’écran de la page Inscrire votre application auprès d’Application Insights](./media/asp-net/00005-register-ed.png)

 Sélectionnez **Projet** > **Gérer les packages NuGet** > **Source du package : nuget.org** > Confirmez que vous disposez de la dernière version stable du Kit de développement logiciel (SDK) Application Insights.

 Les données de télémétrie seront envoyées au [portail Azure](https://portal.azure.com), pendant le débogage et une fois que vous aurez publié votre application.
> [!NOTE]
> Si vous ne souhaitez pas envoyer de données de télémétrie au portail pendant le débogage, ajoutez simplement le kit de développement logiciel (SDK) Application Insights à votre application, mais ne configurez pas de ressource dans le portail. Vous êtes en mesure de voir les données de télémétrie dans Visual Studio pendant le débogage. Plus tard, vous pouvez revenir à cette page de configuration, ou vous pouvez attendre d’avoir déployé votre application et [activer les données de télémétrie au moment de l’exécution](../../azure-monitor/app/monitor-performance-live-website-now.md).

## <a name="step-2-run-your-app"></a><a name="run"></a> Étape 2 : Exécutez l'application.
Exécutez votre application en appuyant sur F5. Ouvrez différentes pages pour générer des données de télémétrie.

Un décompte des événements consignés s’affiche dans Visual Studio.

![Capture d’écran de Visual Studio. Le bouton Application Insights apparaît pendant le débogage.](./media/asp-net/00006-Events.png)

## <a name="step-3-see-your-telemetry"></a>Étape 3 : afficher vos données de télémétrie
Vous pouvez voir vos données de télémétrie dans Visual Studio ou sur le portail web Application Insights. Recherchez les données de télémétrie dans Visual Studio pour vous aider à déboguer votre application. Surveillez les performances et l’utilisation dans le portail web lorsque votre système est en ligne. 

### <a name="see-your-telemetry-in-visual-studio"></a>Afficher vos données de télémétrie dans Visual Studio

Dans Visual Studio, pour afficher les données d’Application Insights.  Sélectionnez **Explorateur de solutions** > **Services connectés** > faites un clic droit sur **Application Insights**, puis cliquez sur **Rechercher parmi la télémétrie active**.

Dans la fenêtre de recherche de Visual Studio Application Insights, vous verrez les données de votre application pour consulter les données de télémétrie générées côté serveur de votre application. Faites des essais avec les filtres, puis cliquez sur n’importe quel événement pour afficher plus de détails.

![Capture d’écran des données de la session de débogage dans la fenêtre Application Insights.](./media/asp-net/55.png)

> [!Tip]
> Si aucune donnée n’apparaît, assurez-vous que l’intervalle de temps est correct, puis cliquez sur l’icône de recherche.

[En savoir plus sur les outils Application Insights dans Visual Studio](../../azure-monitor/app/visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Afficher les données de télémétrie dans le portail web

Vous pouvez également afficher les données de télémétrie dans le portail web Application Insights, à moins que vous n’ayez choisi d’installer le Kit de développement logiciel (SDK) uniquement. Le portail offre plus de graphiques, d’outils d’analyse et de vues de plusieurs composants que Visual Studio. Le portail fournit également des alertes.

Ouvrez votre ressource Application Insights. Connectez-vous dans le [portail Azure](https://portal.azure.com/) et trouvez-les ici, ou bien sélectionnez **Explorateur de solutions** > **Services connectés** > faites un clic droit sur **Application Insights** > **Ouvrir le portail Application Insights** et attendez pour y accéder.

Lorsque le portail s’ouvre, il affiche les données de télémétrie de votre application.

![Capture d’écran de la page de présentation Application Insights](./media/asp-net/007.png)

Dans le portail, cliquez sur n’importe quelle mosaïque ou n’importe quel graphique pour afficher plus de détails.

## <a name="step-4-publish-your-app"></a>Étape 4 : Publier votre application
Publiez votre application sur votre serveur IIS ou sur Azure. Vérifiez [Live Metrics Stream (Flux continu de mesures)](../../azure-monitor/app/live-stream.md) pour vous assurer que tout fonctionne correctement.

Vos données de télémétrie s’affichent dans le portail Application Insights, où vous pouvez surveiller les mesures et effectuer une recherche dans vos données de télémétrie. Vous pouvez également utiliser la puissante [langue de requête Kusto](/azure/kusto/query/) pour analyser l’utilisation et les performances ou rechercher des événements spécifiques.

Vous pouvez également continuer à analyser vos données de télémétrie dans [Visual Studio](../../azure-monitor/app/visual-studio.md) à l’aide d’outils comme la recherche de diagnostic et les [tendances](../../azure-monitor/app/visual-studio-trends.md).

> [!NOTE]
> Si votre application envoie tellement de données de télémétrie qu’elle approche de la [limite](../../azure-monitor/app/pricing.md#limits-summary), l’[échantillonnage](../../azure-monitor/app/sampling.md) automatique s’active. L’échantillonnage réduit la quantité de données de télémétrie envoyées depuis votre application, tout en conservant les données liées au diagnostic.
>
>

## <a name="youre-all-set"></a><a name="land"></a> Vous êtes prêt.

Félicitations ! Vous avez installé le package Application Insights dans votre application et l’avez configuré pour envoyer des données de télémétrie au service Application Insights dans Azure.

La ressource Azure qui reçoit les données de télémétrie de votre application est identifiée par une *clé d’instrumentation*. Vous trouverez cette clé dans le fichier ApplicationInsights.config.


## <a name="upgrade-to-future-sdk-versions"></a>Mettre à niveau vers les versions ultérieures du Kit de développement logiciel (SDK)
Pour passer à la [nouvelle version du Kit de développement logiciel (SDK)](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), ouvrez le **gestionnaire de package NuGet** et filtrez les packages qui ont été installés. Sélectionnez **Microsoft.ApplicationInsights.Web** et choisissez **Mettre à niveau**.

Si vous avez apporté des personnalisations à ApplicationInsights.config, conservez-en une copie avant d’effectuer la mise à niveau. Fusionnez ensuite vos modifications dans la nouvelle version.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez consulter d’autres rubriques selon les aspects qui vous intéressent :

* [Instrumentation d’une application web au moment de l’exécution](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md)

### <a name="more-telemetry"></a>Données de télémétrie supplémentaires

* **[Données de navigateur et de chargement de page](../../azure-monitor/app/javascript.md)** : insérez un extrait de code dans vos pages web.
* **[Obtenir des dépendances et une analyse des exceptions plus détaillées](../../azure-monitor/app/monitor-performance-live-website-now.md)** : installez Status Monitor sur votre serveur.
* **[Encodez des événements personnalisés](../../azure-monitor/app/api-custom-events-metrics.md)** pour calculer le nombre, le temps ou mesurer les actions de l’utilisateur.
* **[Obtenir des données de journalisation](../../azure-monitor/app/asp-net-trace-logs.md)** : corrélez les données de journalisation avec vos données de télémétrie.

### <a name="analysis"></a>Analyse

* **[Utilisation d’Application Insights dans Visual Studio](../../azure-monitor/app/visual-studio.md)**<br/>Inclut des informations sur le débogage avec la télémétrie, la recherche de diagnostic et l’accès au code.
* **[Analytics](../../azure-monitor/log-query/get-started-portal.md)** : un puissant langage de requête.

### <a name="alerts"></a>Alertes

* [Tests de disponibilité](../../azure-monitor/app/monitor-web-app-availability.md) : créez des tests pour vous assurer que votre site est visible sur le web.
* [Diagnostics intelligents](../../azure-monitor/app/proactive-diagnostics.md) : ces tests s’exécutent automatiquement, sans que vous n’ayez rien à faire pour les configurer. Ils vous indiquent si votre application affiche un taux inhabituel de demandes ayant échoué.
* [Alertes de métriques](../../azure-monitor/app/alerts.md) : Définissez des alertes qui vous avertissent si un indicateur de performance dépasse un seuil. Vous pouvez définir des mesures personnalisées que vous codez dans votre application.

### <a name="automation"></a>Automatisation

* [Automatisation de la création d’une ressource Application Insights](../../azure-monitor/app/powershell.md)
