---
title: Utilisation de la recherche dans Azure Application Insights | Microsoft Docs
description: Recherchez et filtrez la télémétrie brute envoyée par votre application web.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mbullwin
ms.openlocfilehash: a9c9ac9ada6dfab051deda14cddb4ba6c07f48df
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998895"
---
# <a name="using-search-in-application-insights"></a>Utilisation de la recherche dans Application Insights
Recherche (Search) est la fonctionnalité [d’Application Insights](../../application-insights/app-insights-overview.md) qui vous permet de rechercher et d’explorer les éléments de télémétrie, par exemple des pages vues, des exceptions ou des requêtes web. Vous pouvez également afficher le suivi et les événements de journal que vous avez codés.

(Pour les requêtes plus complexes sur vos données, utilisez [Analytics](../../azure-monitor/log-query/get-started-portal.md).)

## <a name="where-do-you-see-search"></a>Où voyez-vous Recherche ?

### <a name="in-the-azure-portal"></a>Dans le portail Azure

Vous pouvez ouvrir explicitement la recherche de diagnostics à partir du panneau Vue d’ensemble d’Application Insights de votre application :

![Open diagnostic search](./media/diagnostic-search/001.png)

![Capture d’écran de graphiques de recherche de diagnostic](./media/diagnostic-search/002.png)

La partie principale de Recherche de diagnostic est une liste d’éléments de télémétrie : demandes serveur, pages vues, événements personnalisés que vous avez codés, et ainsi de suite. En haut de la liste se trouve un graphique de synthèse indiquant le nombre d’événements au fil du temps.

Cliquez sur Actualiser pour obtenir les nouveaux événements.

### <a name="in-visual-studio"></a>Dans Visual Studio

Dans Visual Studio, il existe également une fenêtre de recherche Application Insights. Elle est particulièrement utile pour l’affichage des événements de télémétrie générés par l’application que vous déboguez. Mais elle peut également afficher les événements collectés à partir de votre application publiée sur le portail Azure.

Ouvrez la fenêtre de recherche dans Visual Studio :

![Recherche Application Insights dans Visual Studio](./media/diagnostic-search/32.png)

La fenêtre de recherche comporte les mêmes fonctionnalités que le portail web :

![Fenêtre de recherche Visual Studio Application Insights](./media/diagnostic-search/34.png)

L’onglet Suivi des opérations est disponible lorsque vous ouvrez une requête ou un affichage de page. Une 'Opération' est une séquence d’événements qui est associée à une demande ou un affichage de page unique. Par exemple, les appels de dépendance, les exceptions, les journaux de suivi et les événements personnalisés peuvent faire partie d’une opération unique. L’onglet Suivi des opérations représente graphiquement la chronologie et la durée de ces événements par rapport à la demande ou à l’affichage de page. 

## <a name="inspect-individual-items"></a>Inspecter les éléments un par un

Sélectionnez un élément de télémétrie pour afficher les champs clés et les éléments associés.

![Capture d’écran d’une demande de dépendance individuelle](./media/diagnostic-search/003.png)

Cette action lance la vue Détails de la transaction de bout en bout :

![Capture d’écran de la vue Détails de la transaction de bout en bout.](./media/diagnostic-search/004.png)

## <a name="filter-event-types"></a>Filtrer les types d’événement
Ouvrez le panneau Filtre et choisissez les types d’événement que vous souhaitez afficher. (Si vous souhaitez restaurer plus tard les filtres avec lesquels vous avez ouvert le panneau, cliquez sur Réinitialiser).

![Choisissez le filtre et sélectionnez les types de télémétrie](./media/diagnostic-search/02-filter-req.png)

Les types d'événements sont :

* **Suivi** - [Les journaux de diagnostic](../../azure-monitor/app/asp-net-trace-logs.md) comprennent les appels TrackTrace, log4Net, NLog et System.Diagnostic.Trace.
* **Demandes** : demandes HTTP reçues par votre serveur d’applications, dont les pages, les scripts, les images, les fichiers de style et les données. Ces événements sont utilisés pour créer les graphiques de présentation de la demande et la réponse.
* **Affichage de page** - [Télémétrie envoyée par le client web](../../azure-monitor/app/javascript.md) et utilisée pour créer les rapports d’affichage des pages. 
* **Événement personnalisé** : si vous avez inséré des appels vers TrackEvent() pour [surveiller l’utilisation](../../azure-monitor/app/api-custom-events-metrics.md), vous pouvez les rechercher ici.
* **Exception** : [exceptions non interceptées sur le serveur](../../azure-monitor/app/asp-net-exceptions.md) et celles que vous enregistrez avec TrackException().
* **Dépendance** - [Appels de votre application de serveur ](../../azure-monitor/app/asp-net-dependencies.md) à d’autres services tels que des API REST ou des bases de données, et appels AJAX à partir de votre [code client](../../azure-monitor/app/javascript.md).
* **Disponibilité** : résultats des [tests de disponibilité](../../azure-monitor/app/monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrer des valeurs de propriétés
Vous pouvez filtrer les événements en fonction des valeurs de leurs propriétés. Les propriétés disponibles varient en fonction des types d’événement que vous avez sélectionnés. 

Par exemple, les demandes avec un code de réponse spécifique. 

![Développez une propriété et choisissez une valeur](./media/diagnostic-search/03-response500.png)

Si vous ne choisissez aucune valeur pour une propriété, cela a le même effet que si vous sélectionniez toutes les valeurs. Cela désactive le filtrage sur cette propriété.

### <a name="narrow-your-search"></a>Affiner votre recherche
Notez que les nombres à droite des valeurs de filtre affichent le nombre d’occurrences dans le jeu actuellement filtré. 

Dans cet exemple, il est clair que la demande 'Rpt/Employees' provoque la majorité des erreurs '500' :

![Développez une propriété et choisissez une valeur](./media/diagnostic-search/04-failingReq.png)

## <a name="find-events-with-the-same-property"></a>Rechercher des événements avec la même propriété
Recherchez tous les éléments dont la valeur de la propriété est la même :

![Cliquez avec le bouton droit sur une propriété](./media/diagnostic-search/12-samevalue.png)

## <a name="search-the-data"></a>Recherche dans les données

> [!NOTE]
> Pour écrire des requêtes plus complexes, ouvrez [**Analytics**](../../azure-monitor/log-query/get-started-portal.md) à partir du haut du panneau Recherche.
> 

Vous pouvez rechercher des termes dans une des valeurs des propriétés. Cela est particulièrement utile si vous avez écrit des [événements personnalisés](../../azure-monitor/app/api-custom-events-metrics.md) avec des valeurs de propriété. 

Vous pouvez définir une durée, car les recherches sur les plages courtes sont plus rapides. 

![Open diagnostic search](./media/diagnostic-search/appinsights-311search.png)

Recherchez des mots entiers, pas des sous-chaînes. Utilisez des guillemets pour délimiter les caractères spéciaux.

| chaîne | n’est *pas* trouvé par | mais ceux-ci la trouvent |
| --- | --- | --- |
| HomeController.About |home<br/>contrôleur<br/>out | homecontroller<br/>about<br/>« homecontroller.about »|
|États-Unis|Uni<br/>ted|états<br/>unis<br/>états ET unis<br/>« états-unis »

Expressions de recherche utilisables :

| Exemple de requête | Résultat |
| --- | --- |
| `apple` |Trouve tous les événements dont la période comprend le mot « apple » |
| `apple AND banana` <br/>`apple banana` |Trouve les événements qui contiennent les deux mots. Utilisez « AND » en lettres majuscules (et non « and » en lettres minuscules). <br/>Forme abrégée. |
| `apple OR banana` |Trouve les événements qui contiennent un des deux mots. Utilisez « OR » en lettres capitales (et non « or » en lettres minuscules). |
| `apple NOT banana` |Trouve les événements qui contiennent un mot, mais pas l’autre. |

## <a name="sampling"></a>échantillonnage
Si votre application génère un volume important de télémétrie (et si vous utilisez le Kit SDK ASP.NET version 2.0.0-beta3 ou ultérieure), le module d'échantillonnage adaptatif réduit automatiquement le volume qui est envoyé vers le portail en envoyant uniquement une fraction représentative des événements. Cependant, les événements liés à la même demande sont activés ou désactivés en tant que groupe, afin que vous puissiez naviguer entre les événements connexes. 

[En savoir plus sur l'échantillonnage](../../application-insights/app-insights-sampling.md).

## <a name="create-work-item"></a>Création d’un élément de travail
Vous pouvez créer un bogue dans GitHub ou Azure DevOps avec les détails d’un élément de télémétrie. 

![Cliquez sur Nouvel élément de travail, modifiez les champs, puis cliquez sur OK.](./media/diagnostic-search/42.png)

La première fois que vous procédez ainsi, vous êtes invité à configurer un lien vers votre organisation et votre projet Azure DevOps.

![Indiquez l’URL de vos services Azure DevOps Services et le nom du projet, puis cliquez sur Autoriser](./media/diagnostic-search/41.png)

(Vous pouvez également configurer le lien dans le panneau des éléments de travail).

## <a name="send-more-telemetry-to-application-insights"></a>Envoyer plus de télémétrie à Application Insights
En plus de la télémétrie fournie par le Kit de développement logiciel (SDK) Application Insights, vous pouvez :

* Capturer le suivi du journal dans votre infrastructure de journalisation favorite dans [.NET](../../azure-monitor/app/asp-net-trace-logs.md) ou [Java](../../azure-monitor/app/java-trace-logs.md). Cela signifie que vous pouvez effectuer des recherches dans le suivi du journal et les mettre en corrélation avec les pages vues, les exceptions et autres événements. 
* [Écrire du code](../../azure-monitor/app/api-custom-events-metrics.md) pour envoyer des événements personnalisés, des affichages de page et des exceptions. 

[Découvrez comment envoyer les journaux et la télémétrie personnalisée à Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).

## <a name="questions"></a>Questions et réponses
### <a name="limits"></a>Quelle est la quantité de données conservée ?

Voir la section [Synthèse des limites](../../azure-monitor/app/pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Comment puis-je consulter les données POST dans mes demandes serveur ?
Nous n’enregistrons pas automatiquement les données POST, mais vous pouvez utiliser [TrackTrace ou le journal des appels](../../azure-monitor/app/asp-net-trace-logs.md). Placez les données POST dans le paramètre de message. Vous ne pouvez pas filtrer les messages comme vous le feriez pour les propriétés, mais la limite de taille est plus importante.

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>Étapes suivantes
* [Écrire des requêtes complexes dans Analytics](../../azure-monitor/log-query/get-started-portal.md)
* [Envoi des journaux et de la télémétrie personnalisée à Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
* [Configuration des tests de disponibilité et de réactivité](../../azure-monitor/app/monitor-web-app-availability.md)
* [Dépannage](../../application-insights/app-insights-troubleshoot-faq.md)
