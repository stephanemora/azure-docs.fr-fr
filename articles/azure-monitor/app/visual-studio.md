---
title: Déboguer dans Visual Studio avec Azure Application Insights
description: Analyse des performances d’application web et diagnostics en phase de débogage et de production.
ms.topic: conceptual
ms.date: 03/17/2017
ms.custom: vs-azure
ms.openlocfilehash: 2507dbf7bb8294c949f434d5fa96ccc0af9a7eb3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563536"
---
# <a name="debug-your-applications-with-azure-application-insights-in-visual-studio"></a>Débogage d’applications à l’aide d’Azure Application Insights dans Visual Studio
Visual Studio 2015 (et versions ultérieures) vous permet d’analyser les performances et de diagnostiquer les problèmes au niveau de votre application web ASP.NET aussi bien en phase de débogage qu’en production, à l’aide des données de télémétrie [d’Azure Application Insights](./app-insights-overview.md).

Si vous avez créé votre application web ASP.NET à l’aide de Visual Studio 2017 ou version ultérieure, celle-ci possède déjà le kit de développement logiciel (SDK) Application Insights. Sinon, si vous ne l’avez pas encore fait, [ajoutez Application Insights à votre application](./asp-net.md).

Pour analyser votre application lorsqu’elle se trouve dans un environnement de production actif, vous affichez normalement les données de télémétrie Application Insights dans le [portail Azure](https://portal.azure.com), où vous pouvez définir des alertes et utiliser des outils d’analyse puissants. Mais pour le débogage, vous pouvez également rechercher et analyser les données de télémétrie dans Visual Studio. Vous pouvez utiliser Visual Studio pour analyser les données de télémétrie à la fois à partir de votre site de production et de votre environnement de débogage sur votre machine de développement. Dans ce cas, vous pouvez analyser les opérations de débogage même si vous n’avez pas encore configuré le kit de développement logiciel pour que les données de télémétrie soient envoyées au portail Azure. 

## <a name="debug-your-project"></a><a name="run"></a> Débogage de votre projet
Exécutez votre application web en mode de débogage local à l’aide de la touche F5. Ouvrez différentes pages pour générer des données de télémétrie.

Dans Visual Studio, vous voyez un décompte des événements qui ont été consignés par le module Application Insights dans votre projet.

![Dans Visual Studio, le bouton Application Insights apparaît pendant le débogage.](./media/visual-studio/appinsights-09eventcount.png)

Cliquez sur ce bouton pour rechercher vos données de télémétrie. 

## <a name="application-insights-search"></a>Recherche Application Insights
La fenêtre de recherche Application Insights affiche les événements qui ont été consignés. (Si vous étiez connecté à Azure au moment de l’installation d’Application Insights, vous pouvez rechercher ces mêmes événements sur le portail Azure.)

![Cliquez avec le bouton droit sur le projet et sélectionnez Application Insights, Rechercher.](./media/visual-studio/34.png)

> [!NOTE] 
> Une fois que vous avez sélectionné ou désélectionné les filtres, cliquez sur le bouton Rechercher en regard du champ de recherche de texte.
>

La recherche en texte libre fonctionne sur tous les champs des événements. Par exemple, vous pouvez effectuer une recherche sur une partie de l’URL d’une page, sur la valeur d’une propriété telle que la ville du client, ou encore sur des termes spécifiques contenus dans un journal de suivi.

Cliquez sur n’importe quel événement pour afficher le détail de ses propriétés.

Pour afficher les requêtes envoyées à votre application web, vous pouvez parcourir le code.

![Sous Request Details (Détails des requêtes), parcourez le code](./media/visual-studio/31.png)

Vous pouvez également ouvrir l’onglet Éléments connexes pour analyser les requêtes ayant échoué ou les exceptions.

![Sous Request Details (Détails des requêtes), accédez à Éléments connexes](./media/visual-studio/41.png)

## <a name="view-exceptions-and-failed-requests"></a>Afficher les exceptions et les requêtes ayant échoué
Les rapports d’exceptions s’affichent dans la fenêtre de recherche. (Pour certains anciens types d’application ASP.NET, vous devez [configurer l’analyse des exceptions](./asp-net-exceptions.md) pour afficher les exceptions gérées par le framework.)

Cliquez sur une exception pour obtenir une trace de pile. Si le code de l’application est ouvert dans Visual Studio, vous pouvez utiliser la trace de pile pour accéder à la ligne de code recherchée.

![Capture d’écran montrant l’objet À propos dans une arborescence des appels de procédure.](./media/visual-studio/17.png)

## <a name="view-request-and-exception-summaries-in-the-code"></a>Afficher les résumés des requêtes et des exceptions dans le code
Le nombre de requêtes et d’exceptions consignées par Application Insights dans les dernières 24 heures est indiqué dans la ligne de filtre Code au-dessus de chaque méthode de gestionnaire.

![Capture d’écran montrant une exception dans une boîte de dialogue contextuelle.](./media/visual-studio/21.png)

> [!NOTE] 
> Le filtre Code affiche les données Application Insights uniquement si vous avez [configuré votre application pour que les données de télémétrie soient envoyées au portail Application Insights](./asp-net.md).
>

[En savoir plus sur Application Insights dans le filtre Code](./visual-studio-codelens.md)

## <a name="local-monitoring"></a>Surveillance locale
(À partir de Visual Studio 2015 Mise à jour 2) Si vous n’avez pas configuré le Kit de développement logiciel pour envoyer les données de télémétrie au portail Application Insights (et qu’il n’existe donc aucune clé d’instrumentation dans ApplicationInsights.config), la fenêtre de diagnostic affiche les données de télémétrie de votre dernière session de débogage. 

C’est le comportement adéquat si vous avez déjà publié une version antérieure de votre application. Vous ne voulez pas que les données de télémétrie de vos sessions de débogage soient confondues avec les données de télémétrie sur le portail Application Insights de l’application publiée.

Cela est également utile si vous disposez de [données de télémétrie personnalisées](./api-custom-events-metrics.md) que vous souhaitez déboguer avant de les envoyer sur le portail.

* *Dans un premier temps, j’ai entièrement configuré Application Insights pour envoyer des données de télémétrie au portail. À présent, j’aimerais afficher ces données uniquement dans Visual Studio.*
  
  * Dans les paramètres de la fenêtre de recherche, il existe une option permettant de rechercher des diagnostics locaux même si votre application envoie des données de télémétrie au portail.
  * Pour arrêter l’envoi de données de télémétrie au portail, commentez la ligne `<instrumentationkey>...` du fichier ApplicationInsights.config. Lorsque vous êtes prêt à envoyer de nouveau les données de télémétrie au portail, supprimez les commentaires.


## <a name="next-steps"></a>Étapes suivantes

 * **[Utilisation du portail Application Insights](./overview-dashboard.md)** Affichez les tableaux de bord, les puissants outils de diagnostic et d’analyse, les alertes, le mappage direct des dépendances de votre application et les données de télémétrie exportées. 

