---
title: Surveiller votre application web ASP.NET avec Azure Application Insights | Microsoft Docs
description: Fournit des instructions pour configurer rapidement une application web ASP.NET en vue de la supervision avec Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: 3775070320b8c6e96dd06031bd87ea53f37046c5
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78894849"
---
# <a name="start-monitoring-your-aspnet-web-application"></a>Démarrer la surveillance de votre application web ASP.NET

Azure Application Insights vous permet d’analyser facilement la disponibilité, les performances et l’utilisation de votre application web.  De plus, vous pouvez rapidement identifier et diagnostiquer les erreurs dans votre application sans attendre qu’un utilisateur ne les signale.  Avec les informations que vous collectez à partir d’Application Insights sur les performances et l’efficacité de votre application, vous pouvez prendre des décisions avisées pour gérer et améliorer votre application.

Ce guide de démarrage rapide montre comment ajouter Application Insights à une application web ASP.NET existante et commencer à analyser les statistiques actives, ce qui représente simplement l’une des méthodes que vous pouvez utiliser pour analyser votre application. Si vous n’avez pas d’application web ASP.NET, vous pouvez en créer une en suivant le [Guide de démarrage rapide Créer une application web ASP.NET](../../app-service/app-service-web-get-started-dotnet-framework.md).

## <a name="prerequisites"></a>Prérequis
Pour suivre ce guide de démarrage rapide :

- Installez [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec les charges de travail suivantes :
    - Développement web et ASP.NET
    - Développement Azure


Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="enable-application-insights"></a>Activer Application Insights

1. Ouvrez votre projet dans Visual Studio 2019.
2. Sélectionnez **Configurer Application Insights** dans le menu Projet. Visual Studio ajoute le SDK Application Insights à votre application.

    > [!IMPORTANT]
    > Le processus d’ajout d’Application Insights varie selon le type de modèle ASP.NET. Si vous utilisez le modèle **Vide** ou **Application mobile Azure**, sélectionnez **Projet** > **Ajouter Application Insights Telemetry**. Pour tous les autres modèles ASP.NET, consultez les instructions de l’étape précédente. 

3. Cliquez sur **Prise en main** (les versions antérieures de Visual Studio ont un bouton **Démarrer gratuitement** à la place).

    ![Ajout d’Application Insights à Visual Studio](./media/quick-monitor-portal/add-application-insights-b.png)

4. Sélectionnez votre abonnement et cliquez sur **S’inscrire**.

5. Sélectionnez **Projet** > **Gérer les packages NuGet** > **Source du package : nuget.org** > **Mettre à jour** les packages du SDK Application Insights vers la version stable la plus récente.

6. Exécutez votre application en sélectionnant **Démarrer le débogage** dans le menu **Déboguer** ou en appuyant sur la touche F5.

## <a name="confirm-app-configuration"></a>Confirmer la configuration de l’application

Application Insights recueille les données de télémétrie pour votre application, quel que soit l’emplacement de son exécution. Suivez les étapes ci-dessous pour lancer l’affichage de ces données.

1. Ouvrez Application Insights en cliquant sur **Afficher** -> **Autres fenêtres** -> **Recherche Application Insights**.  Vous voyez les données de télémétrie de votre session active.<BR><br>![Données de télémétrie dans Visual Studio](./media/quick-monitor-portal/telemetry-in-vs.png)

2. Cliquez sur la première demande dans la liste (GET Home/Index dans cet exemple) pour afficher les détails de la demande. Notez que le code d’état et le temps de réponse sont tous deux inclus, ainsi que d’autres informations utiles sur la demande.<br><br>![Détails de la réponse dans Visual Studio](media/quick-monitor-portal/request-details.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Démarrer l’analyse dans le portail Azure

Vous pouvez maintenant ouvrir Application Insights dans le portail Azure pour afficher différents détails sur votre application en cours d’exécution.

1. Développez le dossier **Services connectés** (icône d’une prise branchée au cloud) dans l’Explorateur de solutions, cliquez avec le bouton droit sur le dossier **Application Insights**, puis cliquez sur **Ouvrir le portail Application Insights**.  Vous voyez des informations sur votre application et diverses options.

    ![Mise en correspondance d'applications](media/quick-monitor-portal/04-overview.png)

2. Cliquez sur **cartographie d’application** pour obtenir une présentation visuelle des relations de dépendance entre les composants de votre application.  Chaque composant affiche des indicateurs de performance clés comme la charge, les performances, les échecs et les alertes.

    ![Mise en correspondance d'applications](media/quick-monitor-portal/05-appmap.png)

3. Cliquez sur l’icône **Analyse d’application** ![Mise en correspondance d’applications](media/quick-monitor-portal/app-viewinlogs-icon.png)**Voir dans Logs (Analytics)** sur l’un des composants de l’application. Vous ouvrez ainsi **Logs (Analytics)** , lequel fournit un langage de requête enrichi permettant d’analyser toutes les données collectées par Application Insights. Dans ce cas, une requête est générée et affiche le nombre de demandes sous forme de graphique. Vous pouvez écrire vos propres requêtes pour analyser d’autres données.

    ![Analytics](media/quick-monitor-portal/6viewanalytics.png)

4. Cliquez sur **Flux de métriques temps réel** sur la gauche sous Examiner. Des statistiques actives sur votre application en cours d’exécution s’affichent. Cela inclut des informations telles que le nombre de demandes entrantes, la durée de ces demandes et les erreurs qui se produisent. Vous pouvez également examiner les métriques de performances critiques comme la mémoire et le processeur.

    ![Flux temps réel](media/quick-monitor-portal/7livemetrics.png)

    Si vous êtes prêt à héberger votre application dans Azure, vous pouvez la publier maintenant. Suivez les étapes décrites dans le [guide de démarrage rapide Créer une application web ASP.NET](../../app-service/app-service-web-get-started-dotnet.md#update-the-app-and-redeploy).

5. Si vous utilisez Visual Studio pour ajouter la supervision Application Insights, vous pouvez ajouter la supervision côté client automatiquement. Pour ajouter la supervision côté client à une application manuellement, ajoutez le code JavaScript suivant à votre application :

```html
<!-- 
To collect user behavior analytics about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var appInsights=window.appInsights||function(a){
  function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
  }({
      instrumentationKey:"<your instrumentation key>"
  });

window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
```

Pour en savoir plus, visitez le référentiel GitHub pour notre [Kit de développement logiciel (SDK) JavaScript open source](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="video"></a>Vidéo

* Vidéo détaillée externe sur la [configuration à partir de zéro d’Application Insights avec une application .NET](https://www.youtube.com/watch?v=blnGAVgMAfA).

## <a name="clean-up-resources"></a>Nettoyer les ressources
Une fois les tests terminés, vous pouvez supprimer le groupe de ressources et toutes les ressources associées. Pour ce faire, procédez comme suit.
1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur **myResourceGroup**.
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez **myResourceGroup** dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez activé votre application pour une supervision par Azure Application Insights.  Continuez à passer en revue les didacticiels pour apprendre à l’utiliser pour surveiller les statistiques et détecter les problèmes dans votre application.

> [!div class="nextstepaction"]
> [Didacticiels Azure Application Insights](tutorial-runtime-exceptions.md)
