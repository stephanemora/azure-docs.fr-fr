---
title: Surveiller les applications Docker dans Azure Application Insights | Microsoft Docs
description: Vous pouvez visualiser les compteurs de performances, les événements et les exceptions Docker dans Application Insights, avec les données de télémétrie des applications en conteneur.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 272b4e8af7b1ed3d01d8af0979b56954585f795d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90977581"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Analyse des applications Docker dans Application Insights (déconseillé)

> [!NOTE]
> Cette solution a été dépréciée. Pour en savoir plus sur nos investissements actuels dans la supervision des conteneurs, nous vous recommandons de consulter [Azure Monitor pour conteneurs](../insights/container-insights-overview.md).

Les événements de cycle de vie et les compteurs de performances provenant de conteneurs [Docker](https://www.docker.com/) peuvent être représentés dans Application Insights. Installez l'image [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) dans un conteneur de votre hôte pour afficher les compteurs de performances de l'hôte, ainsi que d'autres images.

Avec Docker, vous distribuez vos applications dans des conteneurs légers avec toutes les dépendances. Elles s’exécuteront sur n’importe quelle machine hôte exécutant un moteur Docker.

Lorsque vous exécutez l’[image Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) sur l’hôte Docker, vous bénéficiez des avantages suivants :

* Télémétrie de cycle de vie de tous les conteneurs en cours d'exécution sur l'hôte : démarrage, arrêt, et ainsi de suite.
* Compteurs de performance pour tous les conteneurs. Processeur, mémoire, utilisation du réseau, et bien plus encore.
* Si vous avez [installé le SDK Application Insights pour Java](./java-get-started.md) dans les applications en cours d’exécution dans les conteneurs, toutes les données de télémétrie de ces applications auront des propriétés supplémentaires identifiant l’ordinateur hôte et le conteneur. Par exemple, si vous avez des instances d’une application en cours d’exécution dans plusieurs hôtes, vous pouvez facilement filtrer la télémétrie d’application par hôte.

## <a name="set-up-your-application-insights-resource"></a>Configuration de votre ressource Application Insights

1. Connectez-vous au [portail Microsoft Azure](https://azure.com) et ouvrez la ressource Application Insights pour votre application ou [créez-en une](./create-new-resource.md). 
   
    *Quelle ressource dois-je utiliser ?* Si les applications en cours d’exécution sur votre hôte ont été développées par quelqu’un d’autre, vous devez [créer une ressource Application Insights](./create-new-resource.md). C'est ce qui vous permet d'afficher et d'analyser les données de télémétrie. (Sélectionnez « Général » comme type d’application.)
   
    Toutefois, si vous êtes le développeur des applications, nous espérons que vous avez [ajouté le Kit SDK Application Insights](./java-get-started.md) à chacune d'elles. Si en fait elles sont toutes des composants d'une application d'entreprise unique, vous pouvez toutes les configurer pour envoyer la télémétrie à une ressource, puis vous utiliserez cette même ressource pour afficher les données de performances et du cycle de vie de Docker. 
   
    Un troisième scénario est que vous avez développé la plupart des applications, mais vous utilisez des ressources distinctes pour afficher les données de télémétrie. Dans ce cas, vous pouvez créer une ressource distincte pour les données de Docker.

2. Cliquez sur la liste déroulante **Essentials** et sélectionnez la clé d'instrumentation. Vous l’utilisez pour indiquer au Kit de développement logiciel (SDK) où envoyer ses données de télémétrie.

Ne fermez pas cette fenêtre de navigateur : vous y reviendrez ultérieurement pour consulter vos données de télémétrie.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Exécution de l'analyse Application Insights sur votre hôte

Maintenant que vous avez défini un emplacement pour l'affichage des données de télémétrie, vous pouvez configurer l'application de conteneur qui les recueillera et les enverra.

1. Connectez-vous à votre hôte Docker.
2. Modifiez la clé d'instrumentation par cette commande, puis exécutez-la :
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Une seule image Application Insights est requise par hôte Docker. Si votre application est déployée sur plusieurs hôtes Docker, répétez la commande sur chaque hôte.

## <a name="update-your-app"></a>Mise à jour de votre application
Si votre application est instrumentée avec le [SDK Application Insights pour Java](./java-get-started.md), ajoutez la ligne suivante dans le fichier ApplicationInsights.xml de votre projet, sous l’élément `<TelemetryInitializers>` :

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Ainsi, des informations Docker, telles que le conteneur et l'ID de l'hôte sont ajoutées à chaque élément de télémétrie envoyé à partir de votre application.

## <a name="view-your-telemetry"></a>Affichage de vos données de télémétrie
Revenez à votre ressource Application Insights dans le portail Azure.

Cliquez sur la mosaïque Docker.

Vous verrez bientôt des données arriver à partir de l'application Docker, en particulier si d'autres conteneurs sont en cours d'exécution sur votre moteur Docker.

### <a name="docker-container-events"></a>Événements du conteneur Docker
![Capture d’écran montrant la sélection de Rechercher, une fenêtre de résultats de la recherche de diagnostics avec une flèche pointant d’un événement personnalisé vers une fenêtre avec des données personnalisées.](./media/docker/13.png)

Pour examiner les événements individuels, cliquez sur [Rechercher](./diagnostic-search.md). Utilisez le système de recherche et de filtre pour rechercher des événements particuliers. Cliquez sur un événement pour obtenir plus de détails.

### <a name="exceptions-by-container-name"></a>Exceptions par nom de conteneur
![Capture d’écran montrant la page Metrics Explorer avec un graphique sélectionné et la fenêtre Détails du graphique ouverte sur le côté droit.](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Contexte docker ajouté à la télémétrie d'application
Les données de télémétrie envoyées par l’application instrumentée avec le SDK AI sont enrichies par les informations du contexte Docker.

## <a name="q--a"></a>Questions et réponses
*Quelles sont les fonctionnalités d’Application Insights qui ne sont pas disponibles dans Docker ?*

* Analyse détaillée des compteurs de performances par conteneur et par image.
* Intégration des données de conteneur et d’application dans un tableau de bord.
* [Exportation des données de télémétrie](export-telemetry.md) pour approfondir l'analyse vers une base de données, Power BI ou un autre tableau de bord.

*Comment obtenir des données de télémétrie à partir de l’application elle-même ?*

* Installez le Kit SDK Application Insights dans l’application. Découvrez comment faire pour : les [applications web Java](./java-get-started.md), les [applications web Windows](./asp-net.md).

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Étapes suivantes

* [Application Insights pour Java](./java-get-started.md)
* [Application Insights pour Node.js](./nodejs.md)
* [Application Insights pour ASP.NET](./asp-net.md)

