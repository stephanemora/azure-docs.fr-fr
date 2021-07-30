---
title: 'Démarrage rapide : surveiller une application ASP.NET Core avec Azure Monitor Application Insights'
description: Instrumentez une application web ASP.NET Core pour la surveillance avec Azure Monitor Application Insights.
ms.topic: quickstart
author: lgayhardt
ms.author: lagayhar
ms.date: 06/11/2021
ms.custom: devx-track-dotnet
ms.openlocfilehash: 90d4fd76350d03bd876d97a7191011079b11845e
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300909"
---
# <a name="quickstart-monitor-an-aspnet-core-app-with-azure-monitor-application-insights"></a>Démarrage rapide : surveiller une application ASP.NET Core avec Azure Monitor Application Insights

Dans ce démarrage rapide, vous allez instrumenter une application ASP.NET Core à l’aide du kit de développement logiciel (SDK) Application Insights pour collecter les données de télémétrie côté client et côté serveur.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/dotnet).
- SDK .NET 5.0 ou ultérieur. [Installez le dernier SDK .NET 5.0](https://dotnet.microsoft.com/download/dotnet/5.0) pour votre plateforme.

## <a name="create-an-application-insights-resource"></a>Création d’une ressource Application Insights dans Azure

Pour commencer à recevoir des données de télémétrie, créez une ressource Application Insights dans votre abonnement Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sélectionnez **Créer une ressource** > **Outils de développement** > **Application Insights**.

1. Complétez le formulaire qui s’affiche :
    1. Vérifiez l’**Abonnement** sélectionné.
    1. Sélectionnez un **Groupe de ressources** existant ou créez-en un.
    1. Spécifiez un **Nom** pour cette ressource Application Insights.
    1. Sélectionnez une **Région** proche de votre emplacement.
    1. Définissez le **Mode de ressource** sur *Classique*. 

1. Sélectionnez le bouton **Vérifier + Créer**.
1. Cliquez sur le bouton **Créer**.
1. Une fois le déploiement terminé, sélectionnez le bouton **Accéder à la ressource**.
1. À partir de la **Vue d’ensemble** qui s’affiche, copiez votre **Clé d’instrumentation** (disponible sous **Informations de base**).

## <a name="create-and-configure-an-aspnet-core-web-app"></a>Créer et configurer une application web ASP.NET Core

Pour créer et configurer une application web ASP.NET Core, procédez comme suit :

1. Créer une application Razor Pages ASP.NET Core :
    
    ```dotnetcli
    dotnet new razor -o ai.quickstart
    ```
    
    La commande précédente crée une application Razor Pages ASP.NET Core dans un répertoire nommé *ai.quickstart*. 
    
    > [!TIP]
    > Vous préférerez peut-être [utiliser Visual Studio pour créer votre application](/visualstudio/ide/quickstart-aspnet-core).

1. À l’intérieur du répertoire du projet, ajoutez le package `Microsoft.ApplicationInsights.AspNetCore` au projet. Si vous utilisez Visual Studio, vous pouvez utiliser [Gestionnaire de package NuGet](/nuget/consume-packages/install-use-packages-visual-studio).

    ```dotnetcli
    dotnet add package Microsoft.ApplicationInsights.AspNetCore --version 2.17.0 
    ```

1. À l’aide d’un éditeur de texte ou d’un environnement de développement intégré, modifiez *appsettings.json* afin qu’il contienne une valeur pour `ApplicationInsights.InstrumentationKey`, comme indiqué. Utilisez la clé d'instrumentation que vous avez copiée précédemment.

    :::code language="json" source="~/dotnet-samples/azure/app-insights-aspnet-core-quickstart/appsettings.json" range="1-12" highlight="2-4":::
    
    > [!IMPORTANT]
    > Le kit de développement logiciel (SDK) Application Insights attend la valeur de configuration `ApplicationInsights.InstrumentationKey`. Veillez à correctement la nommer.

## <a name="configure-server-side-telemetry"></a>Configurer les données de télémétrie côté serveur

Dans la méthode `ConfigureServices` de *Startup.cs*, ajoutez le service Application Insights au pipeline. Ajoutez la ligne en surbrillance :

:::code language="csharp" source="~/dotnet-samples/azure/app-insights-aspnet-core-quickstart/startup.cs" range="24-28" highlight="4":::

## <a name="configure-client-side-telemetry"></a>Configurer les données de télémétrie côté client

Pour instrumenter l’application afin d’envoyer des données de télémétrie côté client, procédez comme suit :

1. Dans *Pages/_ViewImports.cshtml*, ajoutez la ligne suivante :

    ```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

    La modification précédente inscrit une dépendance `Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet` contenant l’élément de script côté client Application Insights.

1. Dans *Pages/Shared/_Layout.cshtml*, dans l'élément `<head>`, ajoutez la ligne en surbrillance :

    :::code language="cshtml" source="~/dotnet-samples/azure/app-insights-aspnet-core-quickstart/pages/shared/_layout.cshtml" range="3-10" highlight="7":::

   Cette modification utilise l’objet `JavaScriptSnippet` injecté pour veiller à ce que l’élément `<script>` s’affiche dans l’élément `<head>` de chaque page de l’application.

## <a name="validate-telemetry-ingestion"></a>Valider l’ingestion des données de télémétrie

L’ingestion des données de télémétrie dans Application Insights à des fins d’analyse peut prendre plusieurs minutes. Pour vérifier que votre application envoie des données de télémétrie en temps réel, utilisez les **Métriques en temps réel** :

1. Exécutez l’application web à l’aide en utilisant `dotnet run` ou votre environnement de développement intégré.
1. Dans le portail Azure, lors de l’affichage de votre ressource de Application Insights, sélectionnez **Métriques en temps réel** sous **Examiner**.
1. Dans votre application, sélectionnez les liens **Accueil** et **Confidentialité** à plusieurs reprises.
1. Observez l’activité sur l’affichage **Métriques en temps réel** à mesure que des requêtes interviennent dans l’application.

## <a name="next-steps"></a>Étapes suivantes

Félicitations ! Vous pouvez désormais utiliser les données de télémétrie envoyées par votre application pour :

- [Rechercher des exceptions de runtime](tutorial-runtime-exceptions.md).
- [Rechercher des problèmes de performances](tutorial-performance.md).
- [Alerter sur l’intégrité de l’application](tutorial-alert.md).

> [!div class="nextstepaction"]
> [En savoir plus sur Application Insights dans ASP.NET Core](asp-net-core.md)
