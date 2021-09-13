---
title: Analyser la conservation utilisateur de l’application web avec Application Insights
description: Combien d’utilisateurs reviennent vers votre application ?
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 07/30/2021
ms.openlocfilehash: de83c898d7a471892f61fa299cdf2091a1f228e7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532205"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Analyse de la rétention utilisateur des applications web avec Azure Application Insights

La fonctionnalité de conservation dans [Application Insights](./app-insights-overview.md) vous aide à analyser le nombre d’utilisateurs qui reviennent vers votre application et la fréquence à laquelle ils exécutent des tâches particulières ou atteignent des objectifs. Par exemple, si vous exécutez un site de jeu, vous pouvez comparer le nombre d’utilisateurs qui reviennent sur le site après avoir perdu à un jeu avec le nombre d’utilisateurs qui reviennent après avoir gagné. Cette information peut vous aider à améliorer l’expérience de vos utilisateurs et votre stratégie commerciale.

## <a name="get-started"></a>Bien démarrer

Si aucune donnée n’apparaît dans l’outil de rétention du portail Application Insights, [découvrez comment prendre en main les outils d’utilisation](usage-overview.md).

## <a name="the-retention-workbook"></a>Classeur de conservation

Pour utiliser le classeur de conservation, dans vos ressources Application Insights, accédez à **Utilisation** > **Conservation** et sélectionnez **Classeur d’analyse de conservation**. Ou sous l’onglet **Classeurs**, sélectionnez **Modèles publics**, puis **Analyse de conservation utilisateur** sous *Utilisation*.

:::image type="content" source="./media/usage-retention/workbooks-gallery.png" alt-text="Capture d’écran de la galerie de classeurs sur les modèles publics" lightbox="./media/usage-retention/workbooks-gallery.png":::



### <a name="using-the-workbook"></a>Utilisation du classeur

:::image type="content" source="./media/usage-retention/retention.png" alt-text="Capture d’écran du classeur de rétention montrant un graphique en courbes" lightbox="./media/usage-retention/retention.png":::

- Par défaut, la rétention affiche tous les utilisateurs qui ont effectué une action, puis sont revenus et ont effectué une autre action sur une période donnée. Vous pouvez choisir une combinaison différente d’événements afin de filtrer des activités utilisateur particulières.
- Ajoutez un ou plusieurs filtres sur les propriétés en sélectionnant **Ajouter des filtres**. Par exemple, vous pouvez cibler les utilisateurs d’un pays ou d’une région spécifique. 
- Le graphique de rétention globale récapitule la rétention utilisateur sur la période sélectionnée. 
- La grille affiche le nombre d’utilisateurs retenus. Chaque ligne représente une cohorte d’utilisateurs ayant effectué l’un des événements pendant la période de temps indiquée. Chaque cellule de la ligne indique combien de cette cohorte sont revenus au moins une fois pendant une période ultérieure. Certains utilisateurs peuvent revenir pendant plusieurs périodes. 
- Les cartes d’aperçu affichent les cinq principaux événements de lancement et les cinq principaux événements renvoyés pour aider les utilisateurs à mieux comprendre leur rapport de rétention. 

    :::image type="content" source="./media/usage-retention/retention-2.png" alt-text="Capture d’écran du classeur de conservation, montrant le retour de l’utilisateur après le graphique # de semaines" lightbox="./media/usage-retention/retention-2.png":::

## <a name="use-business-events-to-track-retention"></a>Utiliser des événements commerciaux pour suivre la rétention

Pour obtenir l’analyse de rétention la plus utile, mesurez les événements qui représentent des activités commerciales significatives. 

Par exemple, un grand nombre d’utilisateurs peuvent ouvrir une page dans votre application sans jouer au jeu qu’elle affiche. Le suivi des vues de pages uniquement fournit ainsi une estimation inexacte du nombre de personnes qui reviennent pour jouer au jeu auquel ont déjà joué. Pour obtenir une vision claire des lecteurs qui reviennent, votre application doit envoyer un événement personnalisé lorsqu’un utilisateur joue réellement.  

Il est recommandé de coder les événements personnalisés qui représentent des actions commerciales clés et de les utiliser pour votre analyse de rétention. Pour capturer le résultat du jeu, vous devez écrire une ligne de code pour envoyer un événement personnalisé à Application Insights. Si vous l’écrivez dans le code de page web ou en Node.JS, voici à quoi il ressemble :

```JavaScript
    appinsights.trackEvent("won game");
```

Ou dans le code de serveur ASP.NET :

```csharp
   telemetry.TrackEvent("won game");
```

[Familiarisez-vous avec l’écriture d’événements personnalisés](./api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Étapes suivantes
- - Pour en savoir plus sur les classeurs, consultez [la vue d’ensemble des classeurs](../visualize/workbooks-overview.md).
- Pour activer les expériences d’utilisation, commencez à envoyer des [événements personnalisés](./api-custom-events-metrics.md#trackevent) ou des [affichages de page](./api-custom-events-metrics.md#page-views).
- Si vous envoyez déjà des événements personnalisés ou des affichages de page, explorez les outils d’utilisation pour savoir comment les utilisateurs emploient votre service.
    - [Utilisateurs, sessions, événements](usage-segmentation.md)
    - [Entonnoirs](usage-funnels.md)
    - [Flux d’utilisateurs](usage-flows.md)
    - [Classeurs](../visualize/workbooks-overview.md)
    - [Ajouter du contexte utilisateur](./usage-overview.md)